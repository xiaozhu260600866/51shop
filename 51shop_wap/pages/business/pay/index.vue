<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view v-if="step == 1">
			<view v-if="data.show">
				<view class="block-sec main-bg p15 bdr12 fc-white flex-between flex-middle">
					<view class="left">
						<view class="fs-15">即将付款给</view>
						<view class="fs-20 mt5">{{data.merchantUser.userInfo.company_name}}</view>
					</view>
					<view class="right">
						<text class="iconfont icon-business-pay fs-36"></text>
					</view>
				</view>
				<view class="block-sec num-p p15">
					<view class="fs-15">金额</view>
					<view class="flex-end">
						<view>￥</view>
						<view class="flex1 mt10 lh-34 h-34 ml5 num-input">
							<text class="num fs-34" v-for="v in amount">{{v}}</text>
							<text class="cursor"></text>
							<text class="fs-24 fc-9" v-if="amount.length == 0">请输入金额</text>
						</view>
					</view>
				</view>
			</view>
			<view class="keypad" v-if="data.show">
				<view class="keypad-box">
					<view class="keypad-box-left">
						<view class="keypad-nav num" @click="changeAmount(1)">1</view>
						<view class="keypad-nav num" @click="changeAmount(2)">2</view>
						<view class="keypad-nav num" @click="changeAmount(3)">3</view>
						<view class="keypad-nav num" @click="changeAmount(4)">4</view>
						<view class="keypad-nav num" @click="changeAmount(5)">5</view>
						<view class="keypad-nav num" @click="changeAmount(6)">6</view>
						<view class="keypad-nav num" @click="changeAmount(7)">7</view>
						<view class="keypad-nav num" @click="changeAmount(8)">8</view>
						<view class="keypad-nav num" @click="changeAmount(9)">9</view>
						<view class="keypad-nav zero" @click="changeAmount(0)">0</view>
						<view class="keypad-nav num" @click="changeAmount('.')">•</view>
					</view>
					<view class="keypad-box-right" >
						<view class="keypad-nav colse" @click="del()">
							<text class="cha">×</text>
						</view>
						<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="submit()">
							<view slot="content">
								<view class="keypad-nav pay" v-if="amount.length == 0">付款</view>
								<view class="keypad-nav pay cur" v-else>付款</view>
							</view>
						</myform>
					</view>
				</view>
			</view>
		</view>
		<view v-if="step == 2">
			<dx-results txt="支付成功" @click="goto('/pages/business/show/main?id='+ruleform.merchant_user_id+'&hiddenCoupon=1',1)"></dx-results>
		</view>
	</view>
</template>

<script>
	import dxftButton from "doxinui/components/button/footer-button"
	import dxResults from "doxinui/components/results/results"
	export default {
		components: {
			dxftButton,
			dxResults 
		},
		data() {
			return {
				formAction: '/merchant/pay',
				mpType: 'page', //用来分清父和子组件
				step:1,
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				ruleform: {},
				vaildate: {},
				amount:[],
			}
		},
		onPullDownRefresh() {
			this.data.nextPage = 1;
			this.ajax();
		},
		onLoad(options) {
			this.ajax();
		},
		methods: {
			del(){
			   this.amount.splice(this.amount.length-1,1);	
			},
			changeAmount(res){
				this.amount.push(res);
			},
			submit() {
				let amount = this.amount.join("");
				if(!amount){
					this.getError('金额必填');
					return false;
				}
				var reg = /(^[1-9]([0-9]+)?(\.[0-9]{1,2})?$)|(^(0){1}$)|(^[0-9]\.[0-9]([0-9])?$)/;
				if (!reg.test(amount)) {
				    this.getError(errorMessage + "请填写正确的金额");
					return false;
				}
				this.ruleform.price = amount;
				this.vaildForm(this, res => {
					if (res) {
						this.ruleform.merchant_user_id = this.data.merchantUser.id;
						this.postAjax("/merchant/pay", this.ruleform).then(msg => {
							res = JSON.parse(msg.data.config);
							wx.requestPayment({
								'timeStamp': res.timeStamp,
								'nonceStr': res.nonceStr,
								'package': res.package,
								'signType': res.signType,
								'paySign': res.paySign,
								'success': res => {
									this.step = 2;
								},
								'fail': res => {
									this.getError("支付失败")
								}
							})
						});
					}
				})
			},
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		},

	}
</script>
<style>
	@import "./index.css";
</style>
