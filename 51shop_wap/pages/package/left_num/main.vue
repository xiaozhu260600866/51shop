<template>
	<view>
		<page :parentData="data" :formAction="formAction" ref="page"></page>
		<section v-if="data.show">
			<div id="address" class="bg-f plr10" v-if="ruleform.shipping !=1">
				<block v-if="!address">
					<div class="add-add ptb15" @click="createAddress">
						<p class="add-icon iconfont icon-count-plus mr10"></p>
						<p class="add-txt fs-16">新增收货地址</p>
						<p class="iconfont icon-right fs-12 fc-9"></p>
					</div>
				</block>
				<block v-else>
					<div class="add-info" @click="addressLists">
						<div class="info ptb10">
							<p class="name fs-16">
								<span>{{address.name}}</span>
								<span class="Arial pl10">{{address.phone}}</span>
							</p>
							<div class="add-detail fs-15 fc-6">
								<p class="label">地址：</p>
								<p class="name">{{ address.province }} {{ address.city }} {{ address.area }} {{ address.address }}</p>
							</div>
						</div>
						<p class="iconfont icon-right fs-12 fc-9 pl15"></p>
					</div>
				</block>
			</div>
			<div class="pay_line bg-f mb10" v-if="ruleform.shipping !=1">
				<image src="https://boss.doxinsoft.com/images/site/share-tip.png"></image>
			</div>
			<div class="pro-info bg-f mb10">
				<div class="cart-tb p10">
					<div class="pro-img pr10">
						<image :src="ruleform.getProduct.firstCover" />
					</div>
					<div class="pro-name fs-15 lh-20">
						<div class="name lh-24 fs-16 nowrap">{{ruleform.getProduct.name}}</div>
						<div>原数量：<span class="Arial">{{ruleform.num}}</span></div>
						<div>剩余数量 ：<span class="Arial">{{ruleform.left_num}}</span></div>
					</div>
				</div>
			</div>
			<div id="mode" class="bg-f mb10">
				<weui-input v-model="ruleform.remark" label="买家留言" type="text" name="remark" placeholder="点击给商家留言"></weui-input>
			</div>
			<div class="pro-num flex-between fs-16 bg-f plr15 ptb10 mb10">
				<p class="label">购买数量</p>
				<tui-numberbox :value="ruleform.bnum" @change="change" min="1"></tui-numberbox>
			</div>
		
			<!-- 回桶、押桶、买桶选择，三者选一个 -->
			<view class="order-sec">
				<weui-input @radioCallBack="bucketCallBack" v-model="ruleform.bucket_type" name="bucket_type" changeField="label"
				 :type="ruleform.status>=3 ?'txt':'radio'" dataKey="bucketTypeArr" myclass="exchangeSon"></weui-input>
				<view class="relative">
					<weui-input v-model="ruleform.bucket_value" name="bucket_value" changeField="label" :type="ruleform.status>=3?'txt':'radio'"
					 dataKey="exchangeSonArr" myclass="exchangeSon" v-if="ruleform.bucket_type == '回桶'" :row="true" Labelleft></weui-input>
					<view class="flex-middle orderBrand" v-if="ruleform.bucket_value == '其他品牌' && ruleform.bucket_type == '回桶'">
						<weui-input v-model="ruleform.bucket_brand" placeholder="请选择品牌" myclass="Brand" name="bucket_brand" changeField="value"
						 type="select" dataKey="orderBrandArr"></weui-input>
						<weui-input v-model="ruleform.bucket_num" label="数量" myclass="Num" name="bucket_num" changeField="value" type="select"
						 dataKey="orderNumArr"></weui-input>
					</view>
					<view class="pl40 pb10 fs-15" v-if="ruleform.bucket_value == '其他品牌' && ruleform.bucket_type == '回桶'"></view>
				</view>
				<view class="tong" v-if="ruleform.bucket_type == '押桶'">
					<view class="list">押桶：{{exchangeSonArr[0].label}}</view>
					<view class="list">
						<view>温馨提示：</view>
						<view class="con fc-6 lh-24">押桶：50元一个。<br>每个空桶每年使用费10元。（不足一年按一年收取使用费）</view>
					</view>
				</view>
				<view class="tong" v-if="ruleform.bucket_type == '买桶'">
					<view class="list">买桶：{{exchangeSonArr[0].label}}</view>
					<view class="list">
						<view>温馨提示：</view>
						<view class="con fc-6 lh-24">买桶：15元一个。</view>
					</view>
				</view>

				<!-- <weui-input v-model="ruleform.bucket_num" label="桶数" name="bucket_num" changeField="label" :type="ruleform.status>=3?'txt':'select'" dataKey="bucketNumArr" placeholder="选择桶数"  v-if="ruleform.bucket_type != '回桶'"></weui-input> -->
			</view>
			<!-- 回桶、押桶、买桶选择，三者选一个 -->
			<div id="footer" style="width:100%">


			</div>
			<view id="calculation" class="order-sec" v-if="ruleform.bucket_type">

				<!-- 根据上述选择，三选其一显示 -->
				<view class="list-group" v-if="ruleform.bucket_type == '回桶'">
					<p class="txt">换桶费</p>
					<p class="fs-16 price">￥{{exchangeBucket}}</p>
				</view>
				<view class="list-group" v-if="ruleform.bucket_type == '押桶'">
					<p class="txt">押桶</p>
					<p class="fs-16 price">￥{{exchangeBucket}}</p>
				</view>
				<view class="list-group" v-if="ruleform.bucket_type == '买桶'">
					<p class="txt">买桶</p>
					<p class="fs-16 price">￥{{exchangeBucket}}</p>
				</view>
				<!-- 根据上述选择，三选其一显示 -->
			</view>
			<view id="footer">
				<view class="f_left price fs-18 plr10">￥
					<span class="fs-24 fw-bold">{{amount}}</span>
				</view>
				<view class="f_right">
				<!-- 	<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="redayPay" myclass="nav" title="提交订单"></myform> -->
					<button class="nav" @click="redayPay">提交订单</button>
						
					
				</view>
			</view>
		

			<diag ref="diag">
				<div slot="content">
					<div style="height: 60px;" class="flex-center flex-middle">扣次成功</div>
					<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="goto('/pages/index/main',2)" style="width:100%"
					 myclass="dx-btn dx-btn-big dx-btn-green w-b100 fs-17" title="首页">
					</myform>
				</div>
			</diag>
		</section>
	</view>
