<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<view class="pro_lists">
				<view class="porducts">
					<view class="pro_item" v-for="item in data.lists.data" :key="item.id" @click="goto(item.is_group ? '/pages/group/show/main?id='+item.id :'/pages/product/show/main?id='+item.id,1)">
						<view class="cms_cover">
							<image class="img" :src="item.firstCover" mode="aspectFill"></image>
						</view>
						<view class="pro_info">
							<view class="cms_title Aname fs-16 fc-0 fw-bold nowrap">{{ item.name }}</view>
							<view class="cms_price flex-between w-b100">
								<view class="saleprice">
									<span class="fs-12">￥</span>
									<span class="fs-20">{{item.price.split(".")[0]}}</span>
									<span class="fs-12">.{{item.price.split(".")[1]}}</span>
									<span class="fs-11 fc-9 pl3" v-if="item.unit">/ {{item.unit}}</span>
								</view>
								<view class="fs-12 fc-9 pl5 lh-20" v-if="item.self_num_ > 0">已售<span class="Arial fs-12">{{ item.self_num_ }}</span>件</view>
							</view>
							<view class="pro-cart">
								<span class="iconfont icon-cart-full fs-15 fc-white"></span>
							</view>
						</view>
					</view>
					<hasMore :parentData="data"></hasMore>
				</view>
			</view>
		</div>
	</view>
</template>

<script>
	import "./index.css";
	export default {
		data() {
			return {
				formAction: '/phone.html?activity=1',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
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
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
