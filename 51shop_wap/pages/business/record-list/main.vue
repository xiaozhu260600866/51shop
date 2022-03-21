<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<view class="brecord-list bg-f p10 mb8 flex-between" v-for="item in data.lists.data">
				<view class="group">
					<view class="fs-14">提现</view>
					<view class="fs-14 Arial fc-red">-{{item.amount}}</view>
				</view>
				<view class="group">
					<view class="fs-12 fc-9 Arial">{{item.created_at}}</view>
					<view class="fs-12 fc-9">{{item.getStatus}}</view>
				</view>
			</view>
			<hasMore :parentData="data"></hasMore>
		</div>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				formAction: '/merchant/come-out-infos',
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