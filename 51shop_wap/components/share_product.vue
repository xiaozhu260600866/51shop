<template>
	<section v-if="data.dis && shareDiv">
		<div class="pshare-float text-center bg-f pb5" v-if="data.product.is_share_distribution && data.product.poster_cover">
			<p class="ficon iconfont icon-off fs-10 fc-white" @click="shareDiv = false"></p>
			<p class="ftitle fs-12 fc-white main-bg lh-22">会员分享</p>
				<p class="fprice fs-14 main-color ptb5" v-if="data.product.firstDisPrice2">赚<span class="Arial">￥{{ data.product.firstDisPrice2.price }}</span></p>
			<p class="fprice fs-14 main-color ptb5" v-if="!data.product.firstDisPrice2">赚<span class="Arial">￥{{ data.product.firstDisPrice.price }}</span></p>
			<p class="fs-10 mtb5">Ta下单,我就赚</p>
			<div class="mlr10 fbtn"><button class="dx-btn-xs dx-btn-red fs-11" @click="loadImg(data)">立即分享</button></div>
		</div>
		<div class="pshare-btn" v-if="shareBtn == true">
			<div class="share-tip">
				<img class="img" src="https://boss.doxinsoft.com/images/wap/share-tip.png">
			</div>
			<div class="bbtn-group" style="z-index:999">
				<button class="dx-btn-lg dx-btn-red mb15 bdr30" @click="loadImg(data)">立即生成海报</button>
				<button class="dx-btn-lg dx-btn-red-o bdr30" style="border: 0;" @click="shareBtn = false">取消</button>
			</div>
		</div>

		<div v-if="show">
			<img :src="posterFilePath" hidden @load="imageLoad" data-index="0" :style="'width: '+images[0].width+'px; height: '+images[0].height+'px;'">
			<div style="opacity: 0;">
				<canvas :style="'width: '+width+'px; height: '+height+'px;left:-1300px'" canvas-id="firstCanvas2" v-if="!tempFilePath"></canvas>
			</div>
		</div>

	</section>
</template>
<script type="text/javascript">
	export default {
		props: ["data"],
		data() {
			return {
				shareBtn: false,
				images: [],
				tempFilePath: '',
				posterFilePath:'',
				show:false,
				height: 0,
				shareDiv: true,
			    getSiteName: this.getSiteName(),

			}
		},
		methods: {
			loadImg(data){
				uni.showLoading({
				    title: '加载中'
				});
				this.getParent(this).toOpen();
			},
			open(url) {
				console.log(url);
				uni.previewImage({
					current: url, // 当前显示图片的http链接
					urls: [url] // 需要预览的图片http链接列表
				})
			},
			ajax(msg) {
				console.log(msg);
				this.product = msg.product;
				this.dis = msg.dis;
				wx.downloadFile({
					url:this.getSiteName+'/upload/images/product/'+msg.product.poster_cover,
					success:res=>{
						this.posterFilePath = res.tempFilePath;
						this.show = true;
						console.log(this.posterFilePath);
						console.log(108);
					},
					fail:res=>{
						console.log(res);
					}
				});
				
			}
		},
		mounted() {
			uni.getSystemInfo({
				success: res => {
					this.width = res.windowWidth
					this.height = res.windowHeight
				}
			});
		}
	}
</script>
<style type="text/css" scoped="">
 /* 分享浮动 */
.pshare-float {position: fixed;right: 10px;bottom: 85px;border-radius: 6px;min-width: 80px;box-shadow: 0 0 10px 0 hsla(0, 6%, 58%, .6);-webkit-box-shadow: 0 0 10px 0 hsla(0, 6%, 58%, .6);-moz-box-shadow: 0 0 10px 0 hsla(0, 6%, 58%, .6);z-index: 100;}
/* .pshare-float:before {content: "";display: block;position: absolute;right: 8px;top: -12px;height: 12px;width: 1px;background-color: rgba(0, 0, 0, 0.4);} */
.pshare-float .ficon {position: absolute;top: 0px;right: 0;width: 18px;height: 18px;line-height: 18px;text-align: center;}
.pshare-float .fprice {border-bottom: 1px #EE4543 dashed;}
.pshare-float .ftitle {border-radius: 6px 6px 0 0;}
.pshare-float .fbtn .dx-btn-sm {border-radius: 14px;}
.pshare-btn {position: fixed;top: 0;bottom: 0;left: 0;right: 0;background-color: rgba(0, 0, 0, 0.8);z-index: 999;}
.pshare-btn .share-tip {position: absolute;top: 5px;right: 10px;}
.pshare-btn .share-tip .img {width: 200px;height: 93px;}
.pshare-btn .bbtn-group {position: absolute;bottom: 15px;left: 15px;right: 15px;}
</style>
