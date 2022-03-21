<template>
	<view>
		<page :parentData="data" :formAction="formAction" Fbottom="bottom: 45px">
			
		</page>
		<view class="container" >

			<view class="payed-content" v-if="!type">
				<view class="con">
					<view class="iconfont icon-pay-yes"></view>
					<view class="content" >使用水票购水成功，我们尽快配送上门！</view>
				</view>
			</view>
			<view class="payed-content" v-if="type">
				<view class="con">
					<view class="iconfont icon-pay-no"></view>
					<view class="content" >支付失败！请重新支付</view>
				</view>
			</view>
			<view class="button">
				<view @click="goto('/pages/index/main',2)">
					<dx-button type="success" size="lg" block>返回</dx-button>
				</view>

			</view>
		</view>
	</view>
</template>

<script>
	import dxTitle from "doxinui/components/title/title"
	export default {
		components: {
			dxTitle
		},
		data() {
			return {
				formAction: '/shop/order/payed',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				detail: {},
				type:'',
				getSiteName: this.getSiteName(),
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
			if(options.type){
				this.type = options.type;
			}
			//this.ajax();
		},
		methods: {
			wechatCard() {
				wx.addCard({
					cardList: [
						this.data.cardSignature
					],
					success: (res) => {
						this.postAjax("/shop/order/update", {
							give_wechatCard: 1,
							order_no: this.detail.order_no
						});
					},
					fail(res) {
						console.log('添加失败', res);
					},
					complete(res) {
						console.log('添加完成', res);
					}
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
</style>