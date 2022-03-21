<template>
	<view>
		<page :parentData="data" :formAction="formAction" ref="page"></page>
		<view class="pb60" v-if="show">
			<view id="address" class="order-sec plr10 mb0" v-if="ruleform.shipping !=1">
				<block v-if="!address">
					<view class="add-add ptb15" @click="createAddress">
						<view class="add-icon iconfont icon-count-plus mr10"></view>
						<view class="add-txt fs-16">新增收货地址</view>
						<view class="iconfont icon-right fs-12 fc-9"></view>
					</view>
				</block>
				<block v-else>
					<view class="add-info fs-17" @click="addressLists">
						<view class="licon pr15">
							<view class="iconfont icon-location fs-22 main-color"></view>
						</view>
						<view class="info ptb10">
							<view class="name">
								<text>{{address.name}}</text>
								<text class="Arial pl10">{{address.phone}}</text>
							</view>
							<view class="add-detail fc-6">
								<view class="label">地址：</view>
								<view class="name">{{ address.province }} {{ address.city }} {{ address.area }} {{ address.address }}</view>
							</view>
						</view>
						<view class="iconfont icon-right fs-12 fc-9 pl15"></view>
					</view>
				</block>
			</view>
			<view class="pay_line bg-f mb12" v-if="ruleform.shipping !=1">
				<image class="flex" src="https://boss.doxinsoft.com/images/site/share-tip.png"></image>
			</view>
			<view class="pro-info order-sec" v-for="item in ruleform.getProduct">
				<view class="cart-tb">
					<view class="pro-img pr10">
						<image class="img" :src="item.getProduct.firstCover" />
					</view>
					<view class="pro-name">
						<view class="name w-b100 lh-20 fs-15 wrap2">{{item.getProduct.name}}</view>
						<view class="group w-b100 fc-4">
							<!-- <view class="num fs-12">数量：<text class="Arial">{{item.num}}</text></view> -->
							<view class="num fs-14">水票数量：<text class="Arial">{{item.left_num}}</text>桶</view>
						</view>
					</view>
				</view>
			</view>
			<view id="mode" class="order-sec">
				<weui-input v-model="ruleform.pay_method" name="pay_method" datatype="require" label="付款方式" changeField="value" type="select"
				 dataKey="pay_methods" :disabled=" ruleform.status>=3?true:false" v-if="ruleform.fclass!=109&&ruleform.fclass!=110"></weui-input>
				 <weui-input v-model.lazy="ruleform.myAccount" :disabled="true" label="余额" type="text" name="name" v-if="ruleform.pay_method == 2"></weui-input>
				<weui-input v-model="ruleform.remark" label="买家留言" type="text" name="remark" placeholder="点击给商家留言"></weui-input>
			</view>
			<view id="footer" v-if="ruleform.status == 1">
				<view class="f_left price fs-18 plr10 ">合计：{{ amount }}</view>
				<myform class="flex1" :ruleform="ruleform" :vaildate="vaildate" @callBack="submit" myclass="f_right plr20 m0" title="提交订单"></myform>
			</view>
			<view id="footer" v-else-if="ruleform.status == 3 || ruleform.status == 5">
				<view class="f_left"></view>
				<view class="f_right plr20" @click="canReceipt">确认收货</view>
			</view>
			<view id="footer" class="bd-t" v-else>
				<view class="f_left"></view>
				<view class="f_right plr20">完成</view>
			</view>
		</view>
	</view>
