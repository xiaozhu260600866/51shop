<template>
	<view v-if="shareDiv">
		<!-- 生成海报 -->
		<div class="" v-if="shareBtn == true">
			<!-- <div class="share-overlay"></div> -->
			<div class="pshare-dig">
				<div class="pshare-group">
					<button hover-class="none" class="group-item p0" open-type="share">
						<p class="iconfont icon-pshare-wechat"></p>
						<p class="words">发给好友</p>
					</button>
					<div class="group-item" @tap="shareFc()" v-if="posterFilePath != 'https://456-0310.doxinsoft.com/upload/images/articleOrder/800_null'">
						<p class="iconfont icon-pshare-save"></p>
						<p class="words">生成二维码海报</p>

					</div>
				</div>
				<div class="cancel-btn" @click="shareBtn = false">取消</div>
			</div>
		</div>
		<QSPopup ref="popup">
			<view class="flex_column">
				<view class="backgroundColor-white padding1vh border_radius_10px">
					<image :src="posterImage || ''" mode="widthFix" class="posterImage"></image>
				</view>
				<view class="flex_row marginTop2vh">
					<button  class="plr30" type="primary" size="medium" @tap.prevent.stop="saveImage()">保存图片</button>
					<button class="plr15" type="info" size="medium" @tap.prevent.stop="shareDiv = false">关闭</button>
				</view>
			</view>
		</QSPopup>
		<!-- 画布 -->
		<view class="hideCanvasView">
			<canvas class="hideCanvas" id="default_PosterCanvasId2" canvas-id="default_PosterCanvasId2"
				:style="{width: (poster.width||10) + 'px', height: (poster.height||10) + 'px'}"></canvas>
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
		props: ["data", "posterFilePath", "qrcodeFilePath", ],
		components: {
			QSPopup
		},
		data() {
			return {
				shareBtn: false,
				shareDiv: true,
				getSiteName: this.getSiteName(),
				poster: {},
				posterImage: '',
				canvasId: 'default_PosterCanvasId2',
				count: 0,
				bgImg: 'https://456.doxinsoft.com/upload/images/product/K3c3VqDnU2.jpg'
			}
		},
		methods: {
			async shareFc() {
				console.log("posterFilePath",this.posterFilePath)
				let _this = this;
				try {
					this.count++;
					_app.log('准备生成:' + new Date())
					console.log(996999,_this.canvasId);
					console.log("二维码",_this.qrcodeFilePath);
					const d = await getSharePoster({
						_this: _this, //若在组件中使用 必传
						canvasType: '2d',
						type: 'testShareType',
						posterCanvasId: _this.canvasId, //canvasId
						delayTimeScale: 20, //延时系数
						formData: {
							//访问接口获取背景图携带自定义数据
						},
						backgroundImage: _this.posterFilePath,
						drawArray: ({
							bgObj,
							type,
							bgScale
						}) => {
							console.log(9999, bgObj.width * 0.3)
							const dx = bgObj.width * 0.3;
							const fontSize = bgObj.width * 0.045;
							const lineHeight = bgObj.height * 0.04;
							//可直接return数组，也可以return一个promise对象, 但最终resolve一个数组, 这样就可以方便实现后台可控绘制海报
							return new Promise((rs, rj) => {
								rs([{
									type: 'image',
									url: _this.qrcodeFilePath,
									infoCallBack(imageInfo) {
										return {
											dWidth: imageInfo.width * 0.5,
											dHeight: imageInfo.width * 0.5,
											dx: bgObj.width * 0.03,
											dy: bgObj.height - imageInfo.width * 0.54,
										}
									}
								}]);
							})
						},
						setCanvasWH: ({
							bgObj,
							type,
							bgScale
						}) => { // 为动态设置画布宽高的方法，
							_this.poster = bgObj;
						}
					});
					// uni.canvasToTempFilePath({
					// 	canvasId: this.canvasId,
					// 	success: res => {
					// 		this.posterImage = res.tempFilePath;
					// 		_app.log('海报生成成功, 时间:' + new Date() + '， 临时路径: ' + res.tempFilePath);
					// 		this.$refs.popup.show()
					// 		this.shareBtn = false;
					// 	},
					// 	fail: err => {
					// 		console.log('生成异常', err)
					// 	}
					// })
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
					filePath:this.posterImage,
					success(res) {
						_app.showToast('保存成功');
					}
				})
				// #endif
				// #ifdef H5
				_app.showToast('保存了');
				// #endif
			},
			share() {
				// #ifdef APP-PLUS
				_app.getShare(false, false, 2, '', '', '',this.posterImage, false, false);
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
