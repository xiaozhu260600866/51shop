<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view class="pb60" v-if="data.show">
			<block v-if="ruleform.fclass!=238 && ruleform.dianxin == 0">
				<view v-if="ruleform.staff_userid" class="delivery bg-f flex-center flex-middle p15 mb10"
				 @click="goto('/pages/order/map/main?order_no='+ruleform.order_no,1)">
					<view class="iconfont icon-order-delivery fc-blue fs-24"></view>
					<view class="name fs-16 pl10">配送员位置</view>
				</view>
			</block>
			<view class="buy-detail">
				<view class="buy-detail-title pb0">
					<view class="name">商品信息</view>
				</view>
				<!-- <view class="buy-detail-con">
					<view class="name wrap3" v-for="item in ruleform.products">{{item.getProduct.name}}</view>
				</view> -->
				<orderPro :data="ruleform.products" myclass="plr15" url="/pages/product/show/main?id=" isDetail></orderPro>
				<view class="buy-detail-nav">
					<!-- <button hover-class="none" class="nav" openType="contact">
						<text class="dxi-icon dxi-icon-message-fill pr8 fc-orange"></text>
						<text class="txt">在线客服</text>
					</button> -->
					<button hover-class="none" class="nav" @click="phone('0750-3336888')">
						<text class="iconfont icon-buy-tel pr8 fc-5"></text>
						<text class="txt">电话客服</text>
					</button>
				</view>
			</view>
			<view class="buy-detail">
				<view class="buy-detail-title">
					<view class="name">批量核销</view>
				</view>
				<view class="buy-detail-con buy-detail-qrcode text-center">
					<image class="img w-b80" :src="getSiteName+'/upload/images/order/'+ruleform.order_no+'.png'" mode="widthFix"></image>
					<view class="fs-14 fc-6 text-center">出示该二维码即可消费多个订单</view>
				</view>
			</view>
			<view class="buy-detail">
				<view class="buy-detail-title">
					<view class="name">单个核销</view>
				</view>
				<view class="buy-detail-row">
					<view class="row" v-for="v in cancelL">
						<view class="left">电子码：<text class="num fs-17 fw-bold" :class="[v.status?'fc-9 cprice':'fc-1']">{{v.order_no}}</text></view>
						<view class="con" :class="[v.status?'fc-9':'']">{{v.status?'已完成':'待核销'}}</view>
						<view class="right"><text class="dxi-icon dxi-icon-qrcode"></text></view>
					</view>
				</view>
			</view>
			<view class="buy-detail">
				<view class="storeInfo" v-for="item in storeL">
					<view class="left">
						<view class="name fs-15 fw-bold">{{item.name}}</view>
						<view class="address fs-13 lh-1_3 mt5 fc-4">{{item.address}}</view>
					</view>
					<view class="right">
						<view class="item">
							<view class="icon dxi-icon dxi-icon-navigation"></view>
							<view class="txt">导航</view>
						</view>
						<view class="item">
							<view class="icon dxi-icon dxi-icon-tel-fill2"></view>
							<view class="txt">电话</view>
						</view>
					</view>
				</view>
			</view>
			<view class="buy-detail">
				<view class="buy-detail-title">
					<view class="name">个人信息</view>
				</view>
				<view class="buy-detail-row">
					<view class="row">
						<view class="left">用户姓名</view>
						<view class="right">{{ruleform.addr_name}}</view>
					</view>
					<view class="row">
						<view class="left">联系电话</view>
						<view class="right Arial">{{ruleform.addr_phone}}</view>
					</view>
					<view class="row" v-if="ruleform.addr_address">
						<view class="left">收货地址</view>
						<view class="right">{{ruleform.addr_address}}</view>
					</view>
				</view>
			</view>
			<view class="buy-detail">
				<view class="buy-detail-title">
					<view class="name">订单信息</view>
				</view>
				<view class="buy-detail-row">
					<view class="row">
						<view class="left">订单编号</view>
						<view class="right Arial">{{ruleform.order_no}}</view>
					</view>
					<view class="row">
						<view class="left">购买数量</view>
						<view class="right Arial">{{ruleform.num}}</view>
					</view>
					<view class="row">
						<view class="left">商品金额</view>
						<view class="right Arial">￥{{ruleform.amount}}</view>
					</view>
					<!-- 根据上述选择，三选其一显示 -->
					<view class="row" v-if="ruleform.bucket_type == '回桶'">
						<view class="left">换桶费</view>
						<view class="right Arial">￥{{exchangeBucket}}</view>
					</view>
					<view class="row" v-if="ruleform.bucket_type == '押桶'">
						<view class="left">押桶</view>
						<view class="right Arial">￥{{exchangeBucket}}</view>
					</view>
					<view class="row" v-if="ruleform.bucket_type == '买桶'">
						<view class="left">买桶</view>
						<view class="right Arial">￥{{exchangeBucket}}</view>
					</view>
					<view class="row" v-if="ruleform.shipping == 2">
						<view class="left">运费</view>
						<view class="right Arial">{{ruleform.yunfei}}</view>
					</view>
					<view class="row" v-if="ruleform.coupon_value">
						<view class="left">优惠券</view>
						<view class="right Arial">- ￥{{ ruleform.coupon_value }}</view>
					</view>
					<view class="row" v-if="ruleform.remark">
						<view class="left">买家留言</view>
						<view class="right Arial">{{ruleform.remark}}</view>
					</view>
				</view>
			</view>
			<view class="buy-detail">
				<view class="buy-detail-row ptb10">
					<view class="row">
						<view class="left">发票</view>
						<view class="right">订单使用时联系商家开具发票</view>
					</view>
				</view>
			</view>
			<view id="footer" v-if="ruleform.status == 5">
				<view class="f_left"></view>
				<view class="f_right flex-middle mlr5"><view class="nav" @click="canReceipt">确认收货</view></view>
			</view>
			<view id="footer" class="bd-t" v-else>
				<view class="f_left"></view>
				<view class="f_right flex-middle mlr5"><view class="nav nav-o">{{ruleform.status_message}}</view></view>
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
				formAction: '/shop/order/detail',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				maskCheck:false,
				value: 1,
				pay_methods: [
					/* {label: '翼支付',value: 3}, */
					{label: '微信支付',value: 1},
					{label: '余额支付',value: 2},
				],
				cancelL:[
					{order_no:9962382748011,status:1},
					{order_no:9962382748052,status:0},
				],
				storeL:[
					{name:'大漠风炭火烤肉自助餐厅',address:'广东省江门市蓬江区汇悦大融城三楼炭火烤肉自助餐厅'},
					{name:'大漠风炭火烤肉自助餐厅江海万达店',address:'广东省江门市江海区万达广场四楼大漠风炭火烤肉自助餐厅'},
				],
				ruleform: {
					bucketType:1,
					echange: 1,
					orderNum: '1',
					need:'须知须知须知须知须知须知',
					yatong:'华山泉*4',
					maitong:'华山泉*4',
				},
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
@import url("./index.css");
</style>