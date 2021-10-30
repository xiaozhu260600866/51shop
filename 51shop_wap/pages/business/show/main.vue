<template>
	<view class="pb60">
		<page :parentData="data" :formAction="formAction"></page>
		<view v-if="data.show">
			<view class="banner" v-if="data.swiperPicArr.length">
				<myswiper :data="data.swiperPicArr" purl="user"></myswiper>
			</view>
			<view class="block-sec top_info" :class="data.swiperPicArr.length > 0?'mt0':''">
				<view class="row fs-13 fc-3">
					<view class="left flex-middle">
						<view class="label">诚</view>
						<view>
							<text class="mr3">商家评分</text>
							<tui-rate :current="index" :hollow="true" @change="change" :size="16" active="#ff7900"></tui-rate>
						</view>
					</view>
					<view class="right">浏览量<text class="Arial">{{data.views}}</text></view>
				</view>
				<!-- <view class="row nav_group">
					<view class="nav" @click="phone(detail.phone)">商家电话：<text class="Arial">{{detail.phone}}</text></view>
					<view class="nav" @click="location(detail.location_x,detail.location_y,detail.address)">导航到这里使用现金券</view>
				</view> -->
			</view>
			<view class="block-sec busCoupon" v-if="data.lists.data.length && data.mid">
				<dx-title name="免费优惠券" borderColor="#33c45d" nameColor="#333" nameSize="16" nameBold="bold" line Lline></dx-title>
				<scroll-view class="couponBox"  scroll-y>
					<cashLists :data="data.lists.data" :type="3" :myclass="['bus-coupon',data.lists.data.length > 3?'pb50':'']" :merchant_user_id="data.detail.id"></cashLists>
				</scroll-view>
				<view class="more-bg" v-if="data.lists.data.length > 3"></view>
			</view>
			<view class="block-sec" v-if="data.product.length">
				<dx-title name="福利产品" borderColor="#33c45d" nameColor="#333" nameSize="16" nameBold="bold" line Lline></dx-title>
				<scroll-view class="proBox"  scroll-y>
					<productLists :data="data.product" :type="5" :myclass="[data.product.length > 3?'pb50':'']"></productLists>
				</scroll-view>
				<view class="more-bg" v-if="data.product.length > 3"></view>
			</view>
			<view class="block-sec detail-show">
				<dx-title name="商家简介" borderColor="#33c45d" nameColor="#333" nameSize="16" nameBold="bold" line Lline></dx-title>
				<view class="con p12 fs-15">
					<view v-if="detail.remark">{{ detail.remark }}</view>
					<image class="img" :src="getSiteName + '/upload/images/user/'+detail.pic" mode="widthFix" @click="previewImage(detail.pic,'user')"></image>
					<image class="img" :src="getSiteName + '/upload/images/user/'+v" mode="widthFix" v-for="v in detail.remarkPicArr" @click="previewImage(v,'user')"></image>
					<view class="fs-12 fc-9 text-center ptb10" v-if="detail.remark.length==0 && detail.remarkPicArr.length==0">暂无数据</view>
				</view>
			</view>
			<view class="block-sec bus-info">
				<dx-list-cell name="地址" iconName="location-fill" iconSize="18" iconColor="#33c45d"
				 @click="location(detail.location_x,detail.location_y,detail.address)">
				　　<view slot="right" class="right-box">{{ detail.address }}</view>
				</dx-list-cell>
				<!-- <dx-list-cell name="电话" iconName="tel-fill2" iconSize="18" iconColor="#33c45d" @click="phone(detail.phone)">
				　　<view slot="right" class="right-box Arial">{{ detail.phone }}</view>
				</dx-list-cell>
				<dx-list-cell name="营业时间" iconName="time-fill" iconSize="18" iconColor="#33c45d">
				　　<view slot="right" class="right-box Arial">08:30 ~ 18:00</view>
				</dx-list-cell> -->
			</view>
			<!-- <view class="block-sec code">
				<view class="title">联系我们时，请说在456在线平台看到的哦</view>
				<view class="code_group">
					<view class="money_QR flex-center">
						<image class="code" :src="data.merchantCode" @click="openImage"></image>
					</view>
					<view class="flex-center flex-middle mt8 fs-14 fc-6">
						<view class="fc-red pr5">{{detail.company_name}}</view>
						<view>专属小程序</view>
					</view>
					<view class="mt8"><dx-button type="success" size="medium" hollow round  @click="toMerchant">我是商家，我要入驻</dx-button></view>
				</view>
			</view> -->
			<view id="show_footer">
				<view class="left plr8">
					<button class="btn-item" hover-class="none" @click="goto('/pages/index/main',2)">
						<view class="iconfont icon-home"></view>
						<view class="txt">首页</view>
					</button>
					<button class="btn-item share" hover-class="none" @click="$refs.shareStore.shareBtn = true">
						<view class="iconfont icon-share"></view>
						<view class="txt">分享</view>
					</button>
				</view>
				<view class="right pr5">
					<view class="r-nav">
						<view class="r-item r-item-green" @click="phone(detail.phone)">一键拨号</view>
					</view>
					<view class="r-nav">
						<view class="r-item r-item-red" @click="location(detail.location_x,detail.location_y,detail.address)">一键导航</view>
					</view>
				</view>
			</view>
			<!-- <view class="show-footer">
				<view class="litem" @click="goto('/pages/index/main',2)">
					<view class="iconfont icon-home"></view>
					<view class="name">首页</view>
				</view>
				<view class="litem" @click="location(detail.location_x,detail.location_y,detail.address)">
					<view class="iconfont icon-location-o"></view>
					<view class="name">导航</view>
				</view>
				<view class="litem">
					<view class="iconfont icon-share" @click="$refs.shareStore.shareBtn = true"></view>
					<view class="name">分享</view>
				</view>
				<view class="right ml10" @click="phone(detail.phone)">
					<view class="ritem">一键拨号</view>
					<view class="Arial">{{detail.phone}}</view>
				</view>
			</view> -->
			<shareStore :data="data" :headimgurl="headimgurl" ref="shareStore" :qrcodeFilePath="qrcodeFilePath" :posterFilePath="posterFilePath"></shareStore>
		</view>
	</view>
