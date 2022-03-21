<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<myswiper :data="swiper"></myswiper>
			<productLists :data="data.package" type="1" v-if="data.package.length" :order="true"></productLists>
		</div>
		<selectCity ref="selectCity" title="请选择配送区域" :OFFicon="true" :ruleform="ruleform" :townArr="townArr"
			:cityArr="cityArr" :provinceArr="provinceArr" :areaArr="areaArr" @callBack="cityCallBack"></selectCity>
	</view>
</template>

<script>
	import productLists from "@/components/productLists";
	import selectCity from "@/components/selectCity.vue"
	export default {
		components: {
			productLists,
			selectCity
		},
		data() {
			return {
				ruleform: {
					province: '广东省'
				},
				vaildate: {},
				provinceArr: [],
				townArr: [],
				cityArr: [],
				areaArr: [],
				swiper: [],
				waterCityData: {},
				formAction: '/shop/wapindex?type_=all',
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
			cityCallBack(item) {
				uni.setStorageSync("waterCityData", item);
				uni.setStorageSync("waterCity", item.province + item.city + item.town + item.area);
				this.ajax();
				this.waterCityData = item;
				console.log(item);
			},
			changeStatus(status) {
				this.data.query.status = status;
				this.data.nextPage = 1;
				this.ajax();
			},
			ajax() {
				if (!uni.getStorageSync("waterCity")) {
					console.log("00759");
					this.$nextTick(() => {
						this.$refs.selectCity.init();
					})

				} else {
					if (!this.waterCityData) this.waterCityData = uni.getStorageSync("waterCityData");
					this.getAjax(this).then(msg => {
						if (msg.packageClass && msg.packageClass.cover2) {
							let coverArr = msg.packageClass.cover2.split(",");
							for (let i = 0; i < coverArr.length; i++) {
								this.swiper.push({
									cover: this.getSiteName + "/upload/images/product/800_" + coverArr[i]
								})
							}
						}
					});
				}
			}
		}
	}
</script>
<style>
@import url("index.css");
</style>