<template>
	<view>
		<!-- <button type="primary" @tap="shareFc()">生成海报</button> -->
		<!-- 图片展示由自己实现 -->
		<QSPopup ref="popup">
			<view class="flex_column">
				<view class="backgroundColor-white">
					<image :src="posterImage || ''" mode="widthFix" class="posterImage"></image>
				</view>
				<view class="flex_row marginTop2vh">
					<button class="plr30" type="primary" size="small" @tap.prevent.stop="saveImage()">保存图片</button>
					<!-- <button class="plr15" type="info" size="small" @tap.prevent.stop="shareDiv = false">关闭</button> -->
				</view>
			</view>
		</QSPopup>
		<!-- 画布 -->
		<view class="hideCanvasView">
			<canvas class="hideCanvas" id="default_PosterCanvasId" canvas-id="default_PosterCanvasId" :style="{width: (poster.width||10) + 'px', height: (poster.height||10) + 'px'}"></canvas>
		</view>
	</view>
</template>

<script>
	import QSPopup from '@/components/js_sdk/QS-popup.vue';
	import _app from '@/components/js_sdk/QuShe-SharerPoster/QS-SharePoster/app.js';
	import {
		getSharePoster
	} from '@/components/js_sdk/QuShe-SharerPoster/QS-SharePoster/QS-SharePoster.js';
	export default {
		props: ["data", "posterFilePath", "proposterBg", "qrcodeFilePath", "fingerprint","headimgurl"],
		components:{QSPopup},
		data() {
			return {
				shareBtn: false,
				shareDiv: true,
				getSiteName: this.getSiteName(),
				poster: {},
				posterImage: '',
				canvasId: 'default_PosterCanvasId',
				count: 0,
				getUser:{
					name:'东信科技-梅',
					headerPic:'/static/fenxiaobg.jpg',
					posterFilePath:'/static/qrcode-ad.jpg',
					qrcode:'/static/fenxiaobg.jpg'
				},
			}
		},
		methods: {
			
			async shareFc() {
				console.log("posterFilePath",this.posterFilePath)
				
				let _this = this;
				try {
					this.count++;
					_app.log('准备生成:' + new Date())
					const d = await getSharePoster({
						_this: _this, //若在组件中使用 必传
						canvasType: '2d',
						type: 'testShareType',
						formData: {
							//访问接口获取背景图携带自定义数据
						},
						posterCanvasId: _this.canvasId, //canvasId
						delayTimeScale: 20, //延时系数
						background: {
							height: 10,
							width: 10,
						},
						setCanvasWH({
							bgObj
						}) {
							_this.poster = bgObj
						},
						drawArray({
							bgObj,
							type,
							bgScale,
							setBgObj,
							getBgObj
						}) {
						return [
						// 产品图
						{
							type: 'image',
							id: 'productImage',
							url: _this.getUser.posterFilePath,
							dx: 0,
							dy: 0,
							serialNum: 0,
							infoCallBack(imageInfo) {
								let width;
								let height;
								if (imageInfo.width > imageInfo.height) {
									width = imageInfo.width > 700 ? 700 : imageInfo.width;
									height = width / imageInfo.width * imageInfo.height;
								} else {
									height = imageInfo.height > 700 ? 700 : imageInfo.height;
									width = height / imageInfo.height * imageInfo.width;
								}
								if (width < 500) {
									width = 500;
									height = width / imageInfo.width * imageInfo.height;
								}
								let addHeight = 140;
								let addWidth = 640;
								let addRatio = addHeight / addWidth
								// if (addHeight < 150) addHeight = 150;
								// if (addHeight > 250) addHeight = 250;
								setBgObj({
									width,
									height: height + width*addRatio,
								});
								return {
									dWidth: width,
									dHeight: height,
									addHeight: addHeight,
									addRatio: addRatio,
								}
							}
						},
						// 背景色
						{
							type: 'custom',
							serialNum: 0,
							allInfoCallback({
								drawArray
							}) {
								const productImage = drawArray.find(item => item.id === 'productImage')
								console.log(5555555555,productImage.dHeight,productImage.addHeight)
								console.log(6666,getBgObj().width,productImage.dWidth,productImage.addRatio)
								console.log(7777,getBgObj().height,productImage.dHeight)
								return {
									setDraw(Context) {
										Context.setFillStyle('white');
										Context.setGlobalAlpha(1);
										Context.fillRect(0, productImage.dHeight, productImage.dWidth, productImage.dWidth*productImage.addRatio);
										Context.setGlobalAlpha(1);
									}
								}
							}
						},
						// 分享人头像
						{
							type: 'image',
							url: _this.getUser.headerPic,
							id:'userHead',
							allInfoCallback({
								drawArray
							}) {
								const productImage = drawArray.find(item => item.id === 'productImage')
								const addHeight = productImage.dWidth*productImage.addRatio;
								return {
									dx: 0,
									dy: productImage.dHeight + addHeight*0.28,
								}
							},
							infoCallBack(imageInfo) {
								let scale = getBgObj().width * 0.115 / imageInfo.height;
								return {
									circleSet: {
										x: imageInfo.width * scale / 2,
										y: getBgObj().width * 0.115 / 2,
										r: getBgObj().width * 0.115 / 2
									}, // 圆形图片 , 若circleSet与roundRectSet一同设置 优先circleSet设置
									dWidth: imageInfo.width * scale, // 因为设置了圆形图片 所以要乘以2
									dHeight: getBgObj().width * 0.115,
								}
							}
						},
						// 分享人名称
						{
							type: 'text',
							text: _this.getUser.name,
							color: '#434343',
							serialNum: 4,
							id: 'userName',
							allInfoCallback({
								drawArray
							}) {
								const productImage = drawArray.find(item => item.id === 'productImage')
								const addHeight = productImage.dWidth*productImage.addRatio;
								return {
									size: getBgObj().width * 0.035,
									dx: getBgObj().width * 0.18,
									dy: productImage.dHeight + addHeight*0.49,
								}
							}
						},
						// 推荐
						{
							type: 'text',
							text: '推荐',
							color: '#999',
							serialNum: 4,
							id: 'txt',
							allInfoCallback({
								drawArray
							}) {
								const productImage = drawArray.find(item => item.id === 'productImage')
								const addHeight = productImage.dWidth*productImage.addRatio;
								return {
									size: getBgObj().width * 0.03,
									dx: getBgObj().width * 0.18,
									dy: productImage.dHeight + addHeight*0.7,
								}
							}
						},
						// 长按识别二维码
						{
							type: 'text',
							text: '长按识别二维码',
							color: '#333',
							serialNum: 4,
							id: 'txt',
							textAlign: 'right',
							allInfoCallback({
								drawArray
							}) {
								const productImage = drawArray.find(item => item.id === 'productImage')
								const addHeight = productImage.dWidth*productImage.addRatio;
								// const headWidth = drawArray.find(item => item.id === 'userHead')
								return {
									size: getBgObj().width * 0.026,
									dx: getBgObj().width - getBgObj().width * 0.17-30,
									dy: productImage.dHeight + addHeight*0.52,
								}
							}
						},
						// 跟我一起赚赚赚
						{
							type: 'text',
							text: '跟我一起赚赚赚',
							color: '#333',
							serialNum: 4,
							id: 'txt',
							textAlign: 'right',
							allInfoCallback({
								drawArray
							}) {
								const productImage = drawArray.find(item => item.id === 'productImage')
								const addHeight = productImage.dWidth*productImage.addRatio;
								// const headWidth = drawArray.find(item => item.id === 'userHead')
								return {
									size: getBgObj().width * 0.026,
									dx: getBgObj().width - getBgObj().width * 0.17-30,
									dy: productImage.dHeight + addHeight*0.66,
								}
							}
						},
						// 二维码
						{
							type: 'image',
							url: _this.getUser.qrcode,
							id:'qrocode',
							allInfoCallback({
								drawArray
							}) {
								const productImage = drawArray.find(item => item.id === 'productImage')
								const addHeight = productImage.dWidth*productImage.addRatio;
								return {
									dx: getBgObj().width- getBgObj().width * 0.17 - 15,
									dy: productImage.dHeight + addHeight * 0.16,
								}
							},
							infoCallBack(imageInfo) {
								return {
									dWidth: getBgObj().width * 0.17,
									dHeight: getBgObj().width * 0.17,
								}
							}
						}]
					}
				})
				_app.log('海报生成成功, 时间:' + new Date() + '， 临时路径: ' + d.poster.tempFilePath)
				this.posterImage = d.poster.tempFilePath;
				this.$refs.popup.show();
				this.shareBtn = false;
				} catch (e) {
					_app.hideLoading();
					_app.showToast(JSON.stringify(e));
					console.log(JSON.stringify(e));
				}
			},
			offCanvas(){
				this.$refs.popup.hide()
			},
			saveImage() {
				// #ifndef H5
				
				uni.saveImageToPhotosAlbum({
					filePath: this.posterImage,
					success(res) {
						console.log("gohere");
						_app.showToast('保存成功');
					},
					fail:res=>{
						console.log(this.posterImage)
						console.log(this.poster)
						console.log(res);
					}
				})
				// #endif
				// #ifdef H5
				_app.showToast('保存了');
				// #endif
			},
			share() {
				// #ifdef APP-PLUS
				_app.getShare(false, false, 2, '', '', '', this.posterImage, false, false);
				// #endif

				// #ifndef APP-PLUS
				_app.showToast('分享了');
				// #endif
			},
			hideQr() {
				this.$refs.popup.hide()
			}
		}
	}