</template>
<script>
	import "./index.css";
	import tuiNumberbox from "xiaozhu/uniapp/thorui/components/numberbox/numberbox"
	export default {
		components: {
			tuiNumberbox
		},
		data() {
			return {
				formAction: '/shop/package/info',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				ruleform: {
					bnum: 1
				},
				vaildate: {},
				actIndex: 0,
				order_no: 0,
				address_id: 0,
				address: {},
				coupons: [],
			bucketTypeArr: [
				{label: '回桶',value: '回桶'},
				// {label: '押桶',value: '押桶'},
				{label: '买桶',value: '买桶'},
			],
			exchangeSonArr: [
				{label: '华山泉*4',value: '华山泉*4',amount:0},
				{label: '其他品牌',value: '其他品牌',amount:10},
			],
			orderBrandArr: [
				{label: '华山泉',value: '华山泉'},
				{label: '红百立',value: '红百立'},
				{label: '绿百立',value: '绿百立'},
				{label: '全清纯',value: '全清纯'},
				{label: '七翁井',value: '七翁井'},
				{label: '七井清泉',value: '七井清泉'},
				{label: '怡宝',value: '怡宝'},
				{label: '加伦加',value: '加伦加'},
				{label: '长寿村',value: '长寿村'},
				{label: '一品怡',value: '一品怡'},
				{label: '农夫山泉',value: '农夫山泉'},
				{label: '鼎湖山泉',value: '鼎湖山泉'},
				{label: '屈臣氏',value: '屈臣氏'},
			],
			orderNumArr: [
				{label: '1',value: '1'},
				{label: '2',value: '2'},
				{label: '3',value: '3'},
				{label: '4',value: '4'},
				{label: '5',value: '5'},
				{label: '6',value: '6'},
				{label: '7',value: '7'},
				{label: '8',value: '8'},
				{label: '9',value: '9'},
			],
			bucketNumArr: [
				{label: '1个 50元',value: '1个 50元',amount:50},
				{label: '2个 100元',value: '2个 100元',amount:100},
				{label: '3个 150元',value: '3个 150元',amount:150},
				{label: '4个 200元',value: '4个 200元',amount:200},
				{label: '5个 250元',value: '5个 250元',amount:250},
				{label: '6个 300元',value: '6个 300元',amount:300},
				{label: '7个 350元',value: '7个 350元',amount:350},
				{label: '8个 400元',value: '8个 400元',amount:400},
				{label: '9个 450元',value: '9个 450元',amount:450},
				{label: '10个 500元',value: '10个 500元',amount:500},
			],
			}
		},
		computed: {

			exchangeBucket(){
				let res = 0 ;
				if(this.ruleform.bucket_type== '回桶' &&this.ruleform.bucket_value == '其他品牌'){
					//res+=10 * parseInt(this.ruleform.bucket_num);
					res+=0;
				}else if(this.ruleform.bucket_type== '押桶' ){
					  res+= 50 * parseInt(this.ruleform.bnum);;
				}else if(this.ruleform.bucket_type == '买桶'){
					  res+= 15 * parseInt(this.ruleform.bnum);;
				}
				return res;
			},
			amount() {
				//如果是微信支付

				let order_amount = 0;
				if (this.exchangeBucket > 0) {
					order_amount += parseFloat(this.exchangeBucket);
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
			wx.removeStorageSync('order_no');
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
			bucketCallBack() {
				//this.ruleform.bucket_num = 1
			},
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
				this.getAjax(this).then(msg => {
					this.address = msg.address;
					this.ruleform = msg.detail;
					this.$set(this.ruleform, "bnum", 1);
					this.ruleform.bucket_num = this.ruleform.bnum;
					this.orderBrandArr = msg.brand;
					if ( this.ruleform.getProduct.brand_name) {
						console.log('qqa')
						this.exchangeSonArr[0] = {
							label: this.ruleform.getProduct.brand_name + '×' + this.ruleform.bucket_num,
							value: this.ruleform.getProduct.brand_name,
							amount: 0
						}
						if (!this.ruleform.bucket_value) this.ruleform.bucket_value = this.exchangeSonArr[0].value;
						this.ruleform.bucket_brand = ""

					}

				});
			},
			createAddress() {
				if (this.ruleform.status >= 3) {
					return false;
				}
				wx.removeStorageSync('order_no');
				wx.setStorageSync('package', this.ruleform.id);
				this.createAddressUniapp(res => {
					if (res) {
						this.ajax();
					} else {
						this.goto("/pages/user/address/create_edit/main", 1);
					}
				});
			},
			formSubmitForApp() {
				//调起微信支付
				let res = JSON.parse(this.config);
				wx.requestPayment({
					'timeStamp': res.timeStamp,
					'nonceStr': res.nonceStr,
					'package': res.package,
					'signType': res.signType,
					'paySign': res.paySign,
					'success': res => {
						//发送模板消息结束
						return this.goto("/pages/package/left_num/payed/main");
					},
					'fail': res => {
						return this.goto("/pages/package/left_num/payed/main?type=fail");
					}
				})
			},
			redayPay(e){
				wx.requestSubscribeMessage({
					tmplIds: ['q8wXLHnOUqZl-iKgq6Uia-UuFG7ZVKqXJozFfiTsobc','9XA0ZfZbjymbQqicyO3ISpSNmzmW9LvZw1Woh0FHLgI','lgTDjp3SdweGCltNMgKfvuWxXk52npQyev59Rx1G01s'],
					success:res=> {
						console.log(res);
						this.submit(e);
					},
					fail:res=> {
							console.log(res);
						this.submit(e);
					}
				})
			},
			submit(e) {
				if(!this.ruleform.bucket_type){
					return this.getError('请选择回桶类型');
				}
				if( this.ruleform.bucket_type=='回桶' &&  this.ruleform.bucket_value=='其他品牌' && !this.ruleform.bucket_brand){
					return this.getError('请选择品牌');
				}
				this.getConfirm("是否确认使用水票购水？", msg => {
					let ruleform = this.ruleform;
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
					this.ruleform.bucket_amount = this.exchangeBucket;
					this.postAjax("/shop/product/package", ruleform).then(msg => {
						if (msg.data.status == 2) {
							/* setTimeout(() => {
								this.goto("/pages/index/main", 2);
							}, 2000) */
							
							this.postAjax("/toMessage.html",{id:msg.data.order_id},"","notloading");
							
							if (this.exchangeBucket > 0) {
								this.config = msg.data.config;
								this.formSubmitForApp();
							} else {
								return this.goto("/pages/package/left_num/payed/main");
							}

							//this.$refs.diag.ajax();

						}
					});
				})


			},
			addressLists() {
				if (this.ruleform.status >= 3) {
					return false;
				}
				wx.removeStorageSync('order_no');
				wx.setStorageSync('package', this.ruleform.id);
				this.goto('/pages/user/address/lists/main?id=' + this.ruleform.id);
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

			},
			change: function(e) {
				this.ruleform.bnum = e.value;
				this.ruleform.bucket_num = e.value;
				this.exchangeSonArr[0] = {
					label: this.ruleform.getProduct.brand_name + '×' + this.ruleform.bucket_num,
					value: this.ruleform.getProduct.brand_name,
					amount: 0
				}
			},
		}
	}
</script>
