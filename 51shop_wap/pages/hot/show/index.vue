<template>
	<view class="pb50">
		
		<div v-if="data.show">
			<div class="show_banner bg-f" id="arrowTop">
				<myswiper :data="data.covers" purl="product" :tbPadding="0" :lrPadding="0" :bdR="0"></myswiper>
				<view class="status ing" v-if="data.product.num && data.product.canHot">抢购中</view>
				<view class="status ed" v-if="data.product.num == 0 || !data.product.canHot">已售磬</view>
			</div>
			<div class="pro_infor plr15" v-if="data.product">
				<view class="pro_name fs-18 fw-bold lh-24 pt10">{{ data.product.name }}</view>
				<view class="pro_mark fs-14 fc-6 lh-20 mt5 mb10" v-if="data.product.hot_remark">{{data.product.hot_remark}}</view>
				<view class="">
					<view class="saleprice flex-baseline h-30 lh-30">
						<view class="Arial fs-17 flex-baseline">
							<text class="">￥</text>
							<text class="fs-30">{{data.product.price.split(".")[0]}}</text>
							<text class="">.{{data.product.price.split(".")[1]}}</text>
						</view>
						<view class="tui-factory-price" v-if="data.product.new_price">门市价￥<text class="Arial">{{data.product.new_price}}</text></view>
					</view>
					<!-- <view class="return-price" v-if="data.dis">
						<text class="iconfont icon-hot-return"></text>
						<text>赚</text>
						<text class="Arial">￥{{data.product.firstDisPrice.price}}</text>
					</view> -->
				</view>
					
				<div class="other-info fs-13 pt5">
					<div class="info-item">已售：<span class="Arial">{{data.product.self_num_ + data.product.self}}</span></div>
					<div class="info-item">库存：<span class="Arial">{{data.product.num}}</span></div>
				</div>
			</div>
			<!-- <div class="pro-type" v-if="data.productInfo.length">
				<div class="title-item plr15 lh-40"><span class="fs-15 fc-3">规格选择</span></div>
				<div class="type-list bg-f p8">
					<p :class="['Ptype fs-14 fc-8',key1 == infokey ? 'cur' : '']" v-for="(v,key1) in data.productInfo" @click="change(key1)" :key="v.id">{{ v.name }}</p>
				</div>
			</div> -->
			<div class="business-info bg-f plr15 mt8" v-if="data.product.merchant">
				<div class="title-item lh-50 fs-16 fc-0 fw-bold">商家信息</div>
				<div class="info flex pb10">
					<p class="licon iconfont icon-location fc-red fs-22" @click="location(data.product.merchant.userInfo.location_x, data.product.merchant.userInfo.location_y, data.product.merchant.userInfo.address)"></p>
					<div class="detail plr10">
						<p class="bs-name fs-15 lh-20">{{ data.product.merchant.userInfo.name }}</p>
						<p class="address fs-13 fc-9 lh-18">{{ data.product.merchant.userInfo.address }}</p>
					</div>
					<p class="ricon iconfont icon-info-tel fc-red fs-22" @click="phone(data.product.merchant.userInfo.phone)"></p>
				</div>
			</div>
			<div class="use-box bg-f plr15 mt8">
				<div class="title-item lh-50 fs-16 fc-0 fw-bold">温馨提示</div>
				<div class="rule-list pb15 fc-6">
					<u-parse :content="data.product.remark" />
				</div>
			</div>
			<div class="pro-con bg-f plr15 mt8">
				<div class="title-item lh-50 fs-16 fc-0 fw-bold">商品介绍</div>
				<div class="index-video" v-if="data.product.video_url">
					<video class="video" :src="data.product.video_url" autoplay="true" enable-danmu danmu-btn controls></video>
				</div>
				<div class="pro-content pb15" v-if="data.product">
					<div class="pro-con-main"><u-parse :content="data.product.content" /></div>
				</div>
			</div>
			<div id="show_footer" >
				<div class="left plr8">
					<button class="btn-item" hover-class="none" @click="goto('/pages/index/main',2)">
						<p class="iconfont icon-home"></p>
						<p class="txt">首页</p>
					</button>
					<button class="btn-item" hover-class="none" open-type="contact">
						<p class="iconfont icon-pro-service-o"></p>
						<p class="txt">客服</p>
					</button>
					<button class="btn-item" hover-class="none" @click="phone(data.product.takeMerchant.userInfo.phone)" v-if="data.product.takeMerchant">
						<p class="iconfont icon-tel-o"></p>
						<p class="txt">商家电话</p>
					</button>
				</div>
				<div class="right">
					<div class="r-nav pr10" style="width: 45%;" v-if="(disCountShow || data.product.class_value.split(',')[0] !=360) && data.benefitLinkIds.length">
						<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="goto('/pages/hot/index/index?ids='+data.benefitLinkIds.join(',')+'&title=福利商城',1)" myclass="r-item r-item-red main-bg" title="福利"></myform>
					</div>
					<div class="r-nav" :style="{width: (disCountShow || data.product.class_value.split(',')[0] !=360) && data.benefitLinkIds.length?'55%':'100%'}">
						<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="toBuy"
						 :myclass="['r-item',data.product.canHot && data.product.num?'r-item-red':'r-item-default']" :title="data.product.canHot && data.product.num ? '立即购买' :'已售磬'" :data-is_info="info? 1 :0 " :data-num="num"></myform>
					</div>
					
				</div>
			</div>
		</div>
		<div class="share-overlay" @click="shareDiag=!shareDiag" v-if="shareDiag"></div>
		<div class="share-tip" @click="shareDiag=!shareDiag" v-if="shareDiag">
			<img class="img" src="https://niu-shop-app.doxinsoft.com/images/jmb/share-tip.png" mode="widthFix">
		</div>
		<info :productInfo="data.productInfo" @callback="infoCallBack" ref="productInfo"></info>
		<shareProduct :data="data" v-if="data.show"></shareProduct>
		<div v-if="show" style="position:fixed;left:-600px;top:0;opacity: 0;">
			 <img :src="posterFilePath" hidden @load="imageLoad" data-index="0" >
			<!-- <image :src="tempFilePath" v-if="tempFilePath"  :style="'width: '+images[0].width+'px; height: '+images[0].height+'px;'"></image> -->
			<canvas :style="'width: '+width+'px; height: '+height+'px;left:-1300px'" canvas-id="firstCanvas" v-if="!tempFilePath"  ></canvas>
		</div>
		<selectCity ref="selectCity" title="请选择配送区域" :ruleform="ruleform" :townArr="townArr" :cityArr="cityArr" :provinceArr="provinceArr" :areaArr="areaArr" @callBack="cityCallBack"></selectCity>
	</view>
