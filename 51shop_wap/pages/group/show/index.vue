<template>
	<view>
		<page :parentData="data" :formAction="formAction">	</page>
		<div v-if="data.show">
			<div class="show_banner bg-f" id="arrowTop">
				<myswiper :lists="data.covers" purl="product"></myswiper>
			</div>
			<div class="ass-price plr15 ptb10">
				<p class="price fs-28"><span class="fs-12">￥</span>{{ data.product.group_price }}</p>
				<div class="r-time" v-if="data.product.group_expire_date!='2099-12-30' || data.product.group_expire_date != '2099-12-30 00:00:00'">
					<div v-if="data.product.canGorupBuy">
						<p class="fc-white fs-12">距离活动结束还剩</p>
						<leftTime v-model="data.product.group_expire_date" type="1"></leftTime>
					</div>
					<div v-else>
						<p class="fc-white fs-18">活动已结束</p>
					</div>
				</div>
			</div>
			<div class="bg-f mb8">
				<div class="proTitle plr15 ptb10">
					<p class="fs-15">{{ data.product.name }}</p>
				</div>
				<div class="proCount fs-13 fc-9 plr15 pb10">

					<p>运费：{{ data.product.yunfei ? data.product.yunfei : '免运费' }}</p>
					<p>已售<span class="Arial">{{ data.product.self_num_  + data.product.self}}</span><span class="plr3">|</span>剩余<span class="Arial">{{ data.product.num }}</span></p>
					
				</div>
				<!-- <div class="reward mlr15 ptb10 bd-te" v-if="data.dis">
					<p class="fs-13 fc-6">一级奖励：<span class="Arial">{{ data.product.firstDisPrice.ratio }}%(￥{{ data.product.firstDisPrice.price }})</span></p>
					<p class="fs-13 fc-6">二级奖励：<span class="Arial">{{ data.product.secondDisPrice.ratio }}%(￥{{ data.product.secondDisPrice.price }})</span></p>
				</div> -->
			</div>
		
			<div class="ass-group bg-f mb8">
				<p class="lh40 fs-14 plr15" v-if="data.orderGroup.length">以下小伙伴正在发起拼团，你可以直接参与</p>
				<div class="weui-cells mt0">
					<div class="bd-be">
						
						<div class="weui-cell" v-for="v in data.orderGroup" v-if="v.leftNum">
							<div class="weui-cell__hd userImg mr10">
								<image class="img" :src="v.getUser.headerPic" />
							</div>
							<div class="weui-cell__bd assInfo">
								<p class="name fs-14">{{ v.getUser.nickname }}</p>
								<div class="mark fc-9 fs-12">
									<p class="float_l">还差<span class="fs-color">{{ v.leftNum }}</span>人成团，剩余</p>
									<p class="float_l"><leftTime v-model="v.end_date" type="0"></leftTime></p>
									<p class="float_l">结束</p>
								</div>
							</div>
							<div class="weui-cell__ft goNav ml10" @click="goto('/pages/group/group/index?order_no='+v.order_no)">
								<button class="dx-btn dx-btn-orange-o">去拼团</button>
							</div>
						</div>
					
					</div>
					<div class="weui-cell">
						<div class="weui-cell__bd">
							<p class="name fs-13">支付开团邀请1人参团，人数不足自动退款</p>
						</div>
					</div>
				</div>
				
			</div>
			<!-- 商品评价&商品详情 -->
			<div class="pro_title mt10 bg-f">
				<div class="title-group" @click="showEvaluate=false">
					<span :class="['span','fs-16',!showEvaluate ? 'cur' :'']">商品详情</span>
				</div>
				<div class="title-group" @click="showEvaluate=true">
					<span :class="['span','fs-16',showEvaluate ? 'cur' : '']">评价</span>
				</div>
			</div>
			<view class="pro-content bg-f" v-if="!showEvaluate && data.product">
				<view class="pro-con-main">
					<uParse :content="data.product.content" />
				</view>
			</view>
			<div class="evalute pb50 bd-be" v-if="showEvaluate">
				<div class="evalute-item p10 bg-f bd-be" v-for="v in data.suggestLists">
					<div class="u-info">
						<div class="u-info-box">
							<div class="u-img"><img class="img" :src="v.getUser.headimgurl"></div>
							<div class="u-name pl10">
								<p class="name lh20 fs-14">{{ v.getUser.nickname }}</p>
								<p class="fs-12 fc-6">好评</p>
							</div>
							<div class="r-time Arial fs-12 fc-9" style="width:250px">{{ v.created_at }}</div>
						</div>
					</div>
					<div class="u-con ptb15">
						<p class="p">{{ v.suggestContent }}</p>
						<div class="image-group mt5">
							<div class="img-item" v-for="(item,index2) in v.getSuggestLogo">
								<image :src="getSiteName + '/upload/images/order/'+item " mode="aspectFill"></image>
							</div>
						</div>
					</div>
					<div class="u-con ptb15 bd-te" v-if="v.suggestStatus ==2">
						<p class="fs-color fs-14 p" style="width:250px">{{ v.updated_at }}追评</p>
						<p class="p">{{ v.suggestContent2 }}</p>
						<div class="image-group mt5">
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
			<!-- 商品评价&商品详情结束 -->
			<div id="show_footer">
				<div class="left plr8">
					<button class="btn-item share bdr0" hover-class="none" @click="goto('/pages/index/main', 2)">
						<p class="iconfont icon-user-distribution"></p>
						<p class="txt">首页</p>
					</button>
					<button class="btn-item share bdr0" hover-class="none" open-type="share">
						<p class="iconfont icon-share"></p>
						<p class="txt">分享</p>
					</button>
				</div>
				<div class="right">
					<!-- <div class="r-nav">
						<myform title="加入购物车" :append="true" :ruleform="ruleform" :vaildate="vaildate" @callBack="toBuy(0,0)">
							<div slot="content">
								<div class="r-item r-item-yellow">
									<span class="fs-12">￥</span><span class="Arial">{{ data.product.price }}</span>
									单买
								</div>
							</div>
						</myform>
					</div> -->
					<div class="r-nav">
						<myform title="加入购物车" :append="true" :ruleform="ruleform" :vaildate="vaildate" @callBack="toBuy(0,1)">
							<div slot="content">
								<div class="r-item r-item-red">
									<span class="fs-12">￥</span><span class="Arial">{{ data.product.group_price }}</span>
									开团
								</div>
							</div>
						</myform>
					</div>
				</div>
			</div>
			
			</div>
			<info :productInfo="data.productInfo" @callback="infoCallBack" ref="productInfo"></info>
			
		</div>
	</view>
