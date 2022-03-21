<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<view class="top-custom" :style="{paddingTop:top*2+'rpx',height:height*2+'rpx',}">
				<view class="top-custom-box pl0" v-if="data.content != 'nodata'">
					<view class="right flex1"><THeader showCity :data="data" :callBack="true" :waterCityData="waterCityData" @callBack="$refs.selectCity.init()" title="产品搜索" noborder></THeader></view>
				</view>
			</view>
			<div :style="{marginTop: height*2+'rpx'}" v-if="data.content != 'nodata'">
				<div class="bg-f swiper_box" v-if="data.silders2.data.length">
					<myswiper :data="data.silders2.data"></myswiper>
				</div>
				<!-- <view class="tread bg-f">
					<view class="tread-info">
						<text class="Arial">{{data.weather.date}}</text>
						<text>星期{{data.weather.week}}</text>
						<text class="Arial">{{data.weather.temperature}}°</text>
						<text>{{data.weather.weather}}</text>
					</view>
					<view class="tread-info">总信息量<text class="Arial">{{data.articleOrderCount}}</text></view>
					<view class="tread-info">总浏览<text class="Arial">{{data.viewsCount}}</text></view>
				</view> -->
				<swiper class="swiper bgf mb8" :vertical="vertical" :autoplay="false" :indicator-dots="true" :duration="duration"
				 :circular="circular" :style="data.location[0].length<=5 ? 'height: 100px;' : 'height: 410rpx;'" v-if="data.siteConfig.is_index == 1">
					<swiper-item v-for="(parent,parentKey) in data.location">
						<div class="Nav_btn pb10">
							<div class="item" v-for="v in parent">
								<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="v" @callBack="goto(v.url,1)">
									<div slot="content">
										<div class="Nav_img">
											<image :src="v.cover" class="img" />
										</div>
										<div class="txt">{{ v.name }}</div>
									</div>
								</myform>
							</div>
						</div>
					</swiper-item>
				</swiper>
				<!-- <view id="banner" class="ad_banner" v-if="v.data.length" v-for="(v,key) in data.adArray">
					<view class="ad_group flex" v-if="key %2 ==0">
						<view class="item" v-for="children in v.data" @click="goto(children.url,children.name == '在线商城'? 2 : 1)">
							<image class="img" :src="children.cover" mode="widthFix" />
						</view>
					</view>
					<myswiper :lists="v.data" v-if="key %2  !=0"></myswiper>
				</view> -->
				<view v-if="data.package.length">
					<div class="ass-title p15 pr0 bgf" @click="goto('/pages/package/lists/main?fclass=7',1)">
						<p class="name fs-16">精选推荐<span class="fs-12 pt5 fc-9 pl5">推荐更多优质好货</span></p>
						<p class="icon iconfont icon-n-right fc-9 fs-13 pr15"></p>
					</div>
					<productLists :data="data.package" type="1" :order="true"></productLists>
				</view>
				<view v-if="data.lists.data.length">
					<div class="ass-title p15 pr0 bgf" @click="goto('/pages/product/lists/main',1)">
						<p class="name fs-16">桶装水<span class="fs-12 pt5 fc-9 pl5">推荐更多优质好货</span></p>
						<p class="icon iconfont icon-n-right fc-9 fs-13 pr15"></p>
					</div>
					<productLists :data="data" type="1"></productLists>
				</view>
				<div class="copyright fs-13 fc-9 m20 flex-center flex">
					<span>版权所有：</span><span class="fc-9">456在线</span>
				</div>
			
			</div>
		</div>
		<selectCity ref="selectCity" title="请选择配送区域" :OFFicon="true" :ruleform="ruleform" :townArr="townArr" :cityArr="cityArr" :provinceArr="provinceArr" :areaArr="areaArr" @callBack="cityCallBack"></selectCity>
		<view class="noData text-center pt50" :style="{marginTop: height*2+'rpx'}" v-if="data.content == 'nodata'">
			<view class="lh-1"><dx-icon name="shopping-bag" :size="100" color="#e1e1e1"></dx-icon></view>
			<view class="fs-16 fc-6 mt20">暂无产品</view>
		</view>
	</view>
</template>

<script>
	import marque from "xiaozhu/uniapp/components/marque";
	import THeader from '@/components/THeader';
	import productLists from "@/components/productLists";
	import dxTabsScroll from "doxinui/components/tabs/tabs_scroll"
	import demandLists from "@/components/demandLists";
	import dxIcon from "doxinui/components/icon/icon"
	import dxAddress from "xiaozhu/uniapp/components/addressAndCity";
	import selectCity from "@/components/selectCity.vue"
	export default {
		components: {marque,THeader,productLists,dxTabsScroll,demandLists,dxAddress,selectCity,dxIcon},
		data() {
			return {
				formAction: '/shop/wapindex',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				waterCityData:'',
				height: 64, //header高度
				top: 0, //标题图标距离顶部距离
				ruleform:{province:'广东省'},
				vaildate:{},
				provinceArr:[],
				townArr:[],
				cityArr:[],
				areaArr:[],
				tabs: [
					{value: 0,name: "今日特拼"},
					{value: 1,name: "全部"},
					{value: 2,name: "新品"},
				],
			}
		},
		onReachBottom() {
			this.hasMore(this);
		},
		onPullDownRefresh() {
			this.data.nextPage = 1;
			this.ajax();
		},
		onShow(){
			this.onShow(this);
		},
		onShareAppMessage() {
			if (this.data.dis) this.data.query.distribution = this.data.dis.id;
			return this.shareSource(this, '456在线',"notshare");
		},
		onLoad(options) {
			let obj = {};
			// #ifdef MP-WEIXIN
			obj = wx.getMenuButtonBoundingClientRect();
			// #endif
			// #ifdef MP-BAIDU
			obj = swan.getMenuButtonBoundingClientRect();
			// #endif
			// #ifdef MP-ALIPAY
			my.hideAddToDesktopMenu();
			// #endif
			uni.getSystemInfo({
				success: (res) => {
					this.width = obj.left || res.windowWidth;
					this.height = obj.top ? (obj.top + obj.height + 8) : (res.statusBarHeight + 44);
					this.top = obj.top ? (obj.top + (obj.height - 32) / 2) : (res.statusBarHeight + 6);
					this.scrollH = res.windowWidth
				}
			});
			
			this.ajax();
			
		},
		methods: {
			cityCallBack(item){
				uni.setStorageSync("waterCityData",item);
				uni.setStorageSync("waterCity",item.province + item.city+ item.town + item.area);
				this.ajax();
				this.waterCityData = item;
				console.log(item);
			},
			toProduct(item) {
				if (item.num > 0 && item.canHot) {
					return this.goto('/pages/hot/show/index?id=' + item.id, 1);
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
	}
</script>
<style>
@import url("index.css");
</style>