<template>
	<view class="pb60">
		<page :parentData="data" :formAction="formAction" ref="page"></page>
		
		<view v-if="data.show">
			<!-- 普通订单 -->
			<!-- <view v-if="ruleform.staff_userid" class="delivery bg-f flex-center flex-middle p15 mb10" @click="goto('/pages/order/map/main?order_no='+ruleform.order_no,1)">
				<view class="iconfont icon-order-delivery fc-blue fs-24"></view>
				<view class="name fs-16 pl10">配送员位置</view>
			</view> -->
			<view>
				<view id="address" class="block-sec plr10 mb0" v-if="ruleform.shipping != 1">
					<block v-if="!address">
						<view class="add-add ptb15" @click="createAddress">
							<view class="add-icon iconfont icon-count-plus mr10"></view>
							<view class="add-txt fs-16">新增收货地址</view>
							<view class="iconfont icon-right fs-12 fc-9"></view>
						</view>
					</block>
					<block v-else>
						<view class="add-info" @click="addressLists" v-if="address.name">
							<view class="licon pr15">
								<text class="iconfont icon-location fs-22 main-color"></text>
							</view>
							<view class="info ptb10">
								<view class="name fs-16">
									<text>{{address.name}}</text>
									<text class="Arial pl10">{{address.phone}}</text>
								</view>
								<view class="add-detail fs-15 fc-6">
									<view class="label">地址：</view>
									<view class="name">{{ address.province }} {{ address.city }} {{ address.area }} {{ address.address }}</view>
								</view>
							</view>
							<view class="iconfont icon-right fs-12 fc-9 pl15"></view>
						</view>
					</block>
					<view class="pay_line">
						<image class="img" :src="getSiteName+'/images/wap/share-tip.png'"></image>
					</view>
				</view>
				<view class="block-sec">
					<orderPro :data="ruleform.products" myclass="plr15" url="/pages/product/show/main?id=" isBuy></orderPro>
				</view>
				<view class="block-sec write" v-if="ruleform.shipping == 1">
					<view class="plr15 pt20 pb10 lh-1 fs-17 fc-0">联系信息</view>
					<weui-input v-model="ruleform.addr_name" label="姓名" type="text" name="addr_name" datatype="require"></weui-input>
					<weui-input v-model="ruleform.addr_phone" label="电话" type="text" name="addr_phone" datatype="require|phone"></weui-input>
				</view>
				<view class="block-sec">
					<weui-input v-model="ruleform.pay_method" name="pay_method" datatype="require" label="付款方式" changeField="value"
					 type="select" dataKey="pay_methods"  v-if="ruleform.fclass != 109 &&ruleform.fclass != 110"></weui-input>
					<weui-input v-model="ruleform.getPayMethod" name="pay_method" datatype="require" label="付款方式" changeField="value"
					 type="select" :disabled="ruleform.status >=3 ? true :false" v-if="ruleform.fclass == 109 || ruleform.fclass == 110"></weui-input>
					<weui-input v-model.lazy="ruleform.myAccount" :disabled="true" label="余额" type="text" name="name" v-if="ruleform.pay_method == 2"></weui-input>
					<weui-input v-model="ruleform.remark" label="买家留言" type="text" name="remark" placeholder="点击给商家留言"></weui-input>

				</view>
				<view id="calculation" class="block-sec">
					<view class="list-group">
						<view class="txt">商品金额</view>
						<view class="fs-16 price">￥{{ruleform.amount}}</view>
					</view>
					<view class="list-group" v-if="ruleform.shipping == 2">
						<view class="txt">运费</view>
						<view class="fs-16 price">+ ￥{{ ruleform.yunfei }}</view>
					</view>
					<!-- <view class="list-group" v-if="ruleform.coupon_value">
						<view class="txt">优惠券</view>
						<view class="fs-16 price">- ￥{{ ruleform.coupon_value }}</view>
					</view>
					<view class="list-group">
						<view class="txt">余额</view>
						<view class="fs-16 price">- ￥{{ wallet}}</view>
					</view> -->
				</view>				
				<view id="footer" v-if="ruleform.status == 1">
					<view class="f_left plr10">
						<view class="txt">合计：</view>
						<view class="price fw-bold">
							￥<text class="fs-24">{{amount}}</text>
						</view>
					</view>
					<view class="f_right">
						<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="submit" myclass="nav" title="提交订单"></myform>
					</view>
				</view>
				<view id="footer" v-else-if="ruleform.status == 5">
					<view class="f_left"></view>
					<view class="f_right flex-middle mlr5"><view class="nav" @click="canReceipt">确认收货</view></view>
				</view>
				<view id="footer" class="bd-t" v-else>
					<view class="f_left"></view>
					<view class="f_right flex-middle mlr5"><view class="nav">{{ruleform.status_message}}</view></view>
				</view>
				
			</view>
			<!-- 普通订单结束 -->
			
		</view>
		<view v-else-if="data.show && ruleform.fclass ==106 && ruleform.dianxin == 0">
			<coupon :ruleform="ruleform" :data="data" :amount="amount" :vaildate="vaildate"></coupon>
		</view>
	</view>
