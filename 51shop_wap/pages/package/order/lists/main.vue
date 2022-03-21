<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<div class="order-classify">
				<p :class="['nav-tab',data.query.status == 3 ? 'selected' :'']" @click="changeStatus(3)">我的水票</p>
				<p :class="['nav-tab',data.query.status == 9 ? 'selected' :'']" @click="changeStatus(9)">已使用</p>
				<!-- <p :class="['nav-tab',status == 10 ? 'selected' :'']" @click="changeStatus(10)">售后</p> -->
			</div>
			<div class="w-ticket" v-for="(parent,key) in data.lists.data">
				<div class="wt-item bg-f bdr6 m10 p12 fs-16" v-for="(son,key2) in parent.getProduct">
					<div class="ti-top flex-between flex-middle pb12 bd-be">
						<p class="lh-24 fc-6">时间：<span class="Arial">{{parent.created_at}}</span></p>
						<p class="dx-btn dx-btn-sm fs-12 dx-btn-green bdr30" @click="goto('/pages/package/order/record/main?id='+parent.id+'&product_id='+son.getProduct.id,1)">购水记录</p>
					</div>
					<div class="ti-bottom pt12">
						<div class="lt-info flex">
							<img class="img bdr6 mr10" :src="son.getProduct.firstCover" />
							<p class="lh-20 fs-17 flex1 wrap2">{{son.getProduct.name}}</p>
						</div>
						<div class="lb-info flex-between ptb12">
							<p>水票：{{son.num}}</p>
							<p>剩余：{{son.left_num}}</p>
						</div>
						<div class="lb-info" v-if="parent.getPackage && parent.getPackage.start_at">
							<p>水票使用开始时间：{{parent.getPackage.start_at}}</p>
							<p>水票使用结束时间：{{parent.getPackage.end_at}}</p>
						</div>
					</div>
					<div class="btn-group pt10 bd-te" @click="goto('/pages/product/show/main?id='+son.getProduct.id,1)">
						<div class="btn-item">
							<div class="btn-nav">我要下单</div>
						</div>
					</div>
				</div>
			</div>

			<hasMore :parentData="data" source="order"></hasMore>
		</div>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				formAction: '/shop/package/order-lists',
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
<style>
@import url("index.css");
</style>