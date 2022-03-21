<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<div id="banner" class="pt5" v-if="data.silders.data.length">
				<myswiper2 :lists="data.silders.data"></myswiper2>
			</div>
			<div class="Nav_btn mb10">
				<div class="item" v-for="(v,index) in data.location.data">
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="v"
					 @callBack="goto(v.url,1)">
						<div slot="content">
							<div class="Nav_img">
								<img :src="v.cover" class="img" />
							</div>
							<div class="txt">{{v.name}}</div>
						</div>
					</myform>
				</div>
			</div>
			<productLists :data="data" type="3"></productLists>
		</div>
		<selectCity ref="selectCity" title="请选择配送区域" :OFFicon="true" :ruleform="ruleform" :townArr="townArr" :cityArr="cityArr" :provinceArr="provinceArr" :areaArr="areaArr" @callBack="cityCallBack"></selectCity>
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
				formAction: '/shop/product/lists?is_hot=1&type=1&fclassForIndex=360',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				ruleform:{province:'广东省'},
				vaildate:{},
				provinceArr:[],
				townArr:[],
				cityArr:[],
				areaArr:[],
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
			toProduct(item){
				if(item.num > 0 && item.canHot){
					return this.goto('/pages/hot/show/index?id='+item.id,1);
				}
				
			},
			ajax() {
				
				if(!uni.getStorageSync("waterCity")){
					   console.log("00759");
					   this.$nextTick(()=>{
						   this.$refs.selectCity.init();
					   })
						
				}else{
					if(!this.waterCityData)this.waterCityData = uni.getStorageSync("waterCityData");
					this.getAjax(this).then(msg => {
							console.log(this.data);
						});
					}
					
				}
			
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