</template>

<script>
	import "./index.css";
	import "@/components/gaoyia-parse/parse.css";
	import uParse from '@/components/gaoyia-parse/parse.vue';
	import shareProduct from "@/components/share_product";
	import info from './layouts/info';
	import leftTime from "./layouts/left_time";
		import selectCity from "@/components/selectCity.vue"
	const ctx = uni.createCanvasContext('firstCanvas')
	export default {
		components: { uParse,shareProduct,info,leftTime,selectCity },
		data() {
			return {
				formAction: '/shop/product/show',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				show:false,
				posterFilePath:'',
				tempFilePath:'',
				qrcodeFilePath:'',
				width:'',
				height:'',
				images:[],
				disCountShow:false,
				provinceArr:[],
				waterCityData:'',
				townArr:[],
				cityArr:[],
				areaArr:[],
				ruleform:{province:'广东省'},
				vaildate:{}
			}
		},
		methods: {
			open(url){
				uni.hideLoading();
				uni.previewImage({
					current: url, // 当前显示图片的http链接
					urls: [url] // 需要预览的图片http链接列表
				})
			},
			imageLoad(e) {
				 var $width = e.detail.width, //获取图片真实宽度
					$height = e.detail.height,
					ratio = $width / $height; //图片的真实宽高比例
				var viewWidth = this.width, //如:375 i6屏宽
					viewHeight = this.width / ratio; //计算的高度值
				var image = [];
				//将图片的datadata-index作为image对象的key,然后存储图片的宽高值
				image[e.target.dataset.index] = {
					width: viewWidth,
					height: viewHeight
				}
				console.log(image);
				this.images = image;
				
				//广告图
				ctx.drawImage(this.posterFilePath, 0, 0, this.width, this.images[0].height)
				this.height = this.images[0].height;
				
				//脚部背景
				// ctx.setFillStyle('#fff')
				// ctx.fillRect(0, this.images[0].height, this.width, 120)
				
				//二维码
				ctx.drawImage(this.qrcodeFilePath, 5, this.images[0].height-81, 76, 76)
				
				//商家名称
				// ctx.setFontSize(20)
				// ctx.setFillStyle('#F17E2A')
				// if(this.data.product.takeMerchant){
				// 	ctx.fillText(this.data.product.takeMerchant.userInfo.company_name, 130, this.images[0].height+42)
				// }else{
				// 	ctx.fillText('456商家', 130, this.images[0].height+42)
				// }
				
				//长按扫码抢优惠
				// ctx.setFontSize(13)
				// ctx.setFillStyle('#333')
				// ctx.fillText('长按扫码抢优惠', 130, this.images[0].height+66)
				
				//商家入驻
				// ctx.setFontSize(12)
				// ctx.setFillStyle('#999')
				// ctx.fillText('商家入驻:0750-3336666', 130,  this.images[0].height+88)
				
				// 经销商名称
				// ctx.setFontSize(12)
				// ctx.setFillStyle('#999')
				// let nameWidth = this.data.dis.name.length*12;
				//ctx.fillText(this.data.dis.name, this.width - nameWidth - 10, this.images[0].height+110)
			
				ctx.draw();
				
			},
			toOpen(){
				if(this.tempFilePath){
					this.open(this.tempFilePath);
				}else{
					setTimeout(()=>{
						uni.canvasToTempFilePath({
							canvasId: 'firstCanvas',
							success:res=> {
								console.log(res);
								this.tempFilePath = res.tempFilePath;
								this.open(this.tempFilePath);
							},
							fail:res=>{
								console.log(res);
							}
						})
					},200)
				}
			},
			toBuy() {
				//押金
				if(this.data.product.canHot && this.data.product.num ){
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
								pid:this.data.pid || 0,
								is_info: 0
							});
						}
					}
				}
			
			},
			cityCallBack(item){
				uni.setStorageSync("waterCityData",item);
				uni.setStorageSync("waterCity",item.province + item.city+ item.town + item.area);
				this.ajax();
				this.waterCityData = item;
				console.log(item);
			},
			ajax() {
				this.getAjax(this).then(msg => {
					this.setTitle(msg.product.name);
					if (msg.product.video_url) {
						this.getQQvideoLink(msg.product.video_url, data => {
							msg.product.video_url = data.url;
							console.log(msg.product.video_url);
						});
					}
					wx.downloadFile({
						url:this.getSiteName+'/upload/images/product/'+msg.product.poster_cover,
						success:res=>{
							this.posterFilePath = res.tempFilePath;
							
						},
						fail:res=>{
							console.log(res);
						}
					});
					wx.downloadFile({
						url:this.getSiteName+'/upload/images/dis/'+msg.dis.id+'_'+msg.product.id+'.jpg',
						success:res=>{
							this.qrcodeFilePath = res.tempFilePath;
							this.show = true;
						}
					});
				});
			}
		},
		onLoad(options) {
			  var pages = getCurrentPages();
			    var prevPage = pages[pages.length - 2]; //上一个页面
				console.log("prevPage",prevPage)
				if(prevPage && prevPage.data && prevPage.data.data &&prevPage.data.data.query && prevPage.data.data.query.ids){
					this.disCountShow = true;
				}else{
					this.disCountShow = false;
				}

			uni.getSystemInfo({
				success:res=> {
					this.width = res.windowWidth
					this.height = res.windowHeight
				   //this.height = res.screenHeight;
				}
			});
			if(!uni.getStorageSync("waterCity")){
					console.log("00759");
					this.$nextTick(()=>{
						this.$refs.selectCity.init();
					})
					
			}else{
				if(!this.waterCityData)this.waterCityData = uni.getStorageSync("waterCityData");
				console.log("00871");
				if(options.scene){
					setTimeout(()=>{
						this.ajax();
					},3000);
				}else{
					this.ajax();
				}
				
				
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
			//this.data.query.shareImg = this.data.product.firstCover;
			return this.shareSource(this, "456在线爆款");
		},
		
	}
</script>
