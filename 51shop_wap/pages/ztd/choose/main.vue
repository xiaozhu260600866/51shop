<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<div class="title-search bg-f pro-search wap-top-fixed">
				<div class="search-input">
					<div class="linput"><weui-input v-model="ruleform.content" type="text" name="content" placeholder="请选择自提/配送点"></weui-input></div>
					<button @click="data.nextPage = 1;ajax()" class="dx-btn dx-btn-red ml10 mr0 mt3">搜索</button>
				</div>
			</div>
			<div class="business">
				<!-- <div class="now-item" v-if='ztd &&ztd.name'>
					<div class="now-box bg-f m10 p15 text-center bdr6">
						<p class="fs-12 fc-9 mb5">当前自提/配送点</p>
						<div class="head" @click="openImg(ztd.headerPic)">
							<image class="img" :src="ztd.headerPic"></image>
						</div>
						<p class="name fs-15 fc-3 lh-20 mtb10">{{ ztd.userInfo.company_name }}</p>
						<p class="address fs-13 fc-6" v-if="ztd.getAddress">地址：{{ ztd.getAddress.address }}</p>
					</div>
				</div> -->
				<div class="business-list">
					<div class="list-item p15 bd-be bgf" v-for="v in data.lists.data">
						<div class="info-left mr10" @click="openImg(v.headerPic)">
							<img class="img" :src="v.headerPic" />
						</div>
						<div class="info-con">
							<p class="CItem Atitle fs-15 nowrap lh-20">{{ v.userInfo.company_name }}</p>
							<p class="people fs-12 fc-6 mt5">地址：{{ v.userInfo.address }}</p>
						</div>
						<div class="num ml15 text-center">
							<p class="fs-12 fc-6 flex-center lh-22 mb5" v-if="v.location">距离 
								<span class="distance Arial">
									<filterKm v-model="v.location" v-if="v.location"></filterKm>
								</span>
							</p>
							<div class="dx-btn dx-btn-xs plr20 bdr15 dx-btn-orange" @click="openDiag(v)">选择</div>
						</div>
					</div>
				</div>
				<!-- <div class="list-item p10 bd-be bg-f" v-for="v in data.lists.data">
					<div class="info-left mr10">
						<image class="img" :src="v.headerPic" />
					</div>
					<div class="info-con pl5">
						<p class="CItem Atitle fs-15 nowrap lh-20">{{ v.userInfo.company_name }}</p>
						<p class="fs-13 fc-6  lh-20" v-if="v.userInfo.address">{{ v.userInfo.address }}</p>
						<div :class="['fc-9','flex',v.userInfo.address? '' : 'mt10']">
							<p class="fs-12 lh-16">距离</p>
							<p class="fs-12 lh-16 distance Arial pl3">
								<filterKm v-model="v.location" v-if="v.location"></filterKm>
							</p>
						</div>
					</div>
					<div @click="callBack(v)"><button class="dx-btn plr15 dx-btn-green main-bg">选择</button></div>
				</div> -->
			</div>
			<view class="choose-diag">
				<diag :open="openDiv" top="10" ref="diag">
					<div slot="content">
						<div class="choose-box" v-if='detail && detail.name'>
							<div class="head" @click="openImg(detail.headerPic)">
								<image class="img" :src="detail.headerPic"></image>
							</div>
							<p class="name fs-16 fc-3 lh-20 mt10">名称{{ detail.userInfo.company_name }}</p>
							<p class="address fs-15 fc-6 mt5" v-if="detail.userInfo">地址：{{ detail.userInfo.address }}</p>
							<p class="location fs-15 fc-6 flex-middle mt5">距离：<filterKm v-model="detail.location" v-if="detail.location"></filterKm></p>
						</div>
					<!-- 	<view class="choose-select">
							<weui-input v-model="ruleform.sendType" label="送货方式" name="sendType" changeField="value" type="radio"
							 dataKey="sendTypeArr" :radioType="true"></weui-input>
						</view>
						<view class="choose-type flex mtb10">
							<view class="Ctype" v-for="v in detail.deliver_type">{{v}}</view>
							
						</view> -->
						<div class="btn-group flex-center mt15">
							<div class="nav dx-btn dx-btn-lg dx-btn-green mlr5"  @click="finish(data.query.shipping ==1 ? '自提' : '送货上门')">确定</div>
						</div>
					</div>
				</diag>
			</view>
		</div>
	</view>
</template>

<script>
	import "./index.css";
	import filterKm from '@/components/filterKm';
	export default {
		data() {
			return {
				formAction: '/chooseZtd',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				openDiv: false,
				getSiteName: this.getSiteName(),
				product_id: '',
				detail:{},
				ztd:{},
				sendTypeArr:[
					{label:'自提',value:'自提'},
					{label:'送货上门',value:'送货上门'},
				],
				ruleform: {content:''},
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
		onLoad(options){
			this.product_id = options.product_id;
			this.ztd = uni.getStorageSync('ztd');
			this.getMyAddress(this,msg=>{
				this.ajax();
			});
		},
		components: { filterKm },
		methods: {
			openDiag(detail){
				this.detail = detail;
				if(!Array.isArray(this.detail.deliver_type)){
					this.detail.deliver_type = detail.deliver_type.split(",");
				}
				
 				this.$refs.diag.thisDiag = true;
			},
			chanel(){
				this.$refs.diag.thisDiag = false;
			},
			openImg(url){
				uni.previewImage({
					current: url, // 当前显示图片的http链接
					urls: [url] // 需要预览的图片http链接列表
				})
			},
			finish(value){
				let deliver_type_ = value;
				this.callBack(this.detail,value);
			},
			callBack(res,value) {
				res.deliver_type_ = value;
				uni.setStorageSync('ztd', res);
				var pages = getCurrentPages();
				var prevPage = pages[pages.length - 2]; //上一个页面
				console.log(prevPage);
				prevPage.options.merchant_user_id = res.id;
				prevPage.options.deliver_type_ = value;
				prevPage.options.merchant_user_name = res.userInfo.company_name;
				prevPage.onLoad(prevPage.options);
				wx.navigateBack({})

			},
			ajax() {
				this.getAjax(this,{name:this.ruleform.content}).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