</template>

<script>
	import "./index.css";
	import dxListCell from "doxinui/components/list-cell/list-cell"
	import dxTitle from "doxinui/components/title/title"
	import demandLists from "@/components/demandLists";
	import tuiRate from "xiaozhu/uniapp/thorui/components/rate/rate"
	import cashLists from "@/components/cashLists.vue"
	import productLists from "@/components/productLists";
	import shareStore from "@/components/poster/shareStore.vue";
	export default {
		components:{dxListCell,dxTitle,demandLists,tuiRate,cashLists,productLists,shareStore},
		data() {
			return {
				formAction: '/merchant/show',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				detail: {},
				index: 5
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
			if(this.data.dis) this.data.query.distribution = this.data.dis.id;
			return this.shareSource(this, this.data.detail.userInfo.company_name,1);
		},
		onLoad(options) {
			console.log("11")
			this.getMyAddress(this,msg=>{
				this.ajax();
			})
			//this.ajax();
		},
		methods: {
			openImage(){
				uni.previewImage({
					current: this.data.merchantCode, // 当前显示图片的http链接
					urls: [this.data.merchantCode] // 需要预览的图片http链接列表
				})
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
			ajax() {
				this.getAjax(this).then(msg => {
					this.detail = msg.detail.userInfo;
					this.setTitle(this.detail.company_name); 
					wx.downloadFile({
						url: poster_cover,
						success: res => {
							this.posterFilePath = res.tempFilePath;
						},
						fail: res => {
							console.log(res);
						}
					});
					console.log(msg.user.headimgurl);
					wx.downloadFile({
						url: msg.user.headimgurl,
						success: res => {
							this.headimgurl = res.tempFilePath;
						},
						fail: res => {
							console.log(res);
						}
					});
					wx.downloadFile({
						url: this.data.product.firstCover,
						success: res => {
							this.productFirstCover = res.tempFilePath;
							//this.show = true;
						},
						fail: res => {
							console.log(res);
						}
					});
					wx.downloadFile({
						url: this.data.qrcode,
						success: res => {
							this.qrcodeFilePath = res.tempFilePath;
							this.show = true;
						},
						fail: res => {
							console.log(res);
						}
					});
				});
			}
		}
	}
</script>
