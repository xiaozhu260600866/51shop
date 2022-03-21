<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view class="pb150" v-if="data.show">
			<view class="showBox">
				<view class="top-box">
					<view class="cash-info">
						<!-- <view class="price fc-white"><text class="fs-14">￥</text>{{detail.amount}}</view> -->
						<view class="cover">
							<image class="img" :src="getSiteName+'/upload/images/wechatCard/'+detail.getCoupon.logo_url" mode="aspectFill"></image>
						</view>
						<view class="info">
							<view class="coupon-title w-b100 fs-16 lh-1_5 wrap2">{{detail.name}}</view>
							<view class="time w-b100 fs-12 lh-1 Arial">{{detail.start_at}}-{{detail.end_at}}</view>
						</view>
					</view>
				</view>
				<view class="con-box">
					<view class="qrcode">
						<image :src="getSiteName + '/upload/images/qrcode/'+detail.code+'.svg'" class="img"></image>
						<view class="txt">出示二维码给商家扫码</view>
						<view class="txt">电子券码：<text class="Arial">{{detail.code}}</text></view>
					</view>
					<view class="explain">
						<dx-title name="现金券介绍" borderColor="#33c45d" borderWidth="30" borderR="4" nameColor="#333"
							nameSize="16" nameBold="bold" Bline></dx-title>
						<view class="content">{{detail.getCoupon.deal_detail}}</view>
					</view>
				</view>
				<view class="con-box bg-f" v-if="data.car_merchant.length">
					<view class="explain">
						<dx-title name="附近使用店" borderColor="#33c45d" borderWidth="30" borderR="4" nameColor="#333"
							nameSize="16" nameBold="bold" Bline></dx-title>
						<businessLists :data="data.car_merchant" :otherData="otherData"></businessLists>
						
					</view>
				</view>
			</view>
			<dxftButton type="success" size="lg" round
				@click="goto('/pages/business/show/main?id='+detail.merchant_ids,1)"
				v-if="detail.merchant_ids && detail.merchant_ids.split(',').length == 1">商家主页</dxftButton>
		</view>
	</view>
</template>

<script>
	import dxTabs from "doxinui/components/tabs/tabs"
	import dxTitle from "doxinui/components/title/title"
	import dxftButton from "doxinui/components/button/footer-button"
	import businessLists from '@/components/business_lists';
	export default {
		components: {
			dxTabs,
			dxTitle,
			dxftButton,
			businessLists
		},
		data() {
			return {
				formAction: '/user/couponInfo',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				detail: {},
				otherData: {
					ANavType: 0,
					searchVal: ''
				},
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
		onShow() {
			this.onShow(this);
		},
		onLoad(options) {
			this.getMyAddress(this, msg => {
				this.ajax();
			})
		},
		methods: {

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
</style>