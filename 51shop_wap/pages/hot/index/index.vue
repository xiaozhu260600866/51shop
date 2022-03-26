<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<!-- <productLists :data="data" isList></productLists> -->
			<proLists :data="data.lists.data" isList></proLists>
			<hasMore :parentData="data"></hasMore>
		</div>
		<selectCity ref="selectCity" title="请选择配送区域" :ruleform="ruleform" :townArr="townArr" :cityArr="cityArr" :provinceArr="provinceArr" :areaArr="areaArr" @callBack="cityCallBack"></selectCity>
	</view>
</template>

<script>
	import productLists from "@/components/productLists";
	import selectCity from "@/components/selectCity.vue"
	import proLists from "@/components/proLists";
	export default {
		components: {productLists,selectCity,proLists},
		data() {
			return {
				formAction: '/shop/product/lists?is_hot=1&type=1',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				provinceArr:[],
				waterCityData:'',
				townArr:[],
				cityArr:[],
				areaArr:[],
				ruleform:{province:'广东省'},
				vaildate:{}
			}
		},
		methods: {
			cityCallBack(item){
				uni.setStorageSync("waterCityData",item);
				uni.setStorageSync("waterCity",item.province + item.city+ item.town + item.area);
				this.ajax();
				this.waterCityData = item;
				console.log(item);
			},
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		},
		onLoad(options) {
			if(options.title){
				this.setTitle(options.title);
			}
			if(!uni.getStorageSync("waterCity")){
					console.log("00759");
					this.$nextTick(()=>{
						this.$refs.selectCity.init();
					})
					
			}else{
				if(!this.waterCityData)this.waterCityData = uni.getStorageSync("waterCityData");
				this.ajax();
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
		
	}
</script>
<style>
@import url("index.css");
</style>