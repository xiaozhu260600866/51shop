<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view v-if="data.show">
			<view class="head-info main-bg">
				<view class="utop">
					<view class="uinfo" v-if="data.user.nickname == '游客'">
						<view class="header-img">
							<image class="img" :src="getSiteName +'/images/nouser01.png'" />
						</view>
						<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="v" @callBack="text">
							<view slot="content">
								<view class="login-nav pl20 pr15 lh-28 fc-white fs-14 mlr15 bdr14">立即登录<text class="iconfont icon-right fs-10 fc-white pl3"></text></view>
							</view>
						</myform>
					</view>
					<block v-else>
						<view class="uinfo">
							<view class="header-img">
								<image class="img" :src="data.user.headerPic" />
							</view>
							<view class="pl15 fc-white right info">
								<myform class="w-b100" :ruleform="ruleform" :vaildate="vaildate" :append="true" >
									<view slot="content" class="fc-white lh-24 fs-15">
										<view class="group">
											<view>{{data.user.nickname}}</view>
										</view>
										<view v-if="data.user.userInfo.phone">电话号码：<text class="Arial">{{data.user.userInfo.phone}}</text></view>
										<view class="group" v-if="data.user.member_lev">
											<view class="pr8"><text class="iconfont icon-vip fs-16"></text></view>
											<view class="fs-14">{{data.user.member_lev}}</view>
										</view>
									</view>
								</myform>
							</view>
						</view>
						<view class="head-count user-count">
							<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/integral/count/main',1)">
								<view slot="content" class="c-item" >
									<view class="num">{{data.integral}}</view>
									<view class="name">积分</view>
								</view>
							</myform>
							<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/coupon/cash/lists/main?status=12',1)">
								<view slot="content" class="c-item" >
									<view class="num">{{data.couponNum}}</view>
									<view class="name">卡券</view>
								</view>
							</myform>
							<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/collection/index',1)">
								<view slot="content" class="c-item" >
									<view class="num">0</view>
									<view class="name">收藏</view>
								</view>
							</myform>
							<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('',1)">
								<view slot="content" class="c-item" >
									<view class="num">{{data.articleOrderNum}}</view>
									<view class="name">浏览</view>
								</view>
							</myform>
						</view>
					</block>
				</view>
			</view>
			
			<view class="ugorup-box orderTab">
				<dx-nav-class :data="[
					{url:'/pages/order/lists/main?status=12&historyUrl=del',type: 1,cover:getSiteName+'/images/wap/order01.png',
					name:'全部订单',number:data.orders3},
					{url:'/pages/order/lists/main?historyUrl=del&status=1',type: 1,cover:getSiteName+'/images/wap/order02.png',
					name:'待付款',number:data.orders1},
					{url:'/pages/order/lists/main?historyUrl=del&status=5',type: 1,cover:getSiteName+'/images/wap/order03.png',
					name:'未完成',number:data.orders5},
					{url:'/pages/order/service/main?historyUrl=del&status=10',type: 1,cover:getSiteName+'/images/wap/order04.png',
					name:'售后',number:data.orders10}
				]" @click="checkAuth" :tbPadding="10" :num="4" :imgWidth="24" :imgHeight="24" :imgR="0" :nameSize="13" :namePTop="8"></dx-nav-class>
			</view>
			
			<view class="ulist ugorup-box">
				<view class="black-title">
					<view class="name">常用功能</view>
				</view>
				<view class="ulist-group ptb8 plr5">
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('',1)">
						<view slot="content">
							<view class="ulist-item" >
								<view class="item-icon">
									<image class="icon" :src="getSiteName+'/images/wap/userNav01.png'"></image>
									<text class="num" v-if="data.couponNum">{{data.couponNum}}</text>
								</view>
								<view class="item-name">拼团订单</view>
							</view>
						</view>
					</myform>
				
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('',1)">
						<view slot="content">
							<view class="ulist-item" >
								<view class="item-icon">
									<image class="icon" :src="getSiteName+'/images/wap/userNav02.png'"></image>
									<text class="num" v-if="data.couponNum">{{data.couponNum}}</text>
								</view>
								<view class="item-name">购物车</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/info/main',1)">
						<view slot="content">
							<view class="ulist-item" >
								<view class="item-icon">
									<image class="icon" :src="getSiteName+'/images/wap/userNav03.png'"></image>
									<text class="num" v-if="data.carCouponNum">{{data.carCouponNum}}</text>
								</view>
								<view class="item-name">账号设置</view>
							</view>
						</view>
					</myform>
				</view>
			</view>
			
			<view class="ulist ugorup-box">
				<view class="black-title">
					<view class="name">推广收益</view>
					<view class="more flex-middle" @click="goto(data.isDis ? '/pages/distribution/index/main' :'/pages/distribution/add/main' ,1)">
						<text>查看达人详情</text>
						<text class="dxi-icon dxi-icon-right fs-12 pl5"></text>
					</view>
				</view>
				<view class="other-count user-count">
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('',1)">
						<view slot="content" class="c-item">
							<view class="num">{{data.integral}}</view>
							<view class="name fc-9">累计收益</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('',1)">
						<view slot="content" class="c-item">
							<view class="num">{{data.couponNum}}</view>
							<view class="name fc-9">昨日收益</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('',1)">
						<view slot="content" class="c-item after">
							<view class="num main-color">0</view>
							<view class="name fc-9">待提现</view>
						</view>
					</myform>
				</view>
			</view>
			<view class="ugorup-box userDisAd" @click="toClient()">
				<image class="img w-b100" :src="getSiteName+'/images/wap/userDisAd.jpg'" mode="widthFix"></image>
			</view>
			<view class="copyright fs-13 fc-9 m20 flex-center flex">
				<text>版权所有：</text><span class="fc-9">广东科阅信息技术有限公司</span>
			</view>
			<clientPoster :data="data" ref="clientPoster"></clientPoster>
		</view>
	</view>
