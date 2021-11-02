<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view class="pb60" v-if="data.show">
			<view class="show_banner bg-f">
				<view class="banner_swiper">
					<myswiper :data="data.covers" :tbPadding="0" :lrPadding="0" :bdR="0"></myswiper>
				</view>
				<view class="member">
					<swiper vertical autoplay circular interval="3000" class="member_swiper">
						<swiper-item v-for="(item,index) in memeber" :key="index" class="items">
							<view class="item">
								<view class="avatar">
									<image class="head" :src="item.headerPic"></image>
								</view>
								<view class="name">{{item.name.split('')[0]}}{{item.name.split('')[1]}}**刚刚查看了该产品</view>
							</view>
						</swiper-item>
					</swiper>
				</view>
			</view>
			<view class="countdown-row main-bg fc-white">
				<view class="left flex-middle">
					<text class="iconfont icon-pro-time pr5"></text>
					<image class="img" mode="widthFix" src="/static/time-limit.png"></image>
				</view>
				<view class="countdown flex-middle">
					<view class="text pr3 fs-13">距离结束还剩</view>
					<tui-countdown :time="timeList" bcolor="#fff" bgcolor="#fff" colonColor="#fff" color="#d30802"
					 :height="32" :width="36"></tui-countdown>
				</view>
			</view>
			<view class="show_info pt20 plr15 bg-f" v-if="data.product">
				<view class="name fs-17 fw-bold">{{data.product.name}}</view>
				<view class="count fs-12 flex-between flex-baseline lh-1 mt10">
					<view class="left fc-9 flex-baseline">
						<view class="price pr5">￥<text class="fs-24">{{data.product.price}}</text></view>
						<view class="price_old num pr10" v-if="data.product.new_price">￥{{data.product.new_price}}</view>
						<view class="sale" v-if="data.product.self_num || data.product.self_num_">已售
							<text class="Arial">{{data.product.self_num + data.product.self_num_}}</text>份</view>
					</view>
					<view class="right fc-4">
						<text class="dxi-icon dxi-icon-praise-fill main-color pr5 fs-11"></text>
						<text class="Arial">424</text>
						<text class="txt">人喜欢</text>
					</view>
				</view>
				<view class="labelG ptb15">
					<dx-tag size="small" myclass="mr5" hollow v-for="item in data.product.getTag" >#{{item}}</dx-tag>
				</view>
			</view>
			<view class="show_content mt12">
				<dx-tabs :tabs="[
					{value: 0,name: '商家信息'},
					{value: 1,name: '图文详情'},
					{value: 2,name: '购买须知'},
					{value: 3,name: '精选推荐'}
				]" :height="100" selectedColor="#333" color="#999" sliderBgColor="#d30802" :sliderWidth="60" :size="30" :selectedSize="30"></dx-tabs>
				<!-- <view class="show_tabs">
					<view class="item cur">商家信息</view>
					<view class="item">图文详情</view>
					<view class="item">购买须知</view>
					<view class="item">精选推荐</view>
				</view> -->
				<view id="show01" class="show_content_sec show_content_sec01">
					<view class="flow">
						<view class="type">团购券</view>
						<view class="path">立即抢购</view>
						<view class="path">商家确认</view>
						<view class="path">到店使用</view>
					</view>
					<view class="sec_title">
						<view class="name">资源信息</view>
					</view>
					<view class="sec-chunk" @click="location(merchant.location_x,merchant.location_y,merchant.address)">
						<view class="name fs-15 fw-bold">{{merchant.company_name}}</view>
						<view class="row">
							<view class="icon dxi-icon dxi-icon-time"></view>
							<view class="txt">营业时间：
							<text class="Arial">{{merchant.start_hour}}-{{merchant.end_hour}}</text></view>
						</view>
						<view class="row">
							<view class="icon dxi-icon dxi-icon-location"></view>
							<view class="txt">{{merchant.address}} | 距您步行<text class="Arial">20km</text>以上</view>
						</view>
					</view>
				</view>
				<view id="show02" class="show_content_sec show_content_sec02">
					<u-parse :content="data.product.content" />
				</view>
				<view id="show03" class="show_content_sec show_content_sec03">
					<view class="sec_title">
						<view class="name">购买须知</view>
						<view class="tagG">
							<view class="tag">随时预约</view>
							<view class="tag">不可退</view>
						</view>
					</view>
					<view class="content">
						<view class="item">
							<view class="ltxt">有效期</view>
							<view class="rtxt">2021.10.20 至 2021-12-31 23:59:59 </view>
						</view>
						<view class="item">
							<view class="ltxt">单次使用</view>
							<view class="rtxt">每次使用1张</view>
						</view>
						<view class="item">
							<view class="ltxt">适用范围</view>
							<view class="rtxt">全场通用</view>
						</view>
						<view class="item">
							<view class="ltxt">退改说明</view>
							<view class="rtxt">特惠产品，下单成功后，商家不支持退改，敬请见谅 </view>
						</view>
						<view class="item">
							<view class="ltxt">使用说明</view>
							<view class="rtxt">1、下单成功后您将会获得订单二维码或验证码，请截屏妥善保存。<br>2、到店后主动向服务员出示该二维码，服务员扫码验证使用</view>
						</view>
						<view class="item">
							<view class="ltxt">注意事项</view>
							<view class="rtxt">1、不兑现、不找零，店内无包间，仅限堂食，不提供餐前外带，餐毕未吃完可打包，打包费详情请咨询商家；<br>2、用户不可同时享受商家其他优惠，酒水饮料等问题，请致电商家咨询，以商家反馈为准使用优惠券购买付款，有效期以优惠券规则为准；<br>3、停车位收费标准：详情咨询商家，<br>4、发票问题请询问商家，消费高峰期可能需要等位</view>
						</view>
					</view>
				</view>
				<view id="show04" class="show_content_sec show_content_sec04">
					<view class="sec_title">
						<view class="name">精选推荐</view>
					</view>
					<proLists :data="lists"></proLists>
				</view>
			</view>
			<view id="show_footer" v-if="data.product.fclass != 84 && ruleform.pay_method == 1 && data.product.fclass != 109 && data.product.fclass != 110 ">
				<view class="left plr8">
					<button class="btn-item" hover-class="none" @click="goto('/pages/index/main',2)">
						<view class="icon dxi-icon dxi-icon-home2"></view>
						<view class="txt">首页</view>
					</button>
					<button class="btn-item share" hover-class="none" open-type="contact">
						<view class="icon iconfont icon-pro-service"></view>
						<view class="txt">客服</view>
					</button>
					<button class="btn-item share" hover-class="none" @click="collent = !collent">
						<view :class="['icon iconfont',collent==true?'icon-pro-collent-fill main-color':'icon-pro-collent']"></view>
						<view class="txt">收藏</view>
					</button>
				</view>
				<view class="right">
					<view class="rnavG">
						<button hover-calss="none" class="item" @click="$refs.shareProduct.shareBtn = true">分享好友</button>
						<!-- <button hover-calss="none" class="item" @click="toBuy">立即抢购</button> -->
						<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="toBuy" :myclass="item" title="立即购买"></myform>
					</view>
				</view>
			</view>
		</view>
		<sharePro :data="data" :headimgurl="headimgurl" ref="shareProduct"  :qrcodeFilePath="qrcodeFilePath" :posterFilePath="posterFilePath" v-if="show"></sharePro>
	</view>
