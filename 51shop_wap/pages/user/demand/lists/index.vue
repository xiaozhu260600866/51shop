<template>
	<view class="pb60">
		<page :parentData="data" :formAction="formAction"></page>
		<view v-if="data.show">
			<dx-tabs :tabs="tabs" selectedColor="#33c45d" :selectedSize="32" :size="32" sliderBgColor="#33c45d" @change="listsShow = false;ajax()" v-model="data.query.status"></dx-tabs>
			<demand-lists edit :data="data.lists.data"></demand-lists>
			<dxftButton size="lg" type="success" @click="goto('/pages/user/demand/class/main',1)">发布帖子</dxftButton>
			<hasMore :parentData="data" source="nodata"></hasMore>
		</view>
	</view>
</template>

<script>
	import demandLists from "@/components/demandLists";
	import dxftButton from "doxinui/components/button/footer-button"
	import dxTabs from "doxinui/components/tabs/tabs"
	export default {
		components: {demandLists,dxftButton,dxTabs},
		data() {
			return {
				formAction: '/articleOrder/lists',
				mpType: 'page', //用来分清父和子组件
				listsShow:false,
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				tabs: [
					{value: 4,name: "展示中"},
					{value: 3,name: "审核中"},
					{value: 5,name: "已拒绝"}
				],
			}
		},
		methods: {
			ajax() {
				this.getAjax(this).then(msg => {
					this.listsShow = true;
				});
			}
		},
		onShow() {
			this.ajax();
		},
		onLoad(options) {
			this.ajax();
		},
		components: {  },
		onReachBottom() {
			this.hasMore(this);
		},
		onPullDownRefresh() {
			this.data.nextPage = 1;
			//this.ajax();
		},
		onShareAppMessage() {
			return this.shareSource(this, '商城');
		},
		
	}
</script>
<style>
@import url("index.css");
</style>