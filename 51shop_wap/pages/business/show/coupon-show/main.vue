<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view class="mb60" v-if="data.show">
			<view class="showBox top-box">
				<view class="cover">
					<image class="img" :src="getSiteName+'/upload/images/wechatCard/'+detail.cover" mode="aspectFill"></image>
				</view>
				<view class="cash-info">
					<view class="w-b100">
						<view class="coupon-title fs-16 lh-20 mb5 fc-0">{{detail.name}}</view>
						<view class="coupon-merchant fs-15 lh-20 mb5 fc-6">{{detail.merchant_names}}</view>
					</view>
					<view class="flex-baseline w-b100">
						<view class="price fs-18"><text class="fs-14">￥</text>{{detail.amount}}</view>
						<view class="coupon-full fs-12 fc-9 ml10" v-if="detail.full_amount">满<text class="Arial plr3">{{detail.full_amount}}</text>元可使用</view>
					</view>
				</view>
			</view>
				
			<view class="con-box showBox">
				<view class="explain">
					<dx-title name="现金券介绍" borderColor="#33c45d" borderWidth="30" borderR="4" nameColor="#333" nameSize="16" nameBold="bold" Bline></dx-title>
					<view class="content"><u-parse :content="data.detail.content" /></view>
					
				</view>
			</view>
			<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="item" @callBack="packageCoupon()" v-if="data.isEx == 0">
				<div slot="content">
					<dxftButton type="success" size="lg" round>免费领取</dxftButton>
				</div>
			</myform>
			<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="item" @callBack="goto('/pages/business/show/main?id='+data.query.original_merchant,1)" v-else>
				<div slot="content">
					<dxftButton type="info" size="lg" round>已领取</dxftButton>
				</div>
			</myform>
		</view>
	</view>
</template>

<script>
	import dxTabs from "doxinui/components/tabs/tabs"
	import dxTitle from "doxinui/components/title/title"
	import dxftButton from "doxinui/components/button/footer-button"
	import uParse from '@/components/gaoyia-parse/parse.vue'
	export default {
		components:{dxTabs,dxTitle,dxftButton,uParse},
		data() {
			return {
				formAction: '/user/coupon-package',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				detail:{},
				ruleform:{},
				vaildate:{},
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
		onShow(){
			this.onShow(this);
		},
		onLoad(options) {
			this.ajax();
		},
		methods: {
			packageCoupon(){
				this.getConfirm("是否领取",msg=>{
					this.data.detail.original_merchant=this.data.query.original_merchant;
					this.postAjax(this.formAction,this.data.detail).then(msg=>{
						if(msg.data.status == 2){
							return this.goto('/pages/index/main',2);
							//this.ajax();
						}
					});
				})
			},
			ajax() {
				this.getAjax(this).then(msg => {
					this.detail = msg.detail;
				});
			}
		}
	}
</script>
<style>
@import url("index.css");
@import url("@/components/gaoyia-parse/parse.css");
</style>