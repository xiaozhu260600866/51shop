<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<div class="pro_info m10 bdr3 fs-15" v-for="(parent,key) in data.lists.data">
				<div class="order_date plr10 bd-be fc-6">
					<p class="time">日期：<span class="Arial">{{parent.created_at}}</span></p>
					<view>{{parent.status_message}}</view>
				</div>
				<view class="order_info p10">
					<div class="pro_img mr10">
						<img class="img bdr3" :src="parent.getProduct.firstCover" />
					</div>
					<div class="txt flex1">
						<p class="fs-16 lh-20">{{parent.getProduct.name}}</p>
						<p class="lh-16 fc-6">次数：<span class="Arial">{{parent.num}}</span></p>
					</div>
				</view>
			</div>
			<hasMore :parentData="data" source="order"></hasMore>
		</div>
	</view>
</template>

<script>
	import "./index.css";
	export default {
		data() {
			return {
				formAction: '/shop/package/order-record-lists',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				status: 12
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
		onShow() {
			if (this.$store && this.$store.state && this.$store.state.mutations.historyUrl) {
				this.$store.state.mutations.historyUrl = "";
				this.ajax();
			}
		},
		onLoad(options) {
			this.ajax();
		},
		methods: {
			changeStatus(status) {
				this.data.query.status = status;
				this.data.nextPage = 1;
				this.ajax();
			},
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
