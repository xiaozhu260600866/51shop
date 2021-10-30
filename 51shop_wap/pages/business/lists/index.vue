<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div class="business" v-if="data.show">
			<view class="banner" v-if="data.categoryCover.length"><myswiper :data="data.categoryCover"></myswiper></view>
			<view class="Bt_search">
				<view class="searchbox bg-f7 fc-9 plr15">
					<span class="iconfont icon-search fs-15 pr5"></span>
					<!-- <span class="search-text fs-13">搜索内容</span> -->
					<input class="search-input" type="text" v-model="data.query.name" placeholder="搜索商家" placeholder-class="pla-class" />
				</view>
				<dx-button type="success" size="medium" @click="ajax">搜索</dx-button>
			</view>
			<view v-if="data.children.length">
				<dx-nav-class :data="data.children" filedCover="getcover" myclass="mb12"  @click="changeChildren" :num="5" :isturnpage="true" :pageNum="10"></dx-nav-class>
			</view>
			<businessLists :data="data.lists.data" :otherData="otherData"></businessLists>
			<hasMore :parentData="data"></hasMore>
		</div>
	</view>
</template>

<script>
	import "./index.css";
	import dxTabsScroll from "doxinui/components/tabs/tabs_scroll"
	import businessLists from '@/components/business_lists';
	import dxNavClass from "doxinui/components/nav-class/nav-class"
	export default {
		components: {businessLists,dxTabsScroll,dxNavClass},
		data() {
			return {
				formAction: '/businessLists.html?type=all',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				otherData: {
					ANavType: 0
				},
				swiper: []
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
			this.ajax();
		},
		methods: {
			changeChildren(v){
				console.log("log");
				this.data.query.industry = v.name;
				this.ajax();
			},
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
					this.setTitle(this.data.query.industry)
					// if (msg.parent && msg.parent.cover2) {
					// 	let coverArr = msg.parent.cover2.split(",");
					// 	for (let i = 0; i < coverArr.length; i++) {
					// 		this.swiper.push({
					// 			cover: this.getSiteName + "/upload/images/product/800_" + coverArr[i]
					// 		})
					// 	}
					// }
				});
			}
		},
	}
</script>