</template>

<script>
	import tuiCountdown from "xiaozhu/uniapp/thorui/components/countdown/countdown";
	import dxTag from "doxinui/components/tag/tag"
	import dxTabs from "doxinui/components/tabs/tabs"
	import proLists from "@/components/proLists";
	import "@/components/gaoyia-parse/parse.css";
	import uParse from '@/components/gaoyia-parse/parse.vue'
	import sharePro from "@/components/poster/sharePro.vue";
	export default {
		components: {tuiCountdown,dxTag,dxTabs,proLists,uParse,sharePro},
		data() {
			return {
				formAction: '/shop/product/show',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				lists:[{
					cover:'/static/fenxiaobg.jpg',
					name:'「新品焖锅」低至42.9元，秒131元『凯苑名菜』双人超值套餐',
					newPrice:'42.9',
					price:'131'
				},{
					cover:'/static/fenxiaobg.jpg',
					name:'「新品焖锅」低至42.9元，秒131元『凯苑名菜』双人超值套餐',
					newPrice:'42.9',
					price:'131'
				}],
				memeber:[{
					headerPic:'/static/fenxiaobg.jpg',
					name:'东信科技'
				},{
					headerPic:'/static/fenxiaobg.jpg',
					name:'广勇商务'
				}],
				collent: false,
				merchant: {},
				ruleform: {
					brand: '',
					pay_method: 1
				},
				vaildate: {},
				show:false,
				data: this.formatData(),
				num: 1,
				showEvaluate: false,
				keepAlive: false,
				showType: '',
				intoView: '',
				shareDiag: false,
				product_cover:[],
				rule_type: 0,
				vaildate:{},
				fingerprint: '',
				posterFilePath: '',
				proposterBg: '',
				tempFilePath: '',
				qrcodeFilePath: '',
				headimgurl: '',
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
			this.data.query.proid = this.data.product.id;
			return this.shareSource(this, this.data.product.name);
		},
		onLoad(options) {
			this.rule_type = wx.getStorageSync('role_type');
			this.num = 1;
			this.ruleform.pay_method = 1;
			if (options.user_id) wx.setStorageSync('card_user_id', options.user_id);
			this.ajax();
		},
		methods: {
			changeBrand(val){
				this.ruleform.brand = val;
			},
			rechargeYaji() {
				setTimeout(() => {
					this.postAjax('/shop/user/ready-recharge-yaji', {
						amount: this.data.product.price * this.num,
						type: 0,
						brand: this.ruleform.brand,
						num: this.num
					}).then(msg=>{
						if (msg.data.status == 2) {
							this.goto("/pages/user/yaji/pay_center/main?order_no=" + msg.data.order_no);
						}
					});
				}, 50)
			},
			toBuyPackage() {
				this.goto("/pages/package/left_num/main?id=" + this.data.package.id);
			},
			ajax() {
				this.getAjax(this).then(msg => {
					this.merchant = msg.product.takeMerchant.userInfo;
					this.setTitle(msg.product.name);
					if (msg.product.video_url) {
						this.getQQvideoLink(msg.product.video_url, data => {
							msg.product.video_url = data.url;
							console.log(msg.product.video_url);
						});
					}
					if(msg.product.product_cover){
						this.product_cover = msg.product.product_cover.split(",");
					}
					if(msg.package){
						this.ruleform.pay_method = 2;
					}
					let poster_cover = msg.product.poster_cover ? this.getSiteName + '/upload/images/product/' + msg.product.poster_cover :
						msg.product.firstCover
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
			addCart() {
				this.$store.state.mutations.cart = true;
				this.$refs.productInfo.showType = 'addCart';
				//uni.setStorageSync('refreshUrl',"/pages/user/cart/main");
				this.updateUrl(["/pages/user/cart/main","/pages/index/main","/pages/user/index/main",'/pages/discount/index/index','/pages/index-phone/main']);
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
			toBuy() {
				//押金
				if (this.data.product.fclass == 105) {
					this.rechargeYaji();
				} else {
					this.$refs.productInfo.showType = '';
					if (this.data.productInfo.length > 0) {
						this.$refs.productInfo.toggleInfoDiag();
					} else {
						this.$refs.productInfo.addCartAjax({
							id: this.data.product.id,
							num: this.num,
							is_info: 0
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
			},
			change: function(e) {
				this.num = e.value
			},
		}
	}
</script>
<style>
@import "./index.css";
</style>