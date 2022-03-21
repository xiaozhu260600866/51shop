<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<view class="brecord-list plr15 ptb10 bd-be bg-f flex-between flex-middle" v-for="item in data.lists.data">
				<view class="group">
					<view class="fs-17">付款给：{{item.getUser.userInfo.company_name}}</view>
					<view class="Arial fc-9 fs-16">{{item.created_at}}</view>
				</view>
				<view class="Arial fs-18">+{{item.amount}}</view>
			</view>
			<hasMore :parentData="data"></hasMore>
		</div>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				formAction: '/user/come-out-infos?buy_userid=1',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
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
			return this.shareSource(this, '商城');
		},
		onLoad(options) {
			this.ajax();
		},
		methods: {
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
<style>
@import url("index.css");
</style>