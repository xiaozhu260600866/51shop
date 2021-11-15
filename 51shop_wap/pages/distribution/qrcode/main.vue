<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view class="qrcode-box" v-if="show">
			<view class="share-qrcode-bg"><!-- :style="'background-image: url('+getSiteName+'/images/wap/share-qrcode-bg.jpg);'" -->
				<image class="img" :src="getSiteName+'/images/wap/share-qrcode-bg.jpg'" mode="widthFix"></image>
				<view class="qrcode">
					<image class="img" :src="getSiteName+'/upload/images/dis/'+data.distribution.id+'.jpg'" v-if="data.distribution" @click="previewImage(data.distribution.id+'.jpg','dis')">
				</view>
				<view class="share-nav">
					<button class="item oitem" hover-class="none" @click="shareGuide = !shareGuide">分享链接</button>
					<button class="item iitem" hover-class="none" @click="$refs.qrcodePoster.shareFc()">生成海报</button>
				</view>
			</view>
			<view class="share-guide" v-if="shareGuide">
				<view class="share-overlay" @click="shareGuide = !shareGuide"></view>
				<image class="img" :src="getSiteName+'/images/share-tip.png'" mode="widthFix"></image>
			</view>
			<image :src="share_logo" @load="imageLoad" hidden v-if="share_logo"></image>
			<canvas :style="'width: '+width+'px; height: '+height+'px;'" canvas-id="firstCanvas"  v-if="!tempFilePath"></canvas>
		</view>
		<qrcodePoster ref="qrcodePoster"></qrcodePoster>
	</view>
</template>

<script>
	import "./index.css";
	import qrcodePoster from "@/pages/distribution/qrcode/poster/qrcodePoster"
	export default {
		components:{qrcodePoster},
		data() {
			return {
				formAction: '/shop/dis',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				posterShow:true,
				show:false,
				tempFilePath:'',
				disFile:'',
				width: 375,
				height: 530,
				share_logo:'../../../static/fenxiaobg.jpg',
				images:[],
				avatarUrl:'',
				shareGuide: false,
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
			return this.shareSource(this, '商城');
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
		methods: {
			imageLoad(e){
				var $width = e.detail.width, //获取图片真实宽度
				$height = e.detail.height,
				ratio = $width / $height; //图片的真实宽高比例
				var viewWidth = this.width, //如:375 i6屏宽
				viewHeight = this.width / ratio; //计算的高度值
				
				let ctx = uni.createCanvasContext('firstCanvas')
				//console.log('w:'+viewWidth + 'h:'+viewHeight);
				//广告图
				ctx.drawImage(this.share_logo ? this.share_logo : '../../../static/fenxiaobg.jpg', 0, 0, viewWidth, viewHeight)
				
				this.height = viewHeight + 130 ;
				
				//脚部背景
				ctx.setFillStyle('#fff')
				ctx.fillRect(0, viewHeight, viewWidth, 130)
				
				//二维码
				if(this.disFile){
					ctx.drawImage(this.disFile, viewWidth-115, viewHeight+15, 100, 100)
				}
				
				//商家名称
				ctx.setFontSize(20)
				ctx.setFillStyle('#F17E2A')
				ctx.fillText(this.data.siteConfig.web_name, 15, viewHeight+55)
				
				// 头像+背景
				ctx.drawImage(this.avatarUrl, 15, viewHeight+70, 20, 20)
				ctx.drawImage('../../../static/head-bg.png', 15, viewHeight+70, 20, 20)
				
				//为你推荐
				ctx.setFontSize(13)
				ctx.setFillStyle('#333')
				ctx.fillText(this.data.distribution.name+'为你推荐', 45, viewHeight+86)
			
				ctx.draw();
				//生成图片
				setTimeout(()=>{
					uni.canvasToTempFilePath({
					canvasId: 'firstCanvas',
					success:res=> {
						console.log(res);
						this.tempFilePath = res.tempFilePath;
					},
					fail:res=>{
						console.log(res);
					}
				})
				},200)
				
			},
			poster(){
				uni.previewImage({
					current: this.tempFilePath, // 当前显示图片的http链接
					urls: [this.tempFilePath] // 需要预览的图片http链接列表
				})
			},
			ajax() {
				this.getAjax(this).then(msg => {
					wx.downloadFile({
						url:this.data.qrcode,
						success:res=>{
							this.disFile = res.tempFilePath;
							this.show = true;
						}
					});
					wx.downloadFile({
						url:this.data.distribution.heder_img,
						success:res=>{
							this.avatarUrl = res.tempFilePath;
							this.show = true;
						}
					});
					if(msg.shareLogo){
						wx.downloadFile({
							url:msg.shareLogo,
							success:res=>{
								this.share_logo = res.tempFilePath;
								this.show = true;
							}
						});
					}else{
						
					}
				});
			}
		}
	}
</script>
