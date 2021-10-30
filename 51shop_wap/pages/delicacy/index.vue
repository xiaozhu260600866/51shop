<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view>
			<view class="top-custom" :style="{paddingTop:top*2+'rpx',height:height*2+'rpx',}">
				<view class="top-custom-box pl0">
					<view class="right flex1"><THeader :data="data" :callBack="true" city="江门市" @callBack="$refs.selectCity.init()" title="信息搜索" :waterCityData="waterCityData" TcountShow searchUrl="/pages/search/demand/main" noborder></THeader></view>
				</view>
			</view>
			<view :style="{marginTop: height*2+'rpx'}">
				<dx-nav-class :data="navGroup" @click="checkAuth" :num="5" :imgWidth="40" :imgHeight="40" :imgR="20" :nameSize="12" :namePTop="4"></dx-nav-class>
				<view class="ad p10">
					<image class="img bdr6 w-b100 flex" :src="getSiteName+'/images/app/iad-t.jpg'" mode="widthFix"></image>
				</view>
				<view class="delicacy-list">
					<view class="delicacy-container">
						<block v-for="(item,index) in lists" :key="index" v-if="(index+1)%2!=0 || isList">
							<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="item" @callBack="goto('',1)">
								<div slot="content">
									<view class="delicacy-item">
										<view class="img">
											<image :src="item.cover" class="cover" mode="widthFix"></image>
											<view class="tag">
												<view class="dxi-icon dxi-icon-location-fill"></view>
												<view class="distance">{{item.distance}}</view>
												<view class="type">{{item.type}}</view>
											</view>
										</view>
										<view class="delicacy-info">
											<view class="name">{{item.name}}</view>
											<view class="title">{{item.title}}</view>
											<view class="average">￥<text class="Arial">{{item.average_price}}</text>/人</view>
										</view>
									</view>
								</div>
							</myform>
						</block>
					</view>
					<view class="delicacy-container">
						<block v-for="(item,index) in  lists" :key="index" v-if="(index+1)%2==0">
							<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="item" @callBack="goto('',1)">
								<div slot="content">
									<view class="delicacy-item">
										<view class="img">
											<image :src="item.cover" class="cover" mode="widthFix"></image>
											<view class="tag">
												<view class="dxi-icon dxi-icon-location-fill"></view>
												<view class="distance">{{item.distance}}</view>
												<view class="type">{{item.type}}</view>
											</view>
										</view>
										<view class="delicacy-info">
											<view class="name">{{item.name}}</view>
											<view class="title">{{item.title}}</view>
											<view class="average">￥<text class="Arial">{{item.average_price}}</text>/人</view>
										</view>
									</view>
								</div>
							</myform>
						</block>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import THeader from '@/components/THeader';
	import dxNavClass from "doxinui/components/nav-class/nav-class";
	export default {
		components:{THeader,dxNavClass},
		data() {
			return {
				formAction: '/shop/wapindex2',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				height: 64, //header高度
				top: 0, //标题图标距离顶部距离
				city:'',
				waterCityData:'',
				navGroup:[
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
					{url:"",type:1,cover:"/static/fenxiaobg.jpg",name:"类别名称"},
				],
				lists:[{
					name:'仅178元乐享门市价468元三禾里日式烤肉四人套餐',
					title:'178元乐享三禾里门市价468元高端日式烤肉四人套餐主食：M6和牛横膈膜1份+安格斯上脑1份+优选猪梅花肉1份+黑毛猪五花1份+特级连肝肉1份+台湾墨鱼肠1份+蔬菜沙拉1份/水果沙拉1份（2选1）+特色牛肉炒饭1份+毛豆1份+酸萝卜1份+炉费+茶位4个',
					cover:'https://456-0310.doxinsoft.com/upload/images/articleOrder/300_YyhS8QRO3G.jpg',
					average_price:57,
					distance:'485m',
					type:'烤肉',
				},{
					name:'仅178元乐享门市价468元三禾里日式烤肉四人套餐',
					title:'178元乐享三禾里门市价468元高端日式烤肉四人套餐主食：M6和牛横膈膜1份+安格斯上脑1份+优选猪梅花肉1份+黑毛猪五花1份+特级连肝肉1份+台湾墨鱼肠1份+蔬菜沙拉1份/水果沙拉1份（2选1）+特色牛肉炒饭1份+毛豆1份+酸萝卜1份+炉费+茶位4个',
					cover:'https://456-0310.doxinsoft.com/upload/images/articleOrder/300_YyhS8QRO3G.jpg',
					average_price:57,
					distance:'485m',
					type:'烤肉',
				},{
					name:'仅178元乐享门市价468元三禾里日式烤肉四人套餐',
					title:'178元乐享三禾里门市价468元高端日式烤肉四人套餐主食：M6和牛横膈膜1份+安格斯上脑1份+优选猪梅花肉1份+黑毛猪五花1份+特级连肝肉1份+台湾墨鱼肠1份+蔬菜沙拉1份/水果沙拉1份（2选1）+特色牛肉炒饭1份+毛豆1份+酸萝卜1份+炉费+茶位4个',
					cover:'https://456-0310.doxinsoft.com/upload/images/articleOrder/300_YyhS8QRO3G.jpg',
					average_price:57,
					distance:'485m',
					type:'烤肉',
				},{
					name:'仅178元乐享门市价468元三禾里日式烤肉四人套餐',
					title:'178元乐享三禾里门市价468元高端日式烤肉四人套餐主食：M6和牛横膈膜1份+安格斯上脑1份+优选猪梅花肉1份+黑毛猪五花1份+特级连肝肉1份+台湾墨鱼肠1份+蔬菜沙拉1份/水果沙拉1份（2选1）+特色牛肉炒饭1份+毛豆1份+酸萝卜1份+炉费+茶位4个',
					cover:'https://456-0310.doxinsoft.com/upload/images/articleOrder/300_YyhS8QRO3G.jpg',
					average_price:57,
					distance:'485m',
					type:'烤肉',
				}]
			}
		},
		methods: {
			checkAuth(v){
				return this.goto(v.url,v.type);
			},
			ajax() {
				this.getAjaxForApp(this, {
				
				}).then(msg => {
					
				});
			}
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
			//this.ajax();
		},
		onShareAppMessage() {
			return this.shareSource(this, '商城');
		},
	}
</script>

<style scoped="" lang="scss">
@import './index.scss';
</style>
