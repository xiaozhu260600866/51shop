<template>
	<view>
		<page :parentData="data" :formAction="formAction" Fbottom="bottom: 25px">
			<!-- <view slot="floatBtn">
				<floatBtn type="2" icon="icon-float-feedback" myclass="float-nav-red" iSize="fs-22" openType="contact" title="咨询" shadow></floatBtn>
			</view> -->
		</page>
		<view v-if="data.show">
			<view class="top-custom">
				<THeader showCity :data="data" :callBack="true" city="江门市" @callBack="goto('/pages/search/searchCity/main',1)" title="搜索商品信息" :waterCityData="waterCityData" TcountShow noborder></THeader>
			</view>
			<view>
				<view class="swiper_box">
					<myswiper :data="data.ad1" myclass="pt0"></myswiper>
				</view>
				<view class="InavClass mb10">
					<dx-nav-class :data="data.location" :num="4" :namePTop="5" :imgWidth="46" :imgHeight="46" :itemPadding="5" :nameSize="14"
					 @click="childrenNav"></dx-nav-class>
				</view>
				<view class="pro-scroll m12 bdr12">
					<view class="top flex-right pr15 pt20">
						<view class="num fs-11 fc-white">9<text>+</text></view>
						<view class="fs-14 fc-9 flex-center lh-1">更多<text class="dxi-icon dxi-icon-right fs-11 pt2"></text></view>
					</view>
					<dx-products-scroll :data="lists" :itemWidth="150" imgHeight="220rpx" imageField="firstCover" imgR="12rpx" :nameSize="12" :priceSize="15"
					 bgColor="transparent" :itemLRMargin="3"></dx-products-scroll>
				</view>
				<view class="infoGroup">
					<view class="tit plr15 pt15 pb5 flex-between">
						<view class="name fs-18">限时抢购</view>
						<view class="countdown flex-middle">
							<view class="text pr3">剩余</view>
							<tui-countdown :time="timeList" bcolor="#d30802" bgcolor="#d30802" colonColor="#d30802" color="#fff"
							 :height="32" :width="36"></tui-countdown>
							<view class="text pl3">结束</view>
						</view>
					</view>
					<proLists :data="lists" isList></proLists>
				</view>
				<view class="infoGroup">
					<view class="groupAd" >
						<myswiper :data="data.ad2"></myswiper>
					</view>
					<!-- <businessLists :data="data.merchant" :otherData="otherData"></businessLists> -->
				</view>
				
				<view class="infoGroup">
					<view class="groupAd">
						<myswiper :data="data.ad3"></myswiper>
					</view>
					<!-- <demand-lists :data="data.lists.data" @thumb="thumb3" v-if="listsShow" type="2"></demand-lists> -->
				</view>
				
				<view class="bg-f ptb15 text-center fs-14 fc-3" @click="goto('/pages/demand/index/main',1)">点击查看更多</view>
				
				<view class="copyright fs-13 fc-9 m20 flex-center flex">
					<span>版权所有：</span><span class="fc-9">广东科阅信息技术有限公司</span>
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
	import THeader from '@/components/THeader';
	import proLists from "@/components/proLists";
	import demandLists from "@/components/demandLists";
	import businessLists from '@/components/business_lists';
	import dxNavClass from "doxinui/components/nav-class/nav-class"
	import selectCity from "@/components/selectCity.vue"
	import dxDiag from "doxinui/components/diag/diag"
	import tuiCountdown from "xiaozhu/uniapp/thorui/components/countdown/countdown";
	import dxProductsScroll from "doxinui/components/products/scroll"
	export default {
		components: {THeader,proLists,demandLists,dxNavClass,businessLists,selectCity,dxDiag,tuiCountdown,dxProductsScroll},
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
				swiper01:[
					{cover:'https://51shop.doxinsoft.com/images/site/tt00.jpg'}
				],
				lists:[{
					firstCover:'/static/fenxiaobg.jpg',
					name:'「新品焖锅」低至42.9元，秒131元『凯苑名菜』双人超值套餐',
					price:'42.9',
					new_price:'131',
					id:'4619',
				},{
					firstCover:'/static/fenxiaobg.jpg',
					name:'「新品焖锅」低至42.9元，秒131元『凯苑名菜』双人超值套餐',
					price:'42.9',
					new_price:'131',
					id:'4619',
				},{
					firstCover:'/static/fenxiaobg.jpg',
					name:'「新品焖锅」低至42.9元，秒131元『凯苑名菜』双人超值套餐',
					price:'42.9',
					new_price:'131',
					id:'4619',
				},{
					firstCover:'/static/fenxiaobg.jpg',
					name:'「新品焖锅」低至42.9元，秒131元『凯苑名菜』双人超值套餐',
					price:'42.9',
					new_price:'131',
					id:'4619',
				}],
				timeList:6000
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
			checkAuth(v){
				return this.goto(v.url,v.type);
			},
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
<style>
@import url("index.css");
</style>