</template>
<script>
	import dxAddress from "xiaozhu/uniapp/components/addressAndCity";
	import other from "./layouts/other";
	import coupon from "./layouts/coupon";
	import uParse from '@/components/gaoyia-parse/parse.vue'
	import orderPro from "@/components/orderPro";
	export default {
		components: {other,coupon,uParse,orderPro,dxAddress},
		data() {
			return {
				formAction: '/shop/order/detail',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				maskCheck:false,
				value: 1,
				shipping: [
					{label: '自提',value: 1},
					{label: '送货上门',value: 2},
				],
				
				pay_methods: [
					{label: '微信支付',value: 1},
					 {label: '余额支付',value: 2},
				],
				ruleform: {},
				vaildate: {},
				ps_timeArr: [],
				actIndex: 0,
				order_no: 0,
				address_id: 0,
				address: {},
				coupons: [],
				openNotice: false,
				isMoonCakeCoupon:false,
			}
		},
		computed: {
			exchangeBucket(){
				let res = 0 ;
				if(this.ruleform.bucket_type== '回桶' &&this.ruleform.bucket_value == '其他品牌'){
					//res+=10 * parseInt(this.ruleform.bucket_num);
					res+=0;
				}else if(this.ruleform.bucket_type== '押桶' ){
					  res+= 50 * parseInt(this.ruleform.num);;
				}else if(this.ruleform.bucket_type == '买桶'){
					  res+= 15 * parseInt(this.ruleform.num);;
				}
				return res;
			},
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
				if(this.exchangeBucket >0){
					order_amount+= parseFloat(this.exchangeBucket);
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
				if(options.deliver_type_)this.ruleform.deliver_type_ = options.deliver_type_;
			} else {
				this.ajax();
			}
		},
		onShow(){
			this.ruleform.bucket_type = "";
		},
		methods: {
			bucketCallBack(){
				
				
			},
			changeMask(){
				this.maskCheck = !this.maskCheck;
			},
			changeOrder(item) {
				let title = item.status == 1 ? "您确认要取消订单吗？" : "您确认要申请售后吗？订单金额24小时内原路退回";
				this.getConfirm(title, () => {
					if(item.status == 1){
						this.postAjax("/ajax/mydel",{tablename:'orders',id:item.id}).then(msg=>{
							if (msg.data.status == 2) {
								this.data.nextPage = 1;
								this.ajax();
							}
						})
					}else{
						this.postAjax("/user/change-order-status", {
							id: item.id,
							status: 0
						}).then(msg => {
							if (msg.data.status == 2) {
								this.data.nextPage = 1;
								this.ajax();
							}
						});
					}
					
				});
			},
			couponCallBack(res) {
				this.ruleform.coupon_userid = res.id;
			},
			selectCallBack(item) {
				if (item.value == 2) {
					delete this.vaildate.merchant_user_id;
				}
			},
			chooseMerchant() {
				if (this.ruleform.type == 0) {
					this.goto('/pages/business/choose/main?order_no=' + this.ruleform.order_no + '&type=' + this.ruleform.type, 1);
				} else {
					this.goto('/pages/ztd/choose/main?order_no=' + this.ruleform.order_no + '&type=' + this.ruleform.type+'&shipping='+this.ruleform.shipping, 1);
				}
			},
			text(){
				this.ruleform.merchant_user_id = "";
				this.ruleform.merchant_user_name = "";
			    this.ruleform.deliver_type_ ="";
			},
			ajax() {
				this.getAjax(this).then(msg => {
					this.address = msg.address;
					this.ruleform = msg.detail;
					this.ruleform.myAccount = msg.user.amount;
					this.ruleform.coupon_value = this.ruleform.coupon_value != "0.00" ? this.ruleform.coupon_value : '';
					
					if(this.ruleform.fclass !=238){
						this.ruleform.addr_name = msg.user.userInfo.name;
						this.ruleform.addr_phone = msg.user.userInfo.phone;
						this.ruleform.addr_address = msg.user.userInfo.address;
					}
					if(this.ruleform.status !=1){
						this.maskCheck = true;	
					}
					this.coupons = this.data.coupons;
					if(this.coupons.length){
						this.coupons.forEach(v=>{
							if(v.wechat_card_id){
								this.isMoonCakeCoupon = true;
							}
						});
					}
					if( this.isMoonCakeCoupon && msg.coupons.length){
						this.ruleform.deliver_name = '';	
						this.ruleform.coupon_userid = msg.coupons[0].id;
						this.ruleform.coupon_value = msg.coupons[0].value;
					}else{
						this.ruleform.deliver_name = '送货上门（满50元免配送费）';
					}
					
					
					this.$set(this.ruleform, "provinces", [0, 0]);
					
					if (this.ruleform.merchant_user_id && !this.ruleform.merchant_user_name) {
						this.ruleform.merchant_user_name = this.ruleform.merchantName.company_name;
					}
					if (this.ruleform.type == 1) {
						let ztd = uni.getStorageSync('ztd');
						if (ztd) {
							this.ruleform.merchant_user_id = ztd.id;
							this.ruleform.merchant_user_name = ztd.userInfo.company_name;
							this.ruleform.deliver_type_ = ztd.deliver_type_;
						}

						let hour = new Date().getHours();
						this.ps_timeArr = [];
						if (hour < 11) {
							// this.ps_timeArr.push({
							// 	label: '下午五点',
							// 	value: '下午五点'
							// });
							this.ps_timeArr.push({
								label: '明天上午11点',
								value: '明天上午11点'
							});
							this.ps_timeArr.push({
								label: '明天下午5点',
								value: '明天下午5点'
							});
							if (!this.ruleform.ps_time) {
								this.ruleform.ps_time = "";
							}
						} else {
							this.ps_timeArr.push({
								label: '明天上午11点',
								value: '明天上午11点'
							});
							this.ps_timeArr.push({
								label: '明天下午5点',
								value: '明天下午5点'
							});
							if (!this.ruleform.ps_time) {
								this.ruleform.ps_time = "";
							}
						}

					}
					console.log('q001');
					this.ruleform.bucket_num = this.ruleform.num;
					this.orderBrandArr = msg.brand;
					msg.detail.products.forEach(v=>{
						if(v.getProduct &&  v.getProduct.brand_name){
						
							this.exchangeSonArr[0]={label: v.getProduct.brand_name+ '×'+this.ruleform.bucket_num,value: v.getProduct.brand_name,amount:0}
							if(!this.ruleform.bucket_value)this.ruleform.bucket_value = this.exchangeSonArr[0].value;
							this.ruleform.bucket_brand = ""
						}
					})
				});
			},
			createAddress() {
				if (this.ruleform.status >= 3) {
					return false;
				}
				uni.setStorageSync('order_no', this.ruleform.order_no);
				return this.goto("/pages/user/address/create_edit/main", 1);
				this.createAddressUniapp(res => {
					if (res) {
						this.goto('/pages/order/buy/main?order_no='+this.ruleform.order_no)
					} else {
						this.goto("/pages/user/address/create_edit/main", 1);
					}
				});
			},
			submit(e) {
			
				if(!this.ruleform.deliver_name && this.ruleform.wechat_card_id && this.coupons.length){
					return this.getError('请选择兑回方式');
					
				}
				if(this.ruleform.type == 0 && !this.ruleform.bucket_type){
					return this.getError('请选择回桶类型');
				}
				if(this.ruleform.type == 0 && this.ruleform.bucket_type=='回桶' &&  this.ruleform.bucket_value=='其他品牌' && !this.ruleform.bucket_brand){
					return this.getError('请选择品牌');
				}
				
				let ruleform = this.ruleform;
				ruleform.area = uni.getStorageSync("waterCityData").area;
				if(ruleform.dianxin == 0){
					if(this.ruleform.fclass == 238 && !this.maskCheck){
						 this.getError("请同意口罩国际寄递风险需知");
						 return false;
					}
					if(ruleform.type ==0){
						if (ruleform.shipping != 1  && ruleform.fclass !=238) {
							if (!this.address || !this.address.id) {
								this.getError("请填写收货地址");
								setTimeout(() => {
									this.createAddress();
								}, 500)
								return false;
							}
							ruleform.address_id = this.address.id;
						}
					}else{
						
						// if (!this.address || !this.address.id) {
						// 	this.getError("请填写收货地址");
						// 	setTimeout(() => {
						// 		this.createAddress();
						// 	}, 500)
						// 	return false;
						// }
						// ruleform.address_id = this.address.id;
						
					}
					
					if (ruleform.coupon_value) {
						this.data.coupons.forEach((v, i) => {
							if (v.checked) {
								this.ruleform.coupon_userid = v.id;
							}
						});
					}
					if (this.ruleform.type == 0 && this.ruleform.shipping == 1 && !this.ruleform.merchant_user_id) {
						this.getError("请选择自提点");
						return false;
					}
					// if (this.ruleform.type == 1 &&  !this.ruleform.merchant_user_id) {
					// 	this.getError("请选择自提点");
					// 	return false;
					// }
				}
				this.ruleform.bucket_amount = this.exchangeBucket;
				this.postAjax(this.formAction, ruleform).then(msg => {
					if (msg.data.status == 2) {
						if (ruleform.dianxin == 1 || ruleform.fclass == 106 || ruleform.fclass == 109 || ruleform.fclass == 110) {
							return this.goto("/pages/order/payed/main?order_no=" + ruleform.order_no);
							return false;
						}
						if (this.amount == 0) {
							if (ruleform.is_group) {
								this.goto('/pages/group/group/index?order_no=' + ruleform.order_no);
							} else {
								this.goto('/pages/order/payed/main?order_no=' + ruleform.order_no);
							}
							return false;
						}
						if (ruleform.pay_method == 2 && parseFloat(ruleform.myAccount) > ruleform.amount) {
							this.goto("/pages/order/payed/main?order_no=" + ruleform.order_no);
							return false;
						}
						if (this.ruleform.pay_method == 3) {
							this.goto("/pages/ypay/main?order_no=" + ruleform.order_no + '&source=order', 1);
						} else {
							this.goto("/pages/order/pay_center/main?order_no=" + ruleform.order_no);
						}

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
							this.goto("/pages/order/lists/main?historyUrl=del&status=" + msg.data.detail.status);
						}
					});
				});

			}
		}
	}
</script>
<style>
@import url("index.css");
</style>