</script>

<style>
	.hideCanvasView {position: relative;}
	.hideCanvas {position: fixed;top: -99999upx;left: -99999upx;z-index: -99999;}
	.flex_row_c_c {display: flex;flex-direction: row;justify-content: center;align-items: center;}
	.modalView {width: 100%;height: 100%;position: fixed;top: 0;left: 0;right: 0;bottom: 0;opacity: 0;outline: 0;transform: scale(1.2);perspective: 2500upx;background: rgba(0, 0, 0, 0.6);transition: all .3s ease-in-out;pointer-events: none;backface-visibility: hidden;z-index: 999;}
	.modalView.show {opacity: 1;transform: scale(1);pointer-events: auto;}
	.flex_column {display: flex;flex-direction: column;}
	.backgroundColor-white {background-color: white;}
	.border_radius_10px {border-radius: 10px;}
	.padding1vh {padding: 1vh;}
	.posterImage {width: 70vw;}
	.flex_row {display: flex;flex-direction: row;}
	.marginTop2vh {margin-top: 2vh;}
	
	
	/* 分享按钮组 */
	.pshare-dig {position: fixed;width: 100%;background: #fff;left: 0;bottom: 0;z-index: 100;border-radius: 30px 30px 0 0;}
	.pshare-dig .pshare-group {background: #F3F3F5;display: flex;padding: 40px 10%;border-radius: 30px 30px 0 0;}
	.pshare-group .group-item {width: 100%;text-align: center;background: transparent;}
	.pshare-group .group-item .iconfont {font-size: 50px;line-height: 60px;}
	.pshare-group .group-item .words {font-size: 14px;color: #666;padding-top: 10px;line-height: 20px;}
	.pshare-dig .cancel-btn {height: 50px;line-height: 50px;color: #666;text-align: center;font-size: 15px;}
	.poster {display: flex;align-items: center;justify-content: center;}
</style>
