<template>
	<view>
		<page :parentData="data" :formAction="formAction" Fbottom="bottom: 45px">
			<div slot="floatBtn"  v-if="data.show&& data.cardSignature&& data.detail.payed_at">
				<div @click="goto('/pages/index/main',2)">
					<floatBtn myclass="float-nav-green" title="回首页"></floatBtn>
				</div>
			</div>
		</page>
		    <view class="container" v-if="data.show ">
				<view class="pay-coupon" v-if="data.cardSignature&& data.detail.payed_at">
					<view class="coupon-bg main-bg"></view>
					<view class="coupon-body">
						<view class="coupon-box">
							<view class="top plr15">
								<view class="info">
									<image class="head" :src="getSiteName + '/upload/images/wechatCard/'+data.wechatCard.logo_url"></image>
									<view class="name fs-14 fc-9 pt40">{{data.wechatCard.brand_name}}</view>
								</view>
								<view class="coupon-name">{{data.wechatCard.title}}</view>
								<view class="text-center"><dx-button type="success" myclass="plr50" @click="wechatCard">领取月饼券到卡包</dx-button></view>
								<view class="time fs-12 fc-9 ptb15">
									<text class="fc-6">可用时间：</text>
									<text class="Arial">{{data.wechatCard.begin_timestamp}}-{{data.wechatCard.end_timestamp}}</text>
								
								</view>
							</view>
							<view class="p15 ad">
								<image class="img flex w-b100 bdr3" :src="getSiteName + '/upload/images/wechatCard/'+data.wechatCard.icon_url_list" mode="widthFix"></image>
							</view>
							<dx-title name="月饼券使用须知" borderColor="#33c45d" borderWidth="40" borderR="4" nameColor="#333" nameSize="16" nameBold="bold"
							 Bline></dx-title>
							<view class="content fs-15 p15">
								<view>{{data.wechatCard.deal_detail}}</view>
							</view>
						</view>
					</view>
				</view>
				<view v-else>
					<!-- 如果是电信订单 -->
					<view class="payed-content" v-if="detail.dianxin  ||detail.fclass == 109 || detail.fclass == 110">
						<view class="con" v-if="detail.status == 3 && !detail.payed_at">
							<view class="iconfont icon-pay-yes"></view>
							<view class="content" v-if="detail.dianxin">申请成功，我们将于24小时内上门办理</view>
							<view class="content" v-if="detail.fclass == 109 || detail.fclass == 110">提交成功</view>
						</view>
						<view class="con" v-else-if="detail.status == 9 && detail.payed_at">
							<view class="iconfont icon-pay-yes"></view>
							<view class="content">支付成功</view>
						</view>
						<view v-else class="con">
							<view class="iconfont icon-pay-no"></view>
							<view class="content">支付失败</view>
						</view>
					</view>
					<view class="payed-content" v-if="detail.fclass==106 && detail.dianxin == 0">
						<view class="con" v-if="detail.status == 3 && !detail.payed_at">
							<view class="iconfont icon-pay-yes"></view>
							<view class="content">领券成功</view>
						</view>
					  
					</view>
					<view class="payed-content" v-if="detail.fclass !=106 && detail.fclass !=84 && detail.fclass !=109&& detail.fclass !=110 && detail.dianxin == 0">
						<view class="con" v-if="detail.payed_at">
							<view class="row iconfont icon-pay-yes"></view>
							<view class="row content">支付成功</view>
							<view v-if="detail.fclass==90">商家：[{{ detail.merchantName }}]</view>
						</view>
						<view class="con" v-if="!detail.payed_at">
							<view class="row iconfont icon-pay-no"></view>
							<view class="row content">支付失败</view>
						</view>
					</view>
					<view class="button">
						<view @click="goto(data.hot_productIds.length ? '/pages/hot/index2/index' :'/pages/index/main',2)"><dx-button type="success" size="lg" block>{{data.hot_productIds.length ? '福利' :'返回'}}</dx-button></view>
				
					</view>
				</view>
		    </view>
	</view>
</template>

<script>
	import "./index.css";
	import dxTitle from "doxinui/components/title/title"
	export default {
		components:{dxTitle},
		data() {
			return {
				 formAction: '/shop/order/payed',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				detail:{},
				getSiteName: this.getSiteName(),
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
			this.shareSource(this, '商城');
		},
		onLoad(options) {
			this.ajax();
		},
		methods: {
			wechatCard(){
				wx.addCard({
				      cardList: [
							this.data.cardSignature
				      ],
				      success:(res)=> {
				        this.postAjax("/shop/order/update",{give_wechatCard:1,order_no:this.detail.order_no});
				      },
				      fail(res){
				        console.log('添加失败',res);
				      },
				      complete(res){
				        console.log('添加完成', res);
				      }
				    })
			},
			ajax() {
				this.getAjax(this).then(msg => {
					this.detail = msg.detail;
				});
			}
		}
	}
</script>
