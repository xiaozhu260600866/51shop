<template>
	<view>
		<page :parentData="data" :formAction="formAction" Fbottom="bottom: 45px">
			<div slot="floatBtn">
				<div @click="goto('/pages/index/main',2)">
					<floatBtn myclass="float-nav-green" title="回首页"></floatBtn>
				</div>
			</div>
		</page>
		<div class="pb20" v-if="data.show">
			<div class="Tsearch bg-f flex pl10" :style="{height:height+'px',paddingTop:top-5 +'px'}">
				<p class="fs-17 lh-40">电信专区</p>
				<!-- <div class="flex1"><searchHeader title="搜索商品" ></searchHeader></div> -->
			</div>
			<div :style="{marginTop:height +'px'}">
				<div id="banner" class="pt5" v-if="data.silders.data.length">
					<myswiper :lists="data.silders.data"></myswiper>
				</div>
				<swiper class="swiper mb8 bg-f" :vertical="vertical" :autoplay="false" :indicator-dots="true" :duration="duration"
				 :circular="circular" :style="data.fclass[0].length<=5 ? 'height: 100px;' : 'height: 206px;'" v-if="data.fclass.length">
					<swiper-item v-if="data.fclass.length" v-for="(item,index2) in data.fclass">
						<div class="Nav_btn">
							<div class="item item_4" v-for="(v,index) in item">
								<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="v" @callBack="goto(v.url,1)" >
									<div slot="content">
										<div class="Nav_img">
											<img :src="v.cover" class="img" />
										</div>
										<div class="txt">{{v.name}}</div>
									</div>
								</myform>
							</div>
						</div>
					</swiper-item>
				</swiper>
				<contact myclass="mb8"></contact>
				<div class="ass-title p15 pr0 bgf">
					<p class="name fs-16">精选推荐</p>
					<p class="icon iconfont icon-right fc-9 fs-13 pr15"></p>
				</div>
				<productLists :data="data" type="2"></productLists>
			</div>
		</div>
	</view>
</template>

<script>
	import "./index.css";
	import searchHeader from '@/components/searchHeader';
	import productLists from "@/components/productLists";
	export default {
		data() {
			return {
				formAction: '/dianxin.html',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				orderBy: "id",
				otherData: {
					scrollTop: 0,
					searchVal: '',
					card_user_id: ''
				},
				height: 64, //header高度
				top: 0, //标题图标距离顶部距离
				ruleform:{},
				vaildate:{}
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
			searchName(e) {
				setTimeout(() => {
					if (e.mp.type == "confirm") {
						this.otherData.searchVal = e.mp.detail.value;
					}
					if (!this.otherData.searchVal) return false;
					this.goto("/pages/product/lists/main?name=" + this.otherData.searchVal, 1);
				}, 200);

			},
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		},
		components: {
			searchHeader,
			productLists
		}
	}
</script>
