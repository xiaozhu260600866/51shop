<template>
	<view class="pb50">
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<div class="show_banner bg-f" id="arrowTop">
				<myswiper :lists="data.covers" purl="product"></myswiper>
			</div>
			<div class="pro_infor bg-f" v-if="data.product">
				<div class="pro_name fs-16 plr15 ptb12">{{data.product.name}}</div>
			</div>
			<div class="pro-type" v-if="data.productInfo.length">
				<div class="title-item plr15 lh-40"><span class="fs-15 fc-3">规格选择</span></div>
				<div class="type-list bg-f p8">
					<p :class="['Ptype fs-14 fc-8',key1 == infokey ? 'cur' : '']" v-for="(v,key1) in data.productInfo" @click="change(key1)" :key="v.id">{{ v.name }}</p>
				</div>
			</div>
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
			<div class="pro-con bg-f plr15 mt8">
				<div class="title-item lh-50 fs-16 fc-0 fw-bold">商品详情</div>
				<div class="pro-content pb15" v-if="data.product">
					<div class="pro-con-main"><u-parse :content="data.product.content" /></div>
				</div>
			</div>
		</div>
		<div id="footer-btn" >
			<div class="f-dx-btn dx-btn-red" @click="toBuy" :data-is_info="info? 1 :0 " :data-num="num">立即办理</div>
		</div>
		<div class="share-overlay" @click="shareDiag=!shareDiag" v-if="shareDiag"></div>
		<div class="share-tip" @click="shareDiag=!shareDiag" v-if="shareDiag">
			<img class="img" src="https://niu-shop-app.doxinsoft.com/images/jmb/share-tip.png" mode="widthFix">
		</div>
		<info :productInfo="data.productInfo" @callback="infoCallBack" ref="productInfo"></info>
		<shareProduct :data="data" v-if="data.show"></shareProduct>
		
		<div v-if="show" style="position:fixed;left:-600;top:0;opacity: 0;">
			 <img :src="posterFilePath" hidden @load="imageLoad" data-index="0" >
			<!--<image :src="tempFilePath" v-if="tempFilePath"  :style="'width: '+images[0].width+'px; height: '+images[0].height+'px;'"></image>-->
			<canvas :style="'width: '+width+'px; height: '+height+'px;left:-1300px'" canvas-id="firstCanvas" v-if="!tempFilePath"  ></canvas>
		</div>
	</view>
</template>

<script>
	import "./index.css";
	import "@/components/gaoyia-parse/parse.css";
	import uParse from '@/components/gaoyia-parse/parse.vue';
	import shareProduct from "@/components/share_product";
	import info from './layouts/info';
	import leftTime from "./layouts/left_time";
	const ctx = uni.createCanvasContext('firstCanvas')
	export default {
		components: { uParse,shareProduct,info,leftTime },
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
				images:[]
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
				
				this.height = this.images[0].height + 120 ;
				//脚部背景
				ctx.setFillStyle('#fff')
				ctx.fillRect(0, this.images[0].height, this.width, 120)
				
				//二维码
				ctx.drawImage(this.qrcodeFilePath, 10, this.images[0].height+10, 100, 100)
				
				//商家名称
				ctx.setFontSize(20)
				ctx.setFillStyle('#F17E2A')
				if(this.data.product.takeMerchant){
					ctx.fillText(this.data.product.takeMerchant.userInfo.company_name, 130, this.images[0].height+42)
				}else{
					ctx.fillText('456商家', 130, this.images[0].height+42)
				}
				
				//长按扫码抢优惠
				ctx.setFontSize(13)
				ctx.setFillStyle('#333')
				ctx.fillText('长按扫码抢优惠', 130, this.images[0].height+66)
				
				//商家入驻
				ctx.setFontSize(12)
				ctx.setFillStyle('#999')
				ctx.fillText('商家入驻:0750-3336666', 130,  this.images[0].height+88)
				
				// 经销商名称
				ctx.setFontSize(12)
				ctx.setFillStyle('#999')
				let nameWidth = this.data.dis.name.length*12;
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
			ajax() {
				this.getAjax(this).then(msg => {
					this.setTitle(msg.product.name);
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
			uni.getSystemInfo({
				success:res=> {
					this.width = res.windowWidth
					this.height = res.windowHeight
				   //this.height = res.screenHeight;
				}
			});
			this.ajax();
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
		
	}
</script>
