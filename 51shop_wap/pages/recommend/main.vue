<template>
	<view>
		<page :parentData="data" :formAction="formAction" Fbottom="bottom: 25px">
			<div slot="floatBtn">
				<floatBtn type="2" icon="icon-float-feedback" myclass="float-nav-w-green" iSize="fs-24" openType="contact" title="咨询" shadow></floatBtn>
				<div @click="phone('0750-3336666')">
					<floatBtn icon="icon-float-tel" myclass="float-nav-w-green" iSize="fs-22" title="电话" shadow></floatBtn>
				</div>
			</div>
		</page>
		<div v-if="data.show">
			<view class="tsearch">
				<view class="lcity" @click="goto('/pages/search/searchCity/main',1)">江门<text class="iconfont icon-bottom"></text></view>
				<view class="flex1">
					<searchHeader title="信息搜索,优惠券搜索,商家搜索"></searchHeader>
				</view>
			</view>
			<view class="tread bg-f">
				<view class="tread-info">总信息量<text class="Arial">6.36</text>万</view>
				<view class="tread-info">合作商家<text class="Arial">6666</text></view>
				<view class="tread-info">总浏览<text class="Arial">6666</text></view>
			</view>
			<div>
				<div id="banner" class="pt5" v-if="data.silders.data.length">
					<myswiper :lists="data.silders.data"></myswiper>
				</div>
				<swiper class="swiper bgf mb8" :vertical="vertical" :autoplay="false" :indicator-dots="true" :duration="duration"
				 :circular="circular" :style="data.location[0].length<=5 ? 'height: 100px;' : 'height: 410rpx;'" v-if="data.siteConfig.is_index == 1">
					<swiper-item v-for="(parent,parentKey) in data.location">
						<div class="Nav_btn pb10">
							<div class="item" v-for="v in parent">
								<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="v" @callBack="goto(v.url,1)">
									<div slot="content">
										<div class="Nav_img">
											<image :src="v.cover" class="img" />
										</div>
										<div class="txt">{{ v.name }}</div>
									</div>
								</myform>
							</div>
						</div>
					</swiper-item>
				</swiper>
				<view id="banner" class="ad_banner" v-if="v.data.length" v-for="(v,key) in data.adArray">
					<view class="ad_group flex" v-if="key %2 ==0">
						<view class="item" v-for="children in v.data" @click="goto(children.url,children.name == '在线商城'? 2 : 1)">
							<image class="img" :src="children.cover" mode="widthFix" />
						</view>
					</view>
					<myswiper :lists="v.data" v-if="key %2  !=0"></myswiper>
				</view>
				
				<dx-tabs-scroll :tabs="tabs" :nameSize="15" curColor="#13cc0c" borderColor="#13cc0c"></dx-tabs-scroll>
				<demand-lists></demand-lists>
				
				<div class="copyright fs-13 fc-9 m20 flex-center flex">
					<span>版权所有：</span><span class="fc-9">456在线</span>
				</div>
			</div>
		</div>
	</view>
</template>

<script>
	import "./index.css";
	import marque from "xiaozhu/uniapp/components/marque";
	import searchHeader from '@/components/searchHeader';
	import productLists from "@/components/productLists";
	import dxTabsScroll from "doxinui/components/tabs/tabs_scroll"
	import demandLists from "@/components/demandLists";
	export default {
		components: {marque,searchHeader,productLists,dxTabsScroll,demandLists},
		data() {
			return {
				formAction: '/shop/wapindex',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				height: 64, //header高度
				top: 0, //标题图标距离顶部距离
				ruleform:{},
				vaildate:{},
				tabs: [
					{value: 0,name: "今日特拼"},
					{value: 1,name: "全部"},
					{value: 2,name: "新品"},
				],
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
			this.ajax();
		},
		methods: {
			
			toProduct(item) {
				if (item.num > 0 && item.canHot) {
					return this.goto('/pages/hot/show/index?id=' + item.id, 1);
				}
			},
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		},
	}
</script>
