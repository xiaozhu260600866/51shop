<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view v-if="data.show">
			<view class="head-info">
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
					<view class="uinfo" v-else>
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
									<!-- <view>地址：{{data.user.userInfo.address}}</view> -->
									<view class="group" v-if="data.user.member_lev">
										<view class="pr8"><text class="iconfont icon-vip fs-16"></text></view>
										<view class="fs-14">{{data.user.member_lev}}</view>
									</view>
								</view>
							</myform>
						</view>
						<!-- <myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="v" @callBack="goto('/pages/user/integral/sign/main',1)" >
							<view slot="content">
								<view class="gosign fs-13 fc-white">
									<text class="iconfont icon-user-integral-sign fc-white pr5 fs-12"></text>签到
								</view>
							</view>
						</myform> -->
					</view>
				</view>
			</view>
			<view class="ugorup-box head-count">
				<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/recharge/count/main',1)">
					<view slot="content" class="c-item" >
						<view class="num">{{data.amount}}<text class="unit">元</text></view>
						<view class="name">余额</view>
					</view>
				</myform>
			<!-- 	<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/coupon/cash/lists/main?status=12',1)">
					<view slot="content" class="c-item" >
						<view class="num">{{data.couponNum}}<text class="unit">元</text></view>
						<view class="name">我的券</view>
					</view>
				</myform> -->
				<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/integral/count/main',1)">
					<view slot="content" class="c-item" >
						<view class="num">{{data.integral}}<text class="unit"></text></view>
						<view class="name">积分</view>
					</view>
				</myform>
				<!-- <myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/demand/lists/index?status=4',1)">
					<view slot="content" class="c-item" >
						<view class="num">{{data.articleOrderNum}}<text class="unit">条</text></view>
						<view class="name">信息展示中</view>
					</view>
				</myform>
				<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('',1)">
					<view slot="content" class="c-item" >
						<view class="num">0<text class="unit">条</text></view>
						<view class="name">信息已下架</view>
					</view>
				</myform> -->
			</view>
			
			<view class="ugorup-box">
				<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="v"
				 @callBack="goto('/pages/order/lists/main?status=12&historyUrl=del',1)">
					<view slot="content" class="black-title">
						<view class="name">我的订单</view>
						<view class="all fs-13 fc-6">查看全部
							<text class="iconfont icon-right more fs-13"></text>
						</view>
					</view>
				</myform>
				<dx-nav-class :data="[
					{url:'/pages/order/lists/main?historyUrl=del&status=1',type: 1,cover:getSiteName+'/images/wap/order-icon01.png',
					name:'待付款',number:data.orders1},
					{url:'/pages/order/lists/main?historyUrl=del&status=3',type: 1,cover:getSiteName+'/images/wap/order-icon02.png',
					name:'待发货',number:data.orders3},
					{url:'/pages/order/lists/main?historyUrl=del&status=5',type: 1,cover:getSiteName+'/images/wap/order-icon03.png',
					name:'待收货',number:data.orders5},
					{url:'/pages/order/lists/main?historyUrl=del&status=9',type: 1,cover:getSiteName+'/images/wap/order-icon04.png',
					name:'待评价',number:data.orders9},
					{url:'/pages/order/service/main?historyUrl=del&status=10',type: 1,cover:getSiteName+'/images/wap/order-icon05.png',
					name:'售后',number:data.orders10}
				]" @click="checkAuth" isAuth myclass="bdr12" :num="5" :imgWidth="30" :imgHeight="30" :imgR="0" :nameSize="13" :namePTop="5"></dx-nav-class>
			</view>
			
			<view class="ulist ugorup-box">
				<view class="black-title">
					<view class="name">我的券</view>
				</view>
				<view class="ulist-group ptb8 plr5">
				
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/coupon/cash/lists/main?status=0&title=免费券&package_coupon_id_not_null=1&order_no_null=1',1)">
						<view slot="content">
							<view class="ulist-item" >
								<view class="item-icon">
									<text class="iconfont icon-user-carCoupon"></text>
									<text class="num" v-if="data.couponNum">{{data.couponNum}}</text>
								</view>
								<view class="item-name">免费券</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/coupon/cash/lists/main?status=0&sourceView=car&title=多店通用券',1)">
						<view slot="content">
							<view class="ulist-item" >
								<view class="item-icon">
									<text class="iconfont icon-user-coupon-cash"></text>
									<text class="num" v-if="data.carCouponNum">{{data.carCouponNum}}</text>
								</view>
								<view class="item-name">多店通用券</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/coupon/cash/lists/main?status=0&title=产品券&order_no_not_null=1',1)">
						<view slot="content">
							<view class="ulist-item" >
								<view class="item-icon">
									<text class="iconfont icon-user-coupon"></text>
									<text class="num" v-if="data.couponNum2">{{data.couponNum2}}</text>
								</view>
								<view class="item-name">产品券</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/package/order/lists/main?historyUrl=del&status=3',1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-tiaobao"></text>
								</view>
								<view class="item-name">水票</view>
							</view>
						</view>
					</myform>
				</view>
			</view>
			
			<view class="ugorup-box" v-if="data.diag && data.diag.poster_cover2" @click="goto(data.diag.url2,1)">
				<image class="w-b100 flex" :src="getSiteName+'/upload/images/disAd/800_'+ data.diag.poster_cover2" mode="widthFix"></image>
			</view>
			<view class="ugorup-box" v-if="data.diag && data.diag.pic" @click="goto(data.diag.url,1)">
				<image class="w-b100 flex" :src="getSiteName+'/upload/images/poster/800_'+ data.diag.pic" mode="widthFix"></image>
			</view>
			
			
			
			<view class="ulist ugorup-box">
				<view class="ulist-group ptb8 plr5">
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto(data.isDis ? '/pages/distribution/index/main' :'/pages/distribution/login/main' ,1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-distribution fs-23"></text>
								</view>
								<view class="item-name">VIP中心</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="toMerchant" v-if="data.release == 0">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-merchant"></text>
								</view>
								<view class="item-name">{{data.merchantName}}</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/merchant/main',1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-pay"></text>
								</view>
								<view class="item-name">我的付款</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/collection/index',1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-collection"></text>
								</view>
								<view class="item-name">我的收藏</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/address/lists/main',1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-address"></text>
								</view>
								<view class="item-name">收货地址</view>
							</view>
						</view>
					</myform>
				</view>
			</view>
			
			<view class="ugorup-box">
				<contact myclass="mt10"></contact>
			</view>
			<view class="copyright fs-13 fc-9 m20 flex-center flex">
				<text>版权所有：</text><text class="fc-9">456在线</text>
			</view>
				<!--
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/demand/lists/index?status=4',1)" v-if="data.release">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-demand"></text>
								</view>
								<view class="item-name">我的帖子</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/cart/main?historyUrl=del',1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-cart"></text>
								</view>
								<view class="item-name">我的购物车</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="data.user.role == 8 ? goto('/pages/supplier/index/main',1): goto('/pages/supplier/login/main',1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-supplier"></text>
								</view>
								<view class="item-name">供货商中心</view>
							</view>
						</view>
					</myform>
					
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="toManager()">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-agency"></text>
								</view>
								<view class="item-name">平台代理</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/coupon/mylist/main?historyUrl=del&status=0',1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-coupon"></text>
								</view>
								<view class="item-name">平台优惠券</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="v" @callBack="goto('/pages/group/order-lists/index?status=12&historyUrl=del',1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-group"></text>
								</view>
								<view class="item-name">我的拼团</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/user/coupon/list/main?historyUrl=del',1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-receive"></text>
								</view>
								<view class="item-name">领取优惠券</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="checkUser(data.isDis ? '/pages/distribution/qrcode/main' :'/pages/distribution/add/main' ,1)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-qrcode"></text>
								</view>
								<view class="item-name">我的二维码</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="goto('/pages/order/dian_lists/main?historyUrl=del&fclass=84',1)">
						<view slot="content">
							<view class="weui-cell">
								<view class="item-icon">
									<text class="iconfont icon-user-group"></text>
								</view>
								<view class="item-name">电信业务</view>
							</view>
						</view>
					</myform>
					<view open-type="switchTab" class="weui-cell" @click="goto('/pages/user/cart/main?historyUrl=del',1)">
						<view class="item-icon">
							<text class="iconfont icon-user-cart"></text>
						</view>
						<view class="weui-cell__bd">我的购物车</view>
						<view class="weui-cell__ft iconfont icon-right"></view>
					</view>
					<view  @click="goto('/pages/group/order-lists/main?status=12&historyUrl=del',1)" class="weui-cell">
						<view class="weui-cell__hd">
							<text class="iconfont icon-user-group"></text>
						</view>
						<view class="weui-cell__bd">拼团</view>
						<view class="weui-cell__ft iconfont icon-right"></view>
					</view>
					<view @click="goto('/pages/user/coupon/list/main?historyUrl=del',1)" class="weui-cell">
						<view class="weui-cell__hd">
							<text class="iconfont icon-user-receive"></text>
						</view>
						<view class="weui-cell__bd">领券</view>
						<view class="weui-cell__ft iconfont icon-right"></view>
					</view>
					<view  @click="goto('/pages/user/coupon/mylist/main?historyUrl=del',1)" class="weui-cell">
						<view class="weui-cell__hd">
							<text class="iconfont icon-user-coupon fc-6"></text>
						</view>
						<view class="weui-cell__bd">我的优惠券</view>
						<view class="weui-cell__ft iconfont icon-right"></view>
					</view>
					<view  @click="goto('/pages/user/collection/main?historyUrl=del',1)" class="weui-cell">
						<view class="weui-cell__hd">
							<text class="iconfont icon-user-collection"></text>
						</view>
						<view class="weui-cell__bd">我的收藏</view>
						<view class="weui-cell__ft iconfont icon-right"></view>
					</view> -->
			
			
			<!-- <view class="ulist bg-f mtb10">
				<view class="ulist-group ptb8 plr5">
					
				<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="checkSupplier('/pages/supplier/index/main',data.user)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-supplier"></text>
								</view>
								<view class="item-name">供货商中心</view>
							</view>
						</view>
					</myform>
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" @callBack="checkZtd('/pages/ztd/index/main',data.user)">
						<view slot="content">
							<view class="ulist-item">
								<view class="item-icon">
									<text class="iconfont icon-user-ziti"></text>
								</view>
								<view class="item-name">自提点</view>
							</view>
						</view>
					</myform>
					
				</view>
			</view> -->
		</view>
	</view>
</template>

<script>
	import "./index.css";
	import dxNavClass from "doxinui/components/nav-class/nav-class"
	import cashLists from "@/components/cashLists.vue"
	export default {
		components:{dxNavClass,cashLists},
		data() {
			return {
				formAction: '/shop/user',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				ruleform:{},
				vaildate:{},
				cashLists:[
					{
						cover:'k7ymNQP51P.jpg',
						name:'桶装水5桶1',
						getCoupon:{
							abstract:''
						},
						created_at:'2021-07-21 09:40:55',
						isEx:'0'
					},
					{
						cover:'k7ymNQP51P.jpg',
						name:'桶装水5桶2',
						getCoupon:{
							abstract:''
						},
						created_at:'2021-07-21 09:40:55',
						isEx:'1'
					},
					{
						cover:'k7ymNQP51P.jpg',
						name:'桶装水5桶3',
						getCoupon:{
							abstract:''
						},
						created_at:'2021-07-21 09:40:55',
						isEx:'1'
					},
					{
						cover:'k7ymNQP51P.jpg',
						name:'桶装水5桶4',
						getCoupon:{
							abstract:''
						},
						created_at:'2021-07-21 09:40:55',
						isEx:'0'
					}
				]
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
		components: {}
	}
</script>
