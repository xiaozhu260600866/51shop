<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view>
			<image :src="cover" hidden mode="widthFix" @load="imageLoad" class="w-b100"></image>
			<image hidden :src="img" mode="widthFix"></image>
			<canvas :style="'width:'+width+'px;height:'+height2+'px;'" canvas-id="firstCanvas"></canvas>
		</view>
	</view>
</template>

<script>
	import "./index.css";
	export default {
		components: { },
		data() {
			return {
				formAction: '/shop/product/class',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				width: 0,
				height: 0,
				height2:600,
				imgWidth: 0,
				imgHeight: 0,
				bgHight:0,
				tips:'长按识别二维码查看详情',
				content:'在做弹框的时候，如果是从top、bottom出来，会自动填满宽度，但是从center出来，就只是内容大小。怎么样给父级设置宽度也没用。如果设置固定值，就不能做到自适应。尝试过用vue的方法，但是不成功，用微信小程序的方法会在编译的时候报错 ，虽然运行的时候没问题。',
				len:0,
				img: '',
				cover:'../../static/fenxiaobg.jpg',
			}
		},
		mounted() {
			uni.getSystemInfo({
				success: (res) => {
					console.log(111);
					this.width = res.windowWidth
					this.height = res.windowHeight
				}
			});
			console.log(2222,this.width,this.height);
		},
		methods: {
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			},
			imageLoad(e) {
				var $width = e.detail.width, //获取图片真实宽度
					$height = e.detail.height,
					ratio = $width / $height; //图片的真实宽高比例
				var viewWidth = this.width, //如:375 i6屏宽
					viewHeight = this.width / ratio, //计算的高度值
					bgHeight = viewHeight;
				var image = [];
				this.imgWidth = viewWidth;
				this.imgHeight = viewHeight;
				this.bgHight = bgHeight
				console.log(33333,this.imgWidth,this.imgHeight,this.bgHeight);
				
				var ctx = uni.createCanvasContext('firstCanvas')
			
				// 内容
				ctx.setFontSize(14)
				ctx.setFillStyle('#fff')
				let detail2 = this.drawArticle(ctx, this.content, this.width-50, 20, 180)
			
			
				ctx.setFillStyle('#fff')
				ctx.fillRect(0, 0, 375, this.height2)
				
				// 类别名称
				ctx.setFontSize(16)
				ctx.setFillStyle('#000')
				ctx.fillText('房产物业', 20, 55)
				
				// 发布时间
				ctx.setFontSize(12)
				ctx.setFillStyle('#999')
				ctx.fillText('2021-02-18 20:07:26发布', 20, 83)
				
				// 浏览人数
				ctx.setFontSize(12)
				ctx.setFillStyle('#999')
				ctx.fillText('6156人浏览', 20, 99)
				
				// // 二维码
				ctx.drawImage('../../static/qrcode.jpg',this.width-140, 15, 110, 110)
				
				// 浏览人数
				ctx.setFontSize(12)
				ctx.setFillStyle('#999')
				ctx.fillText(this.tips, this.width-this.tips.length*12-15, 146)
				
				// 内容
				ctx.setFontSize(14)
				ctx.setFillStyle('#333')
				let detail = this.drawArticle(ctx, this.content, this.width-50, 20, 180)
				
				// 图片
				ctx.drawImage(this.cover,15, 180+this.len*30, this.imgWidth-30,[this.imgWidth-30]/ratio)
				
				
				console.log(44444)
				
				ctx.draw();
				this.height2 = this.imgHeight+180+this.len*30;
				
				setTimeout(() => {
					uni.canvasToTempFilePath({
						canvasId: "firstCanvas",
						success: res => {
							//this.img = res.tempFilePath;
							//alert(1)
							uni.previewImage({
								current: res.tempFilePath, // 当前显示图片的http链接
								urls: [res.tempFilePath] // 需要预览的图片http链接列表
							})
						},
						fail: res => {}
					}, this)
				}, 1000);
			},
			drawArticle(ctx, kl, width, x, y) {
				let chr = kl.split('') // 分割为字符串数组
				let temp = ''
				let row = []
				for (let a = 0; a < chr.length; a++) {
					if (ctx.measureText(temp).width < width) {
						temp += chr[a]
					} else {
						a--
						row.push(temp)
						temp = ''
					}
				}
				row.push(temp)
				for (var b = 0; b < row.length; b++) {
					// 控制最多显示5行
					if (b < 5) {
						ctx.fillText(row[b], x, y + b * 30);
					}
					console.log(666, b);
				}
				var _len = row.length > 5 ? 5 :row.length
				this.len = _len;
				console.log(777, _len,b,this.len);
				return y + (_len - 1) * 30
			}
		},
		onLoad(options) {
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
