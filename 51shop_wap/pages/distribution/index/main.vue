<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view>
			<view class="dis-header">
				<view class="head-img">
					<image class="img" :src="distribution.heder_img"></image>
				</view>
				<view class="dis-info fc-white">
					<view class="name fs-18">{{ distribution.name}}</view>
					<!-- <view class="phone fs-16 mt10 Arial">{{ distribution.phone}}</view> -->
				</view>
			</view>
			<view class="dis-count">
				<view class="top flex-between flex-middle plr12">
					<view class="left text-center">
						<view class="txt">可提现金额(元)</view>
						<view class="num fs-24 ">0</view>
					</view>
					<view class="right">
						<dx-button type="primary" size="small" round>去提现</dx-button>
					</view>
				</view>
				<view class="bottom flex-middle plr12 ptb20">
					<view class="row w-b65">
						<text class="txt">累计收益(含未到账)</text>
						<text class="num pl5 fs-20">0</text>
					</view>
					<view class="row w-b35">
						<text class="txt">累计订单</text>
						<text class="num pl5 fs-20">0</text>
					</view>
				</view>
			</view>
			<view class="dis-my">
				<view class="dis-item">
					<view class="title">
						<text class="txt">我的团队</text>
						<text class="Arial">(0)</text>
						<text class="dxi-icon dxi-icon-right fs-13 pl3 fc-9"></text>
					</view>
					<view class="con">
						<view class="name">邀请达人</view>
						<view class="txt">收益平均提升<text class="Arial">40%</text></view>
					</view>
				</view>
				<view class="dis-item">
					<view class="title">
						<text class="txt">我的客户</text>
						<text class="Arial">(0)</text>
						<text class="dxi-icon dxi-icon-right fs-13 pl3 fc-9"></text>
					</view>
					<view class="con">
						<view class="name">锁定客户</view>
						<view class="txt">获取长期收益</view>
					</view>
				</view>
			</view>
			<view class="dis-sec m12">
				<view class="title pb12 fs-16">玩转达人</view>
				<view class="sec-box">
					<view class="sec-item">
						<image class="icon" :src="getSiteName+'/images/wap/dis-icon01.png'"></image>
						<view class="info">
							<view class="name">达人学院</view>
							<view class="txt">充分了解达人知识</view>
						</view>
					</view>
					<view class="sec-item">
						<image class="icon" :src="getSiteName+'/images/wap/dis-icon02.png'"></image>
						<view class="info">
							<view class="name">邀请达人</view>
							<view class="txt">收益平均提升<text class="Arial">40%</text></view>
						</view>
					</view>
					<view class="sec-item">
						<image class="icon" :src="getSiteName+'/images/wap/dis-icon03.png'"></image>
						<view class="info">
							<view class="name">邀请客户</view>
							<view class="txt">锁定顾户长期收益</view>
						</view>
					</view>
				</view>
			</view>
			<!-- <view class="sup-sec">
				<view class="sec-group" @click="goto('/pages/distribution/order-list/main?dateSelect=today&status=-1&dis_type=0',1)">
					<view class="icon_img"><image class="img" :src="getSiteName+'/images/wap/c2.png'"></image></view>
					<view class="name">分销订单</view>
				</view>
				<view class="sec-group" @click="goto('/pages/distribution/order-list/main?dateSelect=today&status=-1&dis_type=1',1)">
					<view class="icon_img"><image class="img" :src="getSiteName+'/images/wap/B24.jpg'"></image></view>
					<view class="name">业务订单</view>
				</view>
				<view class="sec-group" @click="goto('/pages/distribution/commission/index',1)">
					<view class="icon_img"><image class="img" :src="getSiteName+'/images/wap/c1.png'"></image></view>
					<view class="name">分销收益</view>
				</view>
				<view class="sec-group" @click="goto('/pages/distribution/team/main',1)">
					<view class="icon_img"><image class="img" :src="getSiteName+'/images/wap/c4.png'"></image></view>
					<view class="name">我的团队</view>
				</view>
				<view class="sec-group" @click="goto('/pages/distribution/client/main',1)">
					<view class="icon_img"><image class="img" :src="getSiteName+'/images/wap/c3.png'"></image></view>
					<view class="name">我的客户</view>
				</view>
				<view class="sec-group" @click="goto('/pages/distribution/qrcode/main',1)">
					<view class="icon"><span class="iconfont icon-user-qrcode"></span></view>
					<view class="name">我的二维码</view>
				</view>
				<button class="sec-group m0 plr0 bg-f" open-type="share">
					<view class="icon_img"><image class="img" :src="getSiteName+'/images/wap/share.png'"></image></view>
					<view class="name">vip注册</view>
				</button>
				<view class="sec-group" @click="goto('/pages/distribution/ad/main',1)">
					<view class="icon_img"><image class="img" :src="getSiteName+'/images/wap/A07.jpg'"></image></view>
					<view class="name">vip广告位</view>
				</view>
			</view>
			
			<view class="sup-account bg-f pt12 mb12">
				<view class="name fs-14 fc-7 text-center">总收益（已到账收益+未到账收益）</view>
				<view class="flex-center flex-middle" @click="goto('/pages/distribution/domoney/main',1)">
					<view class="price fs-16">￥<span class="fs-30">{{toFixed(count.inSumAll)}}</span></view>
				</view>
				<view class="ac-group">
					<view class="ac-row">
						<view class="label">可提现收益</view>
						<view class="value">￥<span>{{count.price_a}}</span></view>
					</view>
					<view class="ac-row">
						<view class="label">待到账收益</view>
						<view class="value">￥<span>{{count.inSumIng}}</span></view>
					</view>
					<view class="ac-row">
						<view class="label">成功提现金额</view>
						<view class="value">￥<span>{{count.outSumEd}}</span></view>
					</view>
					<view class="ac-row">
						<view class="label">提现审核中</view>
						<view class="value">￥<span>{{count.outSumIng}}</span></view>
					</view>
				</view>
			</view>
			<view class="sup-account bg-f mb12">
				<view class="ac-group">
					<view class="ac-row">
						<view class="label">今日收益</view>
						<view class="value">￥<span>{{count.order_today_sum}}</span></view>
					</view>
					<view class="ac-row">
						<view class="label">昨日到账收益</view>
						<view class="value">￥<span>{{count.order_yesterday_sum}}</span></view>
					</view>
					<view class="ac-row">
						<view class="label">本月收益</view>
						<view class="value">￥<span>{{count.order_thismonth_sum}}</span></view>
					</view>
					<view class="ac-row">
						<view class="label">上月收益</view>
						<view class="value">￥<span>{{count.order_prevmonth_sum}}</span></view>
					</view>
				</view>
			</view>
			<view class="bg-f mb12">
				<div class="plr15 pt10">
					<p class="fs-14 fc-6 lh-20">买家确认收货后，立即获得分销收益</p>
					<p class="fs-14 fc-6 lh-20">注意：可提现收益满<span class="fc-orange Arial plr3">1元</span>后才能提现</p>
				</div>
				<view class="nav_btn flex-between flex-middle plr35 pt15 pb10">
					<dx-button type="success" size="small" round @click="goto('/pages/distribution/commission/index',1)">账户明细</dx-button>
					<dx-button type="success" size="small" round @click="goto('/pages/distribution/record-list/main',1)">提现明细</dx-button>
					<dx-button type="success" size="small" round @click="goto('/pages/distribution/domoney/main',1)">一键提现</dx-button>
				</view>
			</view> -->
		</view>
	</view>
</template>

<script>
	import selectSimple from "xiaozhu/wechatapp/components/selectSimple";
	export default {
		data() {
			return {
				formAction: '/shop/dis',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				selectType: 'toDay',
				getSiteName: this.getSiteName(),
				distribution:{
					heder_img:'../../../static/fenxiaobg.jpg',
					name: '东信科技',
					phone: 13380951183,
				},
				count:{
					inSumAll: 0,
					price_a: 0,
					inSumIng: 0,
					outSumEd: 0,
					outSumIng: 0,
					order_today_sum: 0,
					order_yesterday_sum: 0,
					order_thismonth_sum: 0,
					order_prevmonth_sum: 0
				},
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
			return {
				path: "/pages/distribution/add/main?distribution="+this.data.distribution.id,
				title: "456商城",
				fail: res => {
			
				}
			}
		},
		onLoad(options) {
			//this.ajax();
		},
		components: {
			selectSimple
		},
		methods: {
			ajax() {
				this.getAjax(this,{selectType:this.selectType}).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
<style>
@import "@/public/css/uindex.css";
@import "./index.css";
</style>