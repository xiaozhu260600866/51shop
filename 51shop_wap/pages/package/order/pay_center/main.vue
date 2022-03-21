<template>
	<view>
		<page :parentData="data" :formAction="formAction" ref="page"></page>
		<section class="container" v-if="data.show">
			<view id="pay_price">￥{{ data.amount }}</view>
			<view id="pay_info">
				<view class="list-group">
					<view class="txt_l">订单号</view>
					<view class="txt_r">{{detail.order_no}}</view>
				</view>
				<view class="list-group">
					<view class="txt_l">收款号</view>
					<view class="txt_r">{{siteConfig.web_name}}</view>
				</view>
				<view class="list-group">
					<view class="txt_l">支付项目</view>
					<view class="txt_r">{{siteConfig.web_name}}订单支付</view>
				</view>
			</view>
			<form @submit="data.source == 'wap' ? formSubmitForH5():formSubmitForApp($event)" bindreset="formReset" report-submit="true">
				<button id="pay_submit" formType="submit">立即支付</button>
			</form>
		</section>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				formAction: '/shop/package/ready-pay',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				detail: {},
				returnUrl:'',
				siteConfig: {},
				config: {},
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
			this.ajax();
		},
		methods: {
			formSubmitForH5(e){
				this.payUniapp(this.config,msg=>{
					this.goto(this.returnUrl);
				});
			},
			formSubmitForApp(e) {
				//调起微信支付
				let formId = e.mp.detail.formId;
				this.formId(formId).then(msg => {
					this.payUniapp(this.config,msg=>{
						wx.requestSubscribeMessage({
						  tmplIds: ['EhOxfmm5FvXZmCeYuw0KCfmEAjqAPUIVAjS5TAMm1Js'],
						  success (res) { 
							  console.log(res);
						  },
						  fail(res){
							 console.log(res); 
						  }
						})
						this.goto(this.returnUrl);
					});
				});
			},
			ajax() {
				this.getAjax(this).then(msg => {
					this.detail = msg.detail;
					this.siteConfig = msg.siteConfig;
					this.config = msg.config;
					if (this.detail.is_group) {
						this.returnUrl = '/pages/shop/group/group/main?order_no=' + this.detail.order_no;
					} else {
						this.returnUrl = '/pages/package/order/payed/main?order_no=' + this.detail.order_no;
					}
				});
			}
		},
		
	}

</script>
<style>
@import url("index.css");
</style>