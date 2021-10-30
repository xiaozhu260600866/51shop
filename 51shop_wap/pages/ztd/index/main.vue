<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<view class="dis-header">
				<view class="dis-top">
					<view class="quit" @click="lgout">退出</view>
				</view>
				<view class="dis-info">
					<view class="head-img">
						<img class="img" :src="data.user.headerPic">
					</view>
					<view class="name">{{ data.user.userInfo.name ? data.user.userInfo.name : data.user.nickname }}</view>
				</view>
			</view>
			<view class="sup-sec">
				<view class="sec-group" @click="goto('/pages/ztd/order/list/main?status=5',1)">
					<view class="icon"><span class="iconfont icon-heads-order"></span></view>
					<view class="name">待取货订单</view>
				</view>
				<view class="sec-group" @click="goto('/pages/ztd/order/list/main?status=9',1)">
					<view class="icon"><span class="iconfont icon-heads-order-yes"></span></view>
					<view class="name">完成订单</view>
				</view>
				<view class="sec-group" @click="goto('/pages/ztd/commission/main',1)">
					<view class="icon"><span class="iconfont icon-heads-achievement"></span></view>
					<view class="name">我的收益</view>
				</view>
				<!-- <view class="sec-group">
					<view class="icon"><span class="iconfont icon-heads-account"></span></view>
					<view class="name">查看流水</view>
				</view> -->
			</view>
			<view class="sup-account bg-f ptb12">
				<view class="name fs-14 fc-7 text-center">帐户余额</view>
				<view class="flex-center flex-middle" @click="goto('/pages/ztd/domoney/main',1)">
					<view class="price fs-16">￥<span class="fs-30">{{data.price_a}}</span></view>
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
		
		
			
			<!-- <div class="dis-header main-bg plr15 pb15">
				<div class="item ptb15">今日结算：<span class="Arial">{{ data.order_count }}</span>笔</div>
				<div class="item pb15">可提现佣金（元）</div>
				<div class="num flex-between">
					<p class="n-price">{{ data.price_a }}</p>
					<div class="nav fs-14" @click="goto('/pages/ztd/domoney/main',1)">提现</div>
					<view class="Utop-quit" @click="lgout">退出</view>
				</div>
			</div>
			<div class="stuff-sec">
				<div class="sec-group" @click="goto('/pages/ztd/order/list/main?status=5',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon01.jpg'" />
					<p class="p">订单管理</p>
					<p class="num fs-14 fc-6"><span class="fs-15 pr5 Arial">{{ data.order_count }}</span>笔</p>
				</div>
				<div class="sec-group" @click="goto('/pages/ztd/commission/main',1)">
					<image class="img" :src="getSiteName+'/images/site/dis-icon11.png'" />
					<p class="p">佣金明细</p>
					<p class="num fs-14 fc-6"><span class="fs-15 pr5 Arial">{{ data.price_a }}</span>元</p>
				</div>
			</div> -->
		</div>
	</view>
</template>

<script>
	import "@/public/css/uindex.css";
	import "./index.css";
	export default {
		data() {
			return {
				formAction: '/ztd/index',
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
			
		},
		methods: {
			lgout(){
				this.updateUrl(["/pages/user/cart/main","/pages/index/main","/pages/user/index/main",'/pages/discount/index/index','/pages/index-phone/main']);
				this.getConfirm("是否确认好退出",msg=>{
					this.postAjax("/ztd/login-out").then(msg=>{
						if(msg.data.status == 2){
							this.goto("/pages/ztd/login/main");	
						}
					});
				});
			},
			selectRes(){
				this.ajax();
			},
			ajax() {
				this.getAjax(this, {dateSelect:this.selectType}).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
