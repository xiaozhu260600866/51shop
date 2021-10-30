<template>
	<view class="bg-f plr10 ptb5" @click="goto('/pages/search/product/main',1)">
		<view class="searchbox bg-f7 fc-9 plr15">
			<span class="iconfont icon-search fs-13 pr5"></span>
			<span class="search-text fs-13">{{ title ? title:'搜索内容'}}</span>
		</view>
	</view>
</template>

<script>
	import tuiIcon from "xiaozhu/uniapp/thorui/components/icon/icon"
	export default {
		props: ["title"],
		components: {
			tuiIcon
		},
		data() {
			return {
				
			}
		},
		methods: {
			
		}
	}
</script>

<style>
.searchbox {display: flex;align-items: center;overflow: hidden;z-index: 999;position: relative;}
</style>