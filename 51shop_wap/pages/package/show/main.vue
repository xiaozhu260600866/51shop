<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			
			<div class="show_banner bgf" id="arrowTop">
				<myswiper2 :lists="data.covers" purl="package"></myswiper2>
			</div>
			<div class="pro_infor bgf pt10" v-if="data.product">
				<div class="pro_name fs-16 plr15">{{data.product.name}}</div>
				<div class="cms_price plr15 pt5">
					<p class="saleprice fs20 float_l">￥{{data.product.amount}}</p>
				</div>
				<div class="reward mlr15 ptb10 bd-te mt5" v-if="data.dis">
					<div v-if="!data.dis.lev_id">
						<p class="fs-13 fc-6">一级奖励：<span class="Arial"><span
								v-if="data.product.firstDisPrice.type == 0">{{ data.product.firstDisPrice.ratio }}%</span>(￥{{ data.product.firstDisPrice.price }})</span></span>
						</p>
						<p class="fs-13 fc-6">二级奖励：<span
								v-if="data.product.secondDisPrice.type == 0">{{ data.product.secondDisPrice.ratio }}%</span>(￥{{ data.product.secondDisPrice.price }})</span>
						</p>
					</div>
					
					
					<div v-for="lev in data.levs" v-if="data.dis.lev_id == lev.dis_lev_id">
						<p class="fs-13 fc-6">{{lev.name}}一级奖励：
							<span class="Arial">
								<span v-if="lev.type == 0">{{ data.product.price * lev.first_distor / 100  }}%</span>
								<span v-if="lev.type == 1">￥({{  lev.first_distor   }})</span>
							</span>
						</p>
						<p class="fs-13 fc-6">{{lev.name}}二级奖励：
							<span class="Arial">
								<span v-if="lev.type == 0">{{ data.product.price * lev.second_distor / 100  }}%</span>
								<span v-if="lev.type == 1">￥({{  lev.second_distor   }})</span>
							</span>
						</p>
					</div>
				</div>
			</div>
			<div class="pro_title mt10 bgf">
				<div class="title-item" @click="showEvaluate=false">
					<span :class="!showEvaluate ? 'cur' :''">商品详情</span>
				</div>
				<div class="title-item" @click="showEvaluate=true">
					<span :class="showEvaluate ? 'cur' : ''">评价</span>
				</div>
			</div>
			<div class="pro-content bgf" v-if="!showEvaluate && data.product">
				<div class="pro-con-main">
					<u-parse :content="data.product.content" />
				</div>
			</div>
			<div class="evalute pb50 bd-be" v-if="showEvaluate">
				<div class="evalute-item p10 bgf bd-be" v-for="v in data.suggestLists">
					<div class="u-info">
						<div class="u-info-box">
							<div class="u-img"><img class="img" :src="v.getUser.headimgurl"></div>
							<div class="u-name pl10">
								<p class="name lh20 fs-14">{{ v.getUser.nickname }}</p>
								<p class="fs-14 fc-6" v-if="v.suggestStatus == 1">好评</p>
								<p class="fs-14 fc-6" v-if="v.suggestStatus == 2">中评</p>
								<p class="fs-14 fc-6" v-if="v.suggestStatus == 3">差评</p>
							</div>
							<div class="r-time Arial fs-12 fc-9 pl10">{{ v.created_at }}</div>
						</div>
					</div>
					<div class="u-con pt10 plr15">
						<p class="p">{{ v.suggestContent1 }}</p>
						<div class="image-group">
							<div class="img-item" v-for="(item,index2) in v.getSuggestLogo">
								<image :src="getSiteName + '/upload/images/order/'+item " mode="aspectFill"></image>
							</div>
						</div>
					</div>
					<div class="u-con pb10 mlr15" v-if="v.suggestStatus ==2">
						<p class="fs-13 again mtb15 pl8 fw-bold">追评 <span class="Arial">{{ v.updated_at }}</span></p>
						<p class="p">{{ v.suggestContent2 }}</p>
						<div class="image-group">
							<div class="img-item" v-for="(item,index2) in v.getSuggestLogo2">
								<image :src="getSiteName + '/upload/images/order/'+item " mode="aspectFill"></image>
							</div>
						</div>
					</div>
				</div>
				<div class="data-con" v-if="data.suggestLists.length == 0">
					<p class="fs-12 fc-9 p10 text-center">暂无评价</p>
				</div>
			</div>
			<view class="pro_footer"></view>
			<div id="show_footer">
				<div class="left plr8">
					<button class="btn-item" hover-class="none" @click="goto('/pages/index/main',2)">
						<p class="iconfont icon-home"></p>
						<p class="txt">首页</p>
					</button>
					<button class="btn-item share" hover-class="none" @click="$refs.shareProduct.shareBtn = true">
						<p class="iconfont icon-share"></p>
						<p class="txt">分享</p>
					</button>
				</div>
				<div class="right plr8">
					<div :class="['r-item buy dx-btn-big dx-btn-red', 'w-b100']" @click="toBuy"
						:data-is_info="info? 1 :0 " :data-num="num">立即购买</div>
				</div>
			</div>
			
			<!-- 如果该商品可以购买开始 -->
			<view class="share-overlay" @click="shareDiag=!shareDiag" v-if="shareDiag"></view>
			<div class="share-tip" @click="shareDiag=!shareDiag" v-if="shareDiag">
				<img class="img" src="https://niu-shop-app.doxinsoft.com/images/jmb/share-tip.png" mode="widthFix">
			</div>
			<sharePro :data="data" :headimgurl="headimgurl" ref="shareProduct" :qrcodeFilePath="qrcodeFilePath"
				:posterFilePath="posterFilePath" v-if="show"></sharePro>

		</div>


	</view>
</template>

<script>
	import uParse from '@/components/gaoyia-parse/parse.vue'
	import sharePro from "@/components/poster/sharePro.vue";
	export default {
		components: {
			uParse,
			sharePro
		},
		data() {
			return {
				formAction: '/shop/package/show',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				num: 1,
				show: false,
				shareDiag: false,
				showEvaluate: false,
				rule_type: 0,
				posterFilePath:'',
				headimgurl:'',
				productFirstCover:'',
				qrcodeFilePath:''
				
			}
		},
		onReachBottom() {
			console.log('123');
			this.hasMore(this);
		},
		onPullDownRefresh() {
			this.data.nextPage = 1;
			this.ajax();
		},
		onShareAppMessage() {
			if (this.data.dis) this.data.query.distribution = this.data.dis.id;
			this.data.query.pid = this.data.product.id;
			return this.shareSource(this, this.data.product.name);
		},
		onLoad(options) {
			this.rule_type = wx.getStorageSync('role_type');
			this.num = 1;
			if (options.user_id) wx.setStorageSync('card_user_id', options.user_id);
			this.ajax();
		},
		methods: {
			toBuy() {
				setTimeout(() => {
					this.postAjax(this.formAction, {
						num: this.num,
						package_id: this.data.product.id
					}).then(msg => {
						if (msg.data.status == 2) {
							this.goto("/pages/package/order/buy/main?order_no=" + msg.data.order_no);
						}
					});
				}, 50)
			},
			ajax() {
				this.getAjax(this).then(msg => {
					this.setTitle(msg.product.name);
					let poster_cover = msg.product.cover ?   this.getSiteName + '/upload/images/package/' + msg.product.cover.split(",")[0] :'';
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
			},
		}
	}
</script>
<style>
@import url("./index.css");
@import url("@/components/gaoyia-parse/parse.css");
</style>