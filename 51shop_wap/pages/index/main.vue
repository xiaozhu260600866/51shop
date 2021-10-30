<template>
	<view>
		<page :parentData="data" :formAction="formAction" Fbottom="bottom: 25px">
			<view slot="floatBtn">
				<!-- <view @click="phone('0750-3336666')">
					<floatBtn icon="icon-float-tel" myclass="float-nav-w-green" iSize="fs-22" title="电话" shadow></floatBtn>
				</view> -->
				<!-- <view @click="data.dis ? goto('/pages/distribution/index/main',1) :goto('/pages/distribution/add/main',1) " v-if="data.release">
					<floatBtn  myclass="float-nav-red" iSize="fs-16" nSize="fs-14" title="申请VIP"></floatBtn>
				</view> -->
				<floatBtn type="2" icon="icon-float-feedback" myclass="float-nav-red" iSize="fs-22" openType="contact" title="咨询" shadow></floatBtn>
			</view>
		</page>
		<view v-if="data.show">
			<view class="top-custom" :style="{paddingTop:top*2+'rpx',height:height*2+'rpx',}">
				<view class="top-custom-box pl0">
					<view class="right flex1"><THeader showCity :data="data" :callBack="true" city="江门市" @callBack="$refs.selectCity.init()" title="信息搜索" :waterCityData="waterCityData" TcountShow searchUrl="/pages/search/demand/main" noborder></THeader></view>
				</view>
			</view>
			<view :style="{marginTop: height*2+'rpx'}">
				<view class="groupAd">
					<myswiper :data="data.ad1"></myswiper>
				</view>
				<!-- <view id="banner" class="pt5" v-if="data.silders.data.length">
					<myswiper :lists="data.silders.data"></myswiper>
				</view> -->
				<view class="tread bg-f" :class="data.ad1.length == 0?'pt10':''">
				<!-- 	<view class="tread-info">
						<text class="Arial">{{data.weather.date}}</text>
						<text>星期{{data.weather.week}}</text>
						<text class="Arial">{{data.weather.temperature}}°</text>
						<text>{{data.weather.weather}}</text>
					</view> -->
					<view class="tread-info">总信息量<text class="Arial">{{data.articleOrderCount}}</text></view>
					<view class="tread-info">总浏览<text class="Arial">{{data.viewsCount}}</text></view>
				</view>
				<view class="mb8">
					<!-- 一级分类 -->
					<view class="InavClass">
						<dx-nav-class :data="data.location" :num="5" :namePTop="0" :imgR="25" :tbPadding="0" @click="childrenNav"></dx-nav-class>
					</view>
					<!-- 二级分类 -->
					<!-- @click="classNav" <view class="InavGroup" v-if="childrenShow">
						<dx-nav-class ref="text" :data="children" @click="childrenNav" :num="5" :isturnpage="true" :pageNum="5" :namePTop="0" :imgR="25" :tbPadding="0" ></dx-nav-class>
					</view> -->
				</view>
				<view class="infoGroup">
					<demand-lists :data="data.lists.data" @thumb="thumb3" v-if="listsShow" type="1"></demand-lists>
				</view>
				<view class="infoGroup">
					<view class="groupAd" >
						<myswiper :data="data.ad2"></myswiper>
					</view>
					<businessLists :data="data.merchant" :otherData="otherData"></businessLists>
				</view>
				
				<view class="infoGroup">
					<view class="groupAd">
						<myswiper :data="data.ad3"></myswiper>
					</view>
					<demand-lists :data="data.lists.data" @thumb="thumb3" v-if="listsShow" type="2"></demand-lists>
				</view>
				
				<view class="bg-f ptb15 text-center fs-14 fc-3" @click="goto('/pages/demand/index/main',1)">点击查看更多</view>
				
				<!-- <view class="ass-title p15 pr0 bgf" @click="goto('/pages/product/lists/main',1)">
					<p class="name fs-16">精选推荐<span class="fs-12 pt5 fc-9 pl5">推荐更多优质好货</span></p>
					<p class="icon iconfont icon-n-right fc-9 fs-13 pr15"></p>
				</view>
				<productLists :data="data" type="1" v-if="data.lists.data.length"></productLists> -->
				<view class="copyright fs-13 fc-9 m20 flex-center flex">
					<span>版权所有：</span><span class="fc-9">456在线</span>
				</view>
			</view>
		</view>
		<selectCity ref="selectCity" title="请选择配送区域" :OFFicon="true" :ruleform="ruleform" :townArr="townArr" :cityArr="cityArr" :provinceArr="provinceArr" :areaArr="areaArr" @callBack="cityCallBack"></selectCity>
		<dx-diag  :tbPadding="0" :lrPadding="0" v-if="data.diag" ref="diag">
		　　<view @click="goto(data.diag.url,1)">
				<image class="w-b100 flex bdr6" :src="getSiteName+'/upload/images/disAd/800_'+ data.diag.poster_cover" mode="widthFix" v-if="data.diag.poster_cover"></image>
				<image class="w-b100 flex bdr6" :src="getSiteName+'/upload/images/poster/800_'+ data.diag.pic" mode="widthFix" v-if="data.diag.pic"></image>
		　　</view>
		</dx-diag>
	</view>
