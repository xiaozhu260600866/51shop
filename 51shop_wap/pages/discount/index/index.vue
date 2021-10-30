<template>
	<view>
		<page :parentData="data" :formAction="formAction" Fbottom="bottom: 25px">
			<view slot="floatBtn">
				<view @click="goto('/pages/index/main',2)">
					<floatBtn myclass="float-nav-green" iSize="fs-16" title="回首页"></floatBtn>
				</view>
			</view>
		</page>
		<div v-if="data.show">
			<!-- <div class="Tsearch bg-f flex pl10" :style="{height:height+'px',paddingTop:top-5 +'px'}">
				<p class="fs-17 lh-40" @click="$refs.selectCity.init()">{{waterCityData ?waterCityData.area : '折扣市场'}}</p>
				<div class="flex1">
					<searchHeader title="搜索商品"></searchHeader>
				</div>
			</div>  :style="{marginTop:height +'px'}"-->
			<THeader :data="data" noborder :callBack="true" :waterCityData="waterCityData" @callBack="$refs.selectCity.init()" title="产品搜索"></THeader>
			<div>
				<div id="banner" class="pt5" v-if="data.silders.data.length">
					<myswiper2 :lists="data.silders.data"></myswiper2>
				</div>
				<div class="Nav_btn mb10">
					<div class="item" v-for="(v,index) in data.fclass_">
						<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="v"
						 @callBack="goto('/pages/discount/product/lists/main?type=1&fclass='+v.id,1)">
							<div slot="content">
								<div class="Nav_img">
									<img :src="v.getcover" class="img" />
								</div>
								<div class="txt">{{v.name}}</div>
							</div>
						</myform>
					</div>
				</div>
				<contact myclass="mb8"></contact>
				<div class="ass-title p15 pr0 bgf">
					<p class="name fs-16">精选推荐</p>
					<p class="icon iconfont icon-right fc-9 fs-13 pr15"></p>
				</div>
				<productLists :data="data" type="2" v-if="data.lists.data.length"></productLists>
			</div>
			
		</div>
		<selectCity ref="selectCity" title="请选择配送区域" :ruleform="ruleform" :townArr="townArr" :cityArr="cityArr" :provinceArr="provinceArr" :areaArr="areaArr" @callBack="cityCallBack"></selectCity>
	</view>
</template>

<script>
	import "./index.css";
	import searchHeader from '@/components/searchHeader2';
	import productLists from "@/components/productLists";
	import selectCity from "@/components/selectCity.vue"
	import THeader from '@/components/THeader';
	export default {
		components: {searchHeader,productLists,selectCity,THeader},
		data() {
			return {
				formAction: '/shop/discount/index?fclass=223',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				orderBy: "id",
				provinceArr:[],
				waterCityData:'',
				townArr:[],
				cityArr:[],
				areaArr:[],
				otherData: {
					scrollTop: 0,
					searchVal: '',
					card_user_id: ''
				},
				height: 64, //header高度
				top: 0, //标题图标距离顶部距离
				ruleform:{province:'广东省'},
				vaildate:{}
			}
		},
		onShow(){
			this.onShow(this);
		},
		onReachBottom() {
			//this.hasMore(this);
		},
		onPullDownRefresh() {
			this.data.nextPage = 1;
			this.ajax();
		},
		onShareAppMessage() {
			if (this.data.dis) this.data.query.distribution = this.data.dis.id;
			return this.shareSource(this, '456在线');
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
		methods: {
			cityCallBack(item){
				uni.setStorageSync("waterCityData",item);
				uni.setStorageSync("waterCity",item.province + item.city+ item.town + item.area);
				this.ajax();
				this.waterCityData = item;
				console.log(item);
			},
			searchName(e) {
				setTimeout(() => {
					if (e.mp.type == "confirm") {
						this.otherData.searchVal = e.mp.detail.value;
					}
					if (!this.otherData.searchVal) return false;
					this.goto("/pages/product/lists/main?name=" + this.otherData.searchVal, 1);
				}, 200);

			},
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