</template>
<script>
	import orderPro from "@/components/orderPro";
	export default {
		components:{orderPro},
		data() {
			return {
				formAction: '/shop/package/order-detail',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				show:false,
				getSiteName: this.getSiteName(),
				shipping: [
					{label: '门店自提',value: 1},
					{label: '送货上门',value: 2},
				],
				pay_methods: [
					{value: 1,label: '微信支付'},
					{value: 2,label: '余额支付'}
				],
				ruleform: {},
				vaildate: {},
				actIndex: 0,
				order_no: 0,
				address_id: 0,
				address: {},
				coupons: []
			}
		},
		computed: {
			amount() {
				//如果是微信支付
				let coupon_value = parseFloat(this.ruleform.coupon_value);
				this.ruleform.amount = parseFloat(this.ruleform.amount);
				let order_amount = this.ruleform.amount;
				if (this.ruleform.shipping == 2) {
					order_amount += parseFloat(this.ruleform.yunfei);
				}
				/*如果有优惠券*/
				if (coupon_value) {
					order_amount -= coupon_value;
				}
				/*如果余额支付*/
				if (this.ruleform.pay_method == 2) {
					order_amount -= this.ruleform.myAccount;
				}

				return order_amount < 0 ? 0 : order_amount.toFixed(2);
			},
			wallet() {
				let coupon_value = parseFloat(this.ruleform.coupon_value);
				this.ruleform.amount = parseFloat(this.ruleform.amount);
				let order_amount = this.ruleform.amount;
				if (this.ruleform.shipping == 2) {
					order_amount += parseFloat(this.ruleform.yunfei);
				}
				if (coupon_value) {
					order_amount -= coupon_value;
				}
				/*如果余额支付*/
				if (this.ruleform.pay_method == 2) {
					let res = this.ruleform.myAccount - order_amount;
					if (res > 0) {
						return order_amount;
					} else {
						return this.ruleform.myAccount;
					}
					return order_amount;
				} else {
					return 0;
				}
			}
		},
		onReachBottom() {
			this.hasMore(this);
		},
		onPullDownRefresh() {
			this.data.nextPage = 1;
			this.ajax();
		},
		onShareAppMessage() {
			this.shareSource(this, '商城');
		},
		onLoad(options) {
			wx.removeStorageSync('package');
			this.vaildate = {};
			if (options.order_no) {
				this.ruleform.order_no = options.order_no;
				this.order_no = options.order_no;
			}
			if (options.address_id) {
				this.address_id = options.address_id;
			}
			if (options.merchant_user_id) {
				this.ruleform.merchant_user_id = options.merchant_user_id;
				this.ruleform.merchant_user_name = options.merchant_user_name;
			} else {
				this.ajax();
			}
		},
		methods: {
			selectCallBack(item) {
				if (item.value == 2) {
					delete this.vaildate.merchant_user_id;
				}
			},
			chooseMerchant() {
				for (var i = 0; i < this.ruleform.products.length; i++) {
					let v = this.ruleform.products[i];
					if (!v.getProduct.cart) {
						this.goto('/pages/business/choose/main?product_id=' + v.getProduct.id, 1);
						return false;
					}
				}
				this.goto('/pages/business/choose/main', 1);
			},
			ajax() {
				this.show = false;
				this.getAjax(this).then(msg => {
					this.address = msg.address;
					this.ruleform = msg.detail;
					this.ruleform.myAccount = msg.user.amount;
					this.ruleform.coupon_value = this.ruleform.coupon_value != "0.00" ? this.ruleform.coupon_value : '';
					this.ruleform.addr_name = msg.user.userInfo.name;
					this.ruleform.addr_phone = msg.user.userInfo.phone;
					this.ruleform.addr_address = msg.user.userInfo.address;
					this.$set(this.ruleform, "provinces", [0, 0]);
					this.coupons = this.data.coupons;
					this.show = true;
				});
			},
			createAddress() {
				if (this.ruleform.status >= 3) {
					return false;
				}
				uni.setStorageSync('order_no', this.ruleform.order_no);
				return this.goto("/pages/user/address/create_edit/main", 1);;
				this.createAddressUniapp(res => {
					if (res) {
						this.ajax();
					} else {
						this.goto("/pages/user/address/create_edit/index", 1);
					}
				});
			},
			submit(e) {
				let ruleform = this.ruleform;
				ruleform.area = uni.getStorageSync("waterCityData").area;
				if (ruleform.shipping != 1 && ruleform.fclass != 84) {
					if (!this.address || !this.address.id) {
						this.getError("请填写收货地址");
						setTimeout(() => {
							this.createAddress();
						}, 500)
						return false;
					}
					ruleform.address_id = this.address.id;
				}
				if (ruleform.coupon_value) {
					this.data.coupons.forEach((v, i) => {
						if (v.checked) {
							this.ruleform.coupon_userid = v.id;
						}
					});
				}

				this.postAjax(this.formAction, ruleform).then(msg => {
					if (msg.data.status == 2) {
						if (this.amount == 0) {
							this.goto('/pages/package/order/payed/main?order_no=' + ruleform.order_no);
							return false;
						}
						this.goto("/pages/package/order/pay_center/main?order_no=" + ruleform.order_no);
					}
				});;

			},
			addressLists() {
				if (this.ruleform.status >= 3) {
					return false;
				}
				uni.setStorageSync('order_no', this.ruleform.order_no);
				this.goto('/pages/user/address/lists/main?order_no=' + this.ruleform.order_no);
			},
			canReceipt() {
				this.receipt("确认收货吗");
			},
			radioChange: function(event) {
				this.ruleform.pay_method = event.mp.detail.value;
			},
			receipt(title) {
				this.getConfirm(title, msg => {
					this.postAjax("/shop/user/change-order-status", this.ruleform).then(msg => {
						if (msg.data.status == 2) {
							this.goto("/pages/order/lists/index?historyUrl=del&status=" + msg.data.detail.status);
						}
					});
				});

			}
		}
	}
</script>
<style>
@import url("index.css");
@import url("xiaozhu/css/dx-input.css");
</style>