</template>

<script>
	import "./index.css";
	import marque from "xiaozhu/uniapp/components/marque";
	import THeader from '@/components/THeader';
	import productLists from "@/components/productLists";
	import demandLists from "@/components/demandLists";
	import businessLists from '@/components/business_lists';
	import dxNavClass from "doxinui/components/nav-class/nav-class"
	import selectCity from "@/components/selectCity.vue"
	import dxDiag from "doxinui/components/diag/diag"
	export default {
		components: {marque,THeader,productLists,demandLists,dxNavClass,businessLists,selectCity,dxDiag},
		data() {
			return {
				formAction: '/shop/wapindex2',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				height: 64, //header高度
				top: 0, //标题图标距离顶部距离
				waterCityData:'',
				ruleform:{province:'广东省'},
				provinceArr:[],
				townArr:[],
				cityArr:[],
				areaArr:[],
				vaildate:{},
				listsShow:false,
				showType:0,
				city:'',
				tabs: [
					{value: 0,name: "最新消息"},
					//{value: 1,name: "热门"},
				],
				otherData: {
					ANavType: 0,
					searchVal: ''
				},
				childrenKey:0,
				childrenShow:false,
				children:[],
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
			if(uni.getStorageSync('city')){
				this.city = uni.getStorageSync('city');
			}
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
			if(!uni.getStorageSync('city')){
				uni.setStorageSync('city','江门市');
			}
			if(uni.getStorageSync('city')){
				this.city = uni.getStorageSync('city');
			}

			
			
			// #ifdef MP-WEIXIN
			this.getMyAddress(this,msg=>{
				this.ajax();
			})
			// #endif
			// #ifdef H5
				this.ajax();
			// #endif
			
			// uni.request({
			//     url: 'http://192.168.0.102/?username=456&password=123', //仅为示例，并非真实接口地址。
			//     data: {
			//        // text: 'uni.request'
			//     },
			//     header: {
			//         'custom-header': 'hello' //自定义请求头信息
			//     },
			//     success: (res) => {
			//         console.log("are you ok ",res.data.reset);
			//         //this.text = 'request success';
			//     }
			// });
			
		},
		methods: {
			cityCallBack(item){
				uni.setStorageSync("waterCityData",item);
				uni.setStorageSync("waterCity",item.province + item.city+ item.town + item.area);
				this.ajax();
				this.waterCityData = item;
				console.log(item);
			},
			thumb3(key){
				let item = this.data.lists.data[key];
				
				this.postAjax("/articleOrder/thumb",{articleOrder_id:item.id},"notloading").then(msg=>{
					 if(msg.data.status == 2){
						 //this.getParent()
						 //this.$set(item,"thumb",!item.thumb);
						 item.thumb = !item.thumb;
						 if(item.thumb){
							 item.thumbCount ++;
						 }else{
							  item.thumbCount --;
						 }
					 }
				});
			},
			toProduct(item) {
				if (item.num > 0 && item.canHot) {
					return this.goto('/pages/hot/show/index?id=' + item.id, 1);
				}
			},
			ajax() {
				if(!this.waterCityData)this.waterCityData = uni.getStorageSync("waterCityData");
				this.getAjax(this,{showType:this.showType,townNew:uni.getStorageSync("town")}).then(msg => {
					this.listsShow = true;
					if(this.data.nextPage == 1){
						this.$refs.diag.thisDiag  = true;
					}
					
				});
			},
			classNav(v){
				
				v.key = parseInt(v.key);
				this.childrenShow = false
				this.childrenKey = v.key
				setTimeout(()=>{
					this.childrenShow = true;
					this.children = this.data.location[v.key].children;
				},300);
				
				//this.$refs.text.data = this.children;
			},
			childrenNav(v){
				//console.log(v)
				if(v.name == '桶装水'){
					return this.goto('/pages/water-mall/main',2)	
				}else{
					return this.goto(v.url,1);
				}
				
			},
		},
	}
</script>