</template>

<script>
	import dxNavClass from "doxinui/components/nav-class/nav-class"
	import cashLists from "@/components/cashLists.vue"
	import clientPoster from "@/pages/distribution/client/poster/clientPoster"
	export default {
		components:{dxNavClass,cashLists,clientPoster},
		data() {
			return {
				formAction: '/shop/user',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				ruleform:{},
				vaildate:{},
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
			if (this.data.dis) this.data.query.distribution = this.data.dis.id;
			return this.shareSource(this, '456在线','1');
		},
		onLoad(options) {
			wx.removeStorageSync('order_no');			wx.removeStorageSync('package');
			this.ajax();
		},
		onShow(){
			this.onShow(this);
		},
		methods: {
			checkAuth(v){
				return this.goto(v.url,v.type);
			},
			text(){
				console.log("bbbbbbbbbbbbbbb");
				this.ajax()
				setTimeout(()=>{
						this.ajax()
				},1500);
			
				
			},
			toClient(){
				if(this.data.isDis){
					return this.$refs.clientPoster.shareFc()
				}else{
					return this.goto('/pages/distribution/add/main',1)
				}
			},
			toMerchant(){
				uni.navigateToMiniProgram({
					appId: 'wxb135e689e76457d2',
					path: 'pages/index/main',
					extraData: {
						'data1': 'test'
					},
					success(res) {
					// 打开成功
					}
				})
			},
			toZtd(){
				uni.navigateToMiniProgram({
				  appId: 'wxa6cfc3a417501b21',
				  path: 'pages/ztd/index/main',
				  extraData: {
				    'data1': 'test'
				  },
				  success(res) {
				    // 打开成功
				  }
				})
			},
			toManager(){
				uni.navigateToMiniProgram({
				  appId: 'wxf15d3bf6eb13b8dd',
				  path: 'pages/manager/index/main',
				  extraData: {
				    'data1': 'test'
				  },
				  success(res) {
				    // 打开成功
				  }
				})
			},
			
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		},
	}
</script>
<style>
@import url("index.css");
</style>