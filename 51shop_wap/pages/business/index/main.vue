<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<view class="dis-header">
				<view class="dis-top flex-between">
					<view class="quit" @click="logout">退出</view>
				<!-- <div class="date-search"><selectSimple :data="selectData" v-model="selectType" @callBack="selectRes"></selectSimple></div> -->
				</view>
				<view class="dis-info">
					<view class="head-img">
						<img class="img" :src="data.user.headerPic">
					</view>
					<view class="name">{{ data.user.userInfo.name ? data.user.userInfo.name : data.user.nickname }}</view>
				</view>
			</view>
			
			<view class="sup-sec">
				<view class="sec-group" @click="goto('/pages/business/order/list/main?dateSelect=year&searchMerchantType=3',1)">
					<view class="icon"><span class="iconfont icon-heads-order"></span></view>
					<view class="name">订单管理</view>
				</view>
				<view class="sec-group" @click="goto('/pages/business/order/mylist/main?dateSelect=year',1)">
					<view class="icon"><span class="iconfont icon-heads-achievement"></span></view>
					<view class="name">我的收益</view>
				</view>
				<view class="sec-group" @click="goto('/pages/business/order/cancel/main',1)">
					<view class="icon"><span class="iconfont icon-heads-cancel"></span></view>
					<view class="name">核销订单</view>
				</view>
				<view class="sec-group" @click="goto('/pages/business/product/lists/index',1)">
					<view class="icon"><span class="iconfont icon-heads-code"></span></view>
					<view class="name">我的商品</view>
				</view>
				<view class="sec-group"  @click="goto('/pages/business/commission/main',1)">
					<view class="icon"><span class="iconfont icon-intergral-record"></span></view>
					<view class="name">我的帐户</view>
				</view>
				<view class="sec-group" @click="goto('/pages/user/info/main',1)">
					<view class="icon"><span class="iconfont icon-user-integral"></span></view>
					<view class="name">商家资料</view>
				</view>
			</view>
			
			<view class="sup-account bg-f ptb12">
				<view class="name fs-14 fc-7 text-center">帐户余额</view>
				<view class="flex-center flex-middle">
					<view class="price fs-16" @click="goto('/pages/business/domoney/main',1)">￥<span class="fs-30">{{data.price_a}}</span></view>
					<view class="iconfont icon-heads-eyes-open pl10 fs-18" v-if="seeStatus == true" @click="seeStatus = false"></view>
					<view class="iconfont icon-heads-eyes-close pl10 fs-18" v-if="seeStatus == false" @click="seeStatus = true"></view>
				</view>
				<view class="ac-group">
					<view class="ac-row">
						<view class="label">今日收益</view>
						<view class="value" v-if="seeStatus == true">￥<span>{{data.order_today_sum}}</span></view>
						<view class="ellipsis" v-if="seeStatus == false">*****</view>
					</view>
					<view class="ac-row">
						<view class="label">昨日收益</view>
						<view class="value" v-if="seeStatus == true">￥<span>{{data.order_yesterday_sum}}</span></view>
						<view class="ellipsis" v-if="seeStatus == false">*****</view>
					</view>
					<view class="ac-row">
						<view class="label">本月收益</view>
						<view class="value" v-if="seeStatus == true">￥<span>{{data.order_thismonth_sum}}</span></view>
						<view class="ellipsis" v-if="seeStatus == false">*****</view>
					</view>
				</view>
			</view>
			
			<!-- <div class="dis-header main-bg ptb20">
				<div class="quit-nav lh-30 plr20 fc-white fs-12 bd-d bdr5" @click="logout">退出</div>
				<div class="item" @click="goto('/pages/business/order-list/main',1)">收款{{ data.order_count }}笔，合计</div>
				<div class="num">
					<span class="fs-16 pr5">￥</span>{{ data.price_a }}
				</div>
				<div class="Anav fs-14 lh-30" @click="goto('/pages/business/domoney/main',1)">提现</div>
				<div class="date-search"><selectSimple :data="selectData" v-model="selectType" @callBack="selectRes"></selectSimple></div>
			</div>
			<div class="stuff-sec">
				<div class="sec-group" @click="goto('/pages/business/order/list/main?dateSelect=year&searchMerchantType=3',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon04.jpg'" />
					<p class="p">订单管理</p>
				</div>
				<div class="sec-group" @click="goto('/pages/business/order/mylist/main?dateSelect=year',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon09.png'" />
					<p class="p">我的收益</p>
				</div>
				<div class="sec-group" @click="goto('/pages/business/order/cancel/main',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon02.jpg'" />
					<p class="p">核销订单</p>
				</div>
				<div class="sec-group" @click="goto('/pages/business/product/lists/index',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon12.png'" />
					<p class="p">我的商品</p>
				</div>
				<div class="sec-group" @click="goto('/pages/business/record-list/main',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon07.png'" />
					<p class="p">提现记录</p>
				</div>
				 <div class="sec-group" @click="goto('/pages/user/info/main',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon01.jpg'" />
					<p class="p">商家资料</p>
				</div>
				
				<div class="sec-group" @click="goto('/pages/business/coupon/cancel/main',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon10.png'" />
					<p class="p">核销优惠券</p>
					</a>
				</div>
				<div class="sec-group" @click="goto('/pages/business/coupon/lists/main',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon11.png'" />
					<p class="p">发布优惠券</p>
				</div>
				<div class="sec-group" @click="goto('/pages/business/product/lists/main',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon08.png'" />
					<p class="p">发布商品</p>
				</div>
			</div> -->
		</div>
	</view>
</template>

<script>
	import selectSimple from "xiaozhu/uniapp/components/selectSimple";
	export default {
		data() {
			return {
				formAction: '/merchant',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				selectType:'toDay' ,
				selectData: [
					{ label: '今日', value: 'toDay' },
					{ label: '本月', value: 'thisMonth' },
					{ label: '本年', value: 'thisYear' },
				],
				seeStatus: true,
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
		onLoad(options) {
			this.ajax();
		},
		components:{
			selectSimple
		},
		methods: {
			logout(){
				this.getConfirm("是否确认退出",msg=>{
					this.postAjax("/user/lgout").then(msg=>{
						if(msg.data.status == 2){
							this.updateUrl(["/pages/user/cart/main","/pages/index/main","/pages/user/index/main",'/pages/discount/index/index','/pages/index-phone/main']);
							this.goto("/pages/user/login/main");	
						}
					});
				});
			},
			selectRes(){
				this.ajax();
			},
			ajax() {
				this.getAjax(this, {dateSelect:this.selectType}).then(msg => {
					this.setTitle(msg.merchantName);
				});
			}
		}
	}
</script>
<style>
@import url("index.css");
@import url("@/public/css/uindex.css");
</style>