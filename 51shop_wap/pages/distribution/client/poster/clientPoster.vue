<template>
	<view>
		<!-- <button type="primary" @tap="shareFc()">生成海报</button> -->
		<QSPopup ref="popup">
			<view class="flex_column">
				<view class="backgroundColor-white padding1vh border_radius_10px">
					<image :src="posterImage || ''" mode="widthFix" class="posterImage"></image>
				</view>
				<view class="flex_row marginTop2vh">
					<button class="plr30" type="primary" size="medium" @tap.prevent.stop="saveImage()">保存图片</button>
					<!-- <button class="plr15" type="info" size="medium" @tap.prevent.stop="shareDiv = false">关闭</button> -->
				</view>
			</view>
		</QSPopup>
		<!-- 画布 -->
		<view class="hideCanvasView">
			<canvas class="hideCanvas" id="default_PosterCanvasId2" canvas-id="default_PosterCanvasId2"
				:style="{width: (poster.width||10) + 'px', height: (poster.height||10) + 'px'}"></canvas>
		</view>
		<!-- <image src="../../../../static/fenxiaobg.jpg"></image> -->
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
				shareBtn: true,
				shareDiv: true,
				getSiteName: this.getSiteName(),
				poster: {},
				posterImage: '',
				canvasId: 'default_PosterCanvasId2',
				bgImg: '/static/client-share-bg.png',
				detail: {
					logo: '/static/fenxiaobg.jpg',
					name: '李俊',
					position: '设计师',
					company: '蓬江区广盈商务中心',
					phone: '13380951183',
					email: '3314024140@qq.com',
					address: '江门市蓬江区建设路82号之二金山大厦10楼1001室',
					store_name: '广盈商务中心'
				}
			}
		},
		methods: {
			async shareFc() {
				console.log("posterFilePath", this.posterFilePath)
				let _this = this;
				try {
					_app.log('准备生成:' + new Date())
					console.log(996999, _this.canvasId);
					console.log("二维码", _this.qrcodeFilePath);
					const d = await getSharePoster({
						_this: _this, //若在组件中使用 必传
						canvasType: '2d',
						type: 'testShareType',
						posterCanvasId: _this.canvasId, //canvasId
						delayTimeScale: 20, //延时系数
						formData: {
							//访问接口获取背景图携带自定义数据
						},
						backgroundImage: _this.bgImg,
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
								rs([
									// 头像
									{
										type: 'image',
										url: this.detail.logo,
										id:'logo',
										infoCallBack(imageInfo) {
											let scale = bgObj.width * 0.16 / imageInfo.height;
											return {
												circleSet: {
													x: imageInfo.width * scale / 2,
													y: bgObj.width * 0.16 / 2,
													r: bgObj.width * 0.16 / 2
												}, // 圆形图片 , 若circleSet与roundRectSet一同设置 优先circleSet设置
												dWidth: imageInfo.width * scale, // 因为设置了圆形图片 所以要乘以2
												dHeight: bgObj.width * 0.16,
												dx: bgObj.width * 0.05,
												dy: bgObj.height * 0.05,
											}
										}
									},
									// 姓名
									{
										type: 'text',
										text: _this.detail.name+'邀请您关注：',
										color: '#fff',
										id: 'name',
										size: bgObj.width * 0.048,
										dx: bgObj.width * 0.32,
										dy: bgObj.height * 0.088,
									},
									// 商家名称
									{
										type: 'text',
										text: _this.detail.store_name,
										color: '#fff',
										id: 'storeName',
										size: bgObj.width * 0.048,
										dx: bgObj.width * 0.32,
										dy: bgObj.height * 0.132,
									},
									// 背景色
									{
										type: 'custom',
										serialNum: 0,
										setDraw(Context) {
											Context.setFillStyle('white');
											Context.setGlobalAlpha(1);
											Context.fillRect(bgObj.width * 0.1, bgObj.height * 0.20, bgObj.width * 0.8, bgObj.width * 0.85);
											Context.setGlobalAlpha(1);
										}
									},
									// 提示1
									{
										type: 'text',
										text: '发现一些好货，邀你一起看看',
										color: '#999',
										id: 'tips1',
										// textAlign: 'center',
										size: bgObj.width * 0.046,
										dx: bgObj.width * 0.20,
										dy: bgObj.height * 0.25,
									},
									{
										type: 'image',
										url: '/static/qrcode.jpg',
										infoCallBack(imageInfo) {
											return {
												dWidth: bgObj.width * 0.56,
												dHeight: bgObj.width * 0.56,
												dx: bgObj.width * 0.22,
												dy:bgObj.height * 0.3,
											}
										}
									},
									// 提示2
									{
										type: 'text',
										text: '微信扫码',
										color: '#999',
										id: 'tips2',
										// textAlign: 'center',
										size: bgObj.width * 0.046,
										dx: bgObj.width * 0.40,
										dy: bgObj.height * 0.72,
									},
								]);
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
			offCanvas() {
				this.$refs.popup.hide()
			},
			saveImage() {
				// #ifndef H5
				uni.saveImageToPhotosAlbum({
					filePath: this.posterImage,
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
	.hideCanvasView {
		position: relative;
	}

	.hideCanvas {
		position: fixed;
		top: -99999upx;
		left: -99999upx;
		z-index: -99999;
	}

	.flex_row_c_c {
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
	}

	.modalView {
		width: 100%;
		height: 100%;
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		opacity: 0;
		outline: 0;
		transform: scale(1.2);
		perspective: 2500upx;
		background: rgba(0, 0, 0, 0.6);
		transition: all .3s ease-in-out;
		pointer-events: none;
		backface-visibility: hidden;
		z-index: 999;
	}

	.modalView.show {
		opacity: 1;
		transform: scale(1);
		pointer-events: auto;
	}

	.flex_column {
		display: flex;
		flex-direction: column;
	}

	.backgroundColor-white {
		background-color: white;
	}

	.border_radius_10px {
		border-radius: 10px;
	}

	.padding1vh {
		padding: 1vh;
	}

	.posterImage {
		width: 70vw;
	}

	.flex_row {
		display: flex;
		flex-direction: row;
	}

	.marginTop2vh {
		margin-top: 2vh;
	}


	/* 分享按钮组 */
	.pshare-dig {
		position: fixed;
		width: 100%;
		background: #fff;
		left: 0;
		bottom: 0;
		z-index: 100;
		border-radius: 30px 30px 0 0;
	}

	.pshare-dig .pshare-group {
		background: #F3F3F5;
		display: flex;
		padding: 40px 10%;
		border-radius: 30px 30px 0 0;
	}

	.pshare-group .group-item {
		width: 100%;
		text-align: center;
		background: transparent;
	}

	.pshare-group .group-item .iconfont {
		font-size: 50px;
		line-height: 60px;
	}

	.pshare-group .group-item .words {
		font-size: 14px;
		color: #666;
		padding-top: 10px;
		line-height: 20px;
	}

	.pshare-dig .cancel-btn {
		height: 50px;
		line-height: 50px;
		color: #666;
		text-align: center;
		font-size: 15px;
	}

	.poster {
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>
