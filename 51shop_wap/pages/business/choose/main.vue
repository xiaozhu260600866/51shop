<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<div class="business">
				<!-- <div class="now-item" v-if='merchant &&merchant.name'>
					<div class="now-box bg-f m10 p15 text-center bdr6">
						<p class="fs-12 fc-9 mb5">当前社区</p>
						<div class="head">
							<image class="img" :src="merchant.heder_img"></image>
						</div>
						<p class="name fs-15 fc-3 lh-20 mtb10">{{ merchant.name }}</p>
						<p class="address fs-13 fc-6">地址：{{ merchant.community.address }}</p>
					</div>
				</div> -->
				<div class="business-list">
					<div class="list-item p15 bd-be bgf" v-for="v in data.lists.data">
						<div class="info-left mr10">
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
							<div class="dx-btn dx-btn-xs plr20 bdr15 dx-btn-orange" @click="callBack(v)">选择</div>
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
		</div>
	</view>
</template>

<script>
	import filterKm from '@/components/filterKm';
	export default {
		data() {
			return {
				formAction: '/wapindex?show_index=1',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				product_id: '',
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
			this.getMyAddress(this,msg=>{
				this.ajax();
			});
		},
		components: { filterKm },
		methods: {
			callBack(res) {
				var pages = getCurrentPages();
				var prevPage = pages[pages.length - 2]; //上一个页面
				console.log(prevPage);
				prevPage.options.merchant_user_id = res.id;
				prevPage.options.merchant_user_name = res.userInfo.company_name;
				prevPage.onLoad(prevPage.options);
				wx.navigateBack({})

			},
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
<style>
@import url("index.css");
</style>