</template>

<script>
	import "./index.css";
	import "@/components/gaoyia-parse/parse.css";
	import uParse from '@/components/gaoyia-parse/parse.vue'
	import leftTime from "./layouts/left_time";
	import info from './layouts/info'
	export default {
		data() {
			return {
				formAction: '/shop/product/show',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				num: 1,
				showEvaluate: false,
				keepAlive: false,
				showType: '',
				intoView: '',
				shareDiag: false,
				rule_type: 0,
				otherData: {
					card_user_id: ''
				},
				ruleform:{},
				vaildate:{}
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
			if(this.data.dis) this.data.query.distribution = this.data.dis.id;
			return this.shareSource(this, this.data.product.name,1);
		},
		onLoad(options) {
			//this.getMyAddress(this);
			this.ajax();
		},
		methods: {
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			},
			addCart() {
				this.$store.state.mutations.cart = true;
				this.$refs.productInfo.showType = 'addCart';
				if (this.data.productInfo.length > 0) {
					this.$refs.productInfo.toggleInfoDiag();
				} else {
					this.$refs.productInfo.addCartAjax({
						id: this.data.product.id,
						num: this.num,
						is_info: 0
					});
				}
			},
			infoCallBack(cartNum) {
				this.data.cartNum = cartNum;
			},
			toBuy(group_id, is_group) {
				if (is_group && !this.data.product.canGorupBuy) {
					this.getError('活动已结束');
					return false;
				}
				this.$refs.productInfo.showType = '';
				if (this.data.productInfo.length > 0) {
					this.$refs.productInfo.group_id = group_id;
					this.$refs.productInfo.is_group = is_group;
					this.$refs.productInfo.toggleInfoDiag();
				} else {
					if (group_id) {
						this.$refs.productInfo.addCartAjax({
							id: this.data.product.id,
							num: this.num,
							is_info: 0,
							group_id: group_id,
							is_group: is_group
						});
					} else {
						this.$refs.productInfo.addCartAjax({
							id: this.data.product.id,
							num: this.num,
							is_info: 0,
							is_group: is_group
						});
					}
				}
			},
			addUserCollection() {
				let product_id = this.data.product.id;
				let url = "/shop/product/collection";
				if (this.data.collections) return false;
				this.postAjax(url, {
					id: product_id
				}, msg => {
					if (msg.data.status == 2) {
						this.data.collections = 1;
					}
				});
			}
		},
		components: {
			uParse,
			info,
			leftTime,
			

		}
	}
</script>
<style>

</style>
