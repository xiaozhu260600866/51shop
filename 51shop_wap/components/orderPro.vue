<template>
	<view :class="['order_pro', myclass ? myclass : '']">
		<view class="order_info" v-for="(item,key) in data">
			<view class="order_item" @click="toProduct(item)">
				<view class="pro_img">
					<image class="img" :src="item.getProduct.firstCover" mode="aspectFill" />
				</view>
				<view class="pro_info fs-13 lh-1_5" :class="[isDetail?'flex-between':'']">
					<view class="name wrap2 fs-15">{{item.getProduct.name}}</view>
					<view class="specs mt15" :class="[isDetail?' fc-3':' fc-9']" v-if="item.is_info">已选：{{item.attribute}}</view>
				</view>
				<view class="amount fs-12 text-right pl0" v-if="isDetail">
					<view class="price fc-3">￥{{item.amount}}</view>
					<view class="num">x {{item.num}}</view>
				</view>
			</view>	
			<view class="choose-num" v-if="isBuy">
				<view class="fs-15 fw-bold">购买数量</view>
				<view class="num-input">
					<tui-numberbox v-model="item.num"></tui-numberbox>
				</view>
			</view>
		</view>
	</view>
</template>
<script type="text/javascript">
import tuiNumberbox from "xiaozhu/uniapp/thorui/components/numberbox/numberbox";
export default {
	components:{tuiNumberbox},
	props: ['data','url','myclass',"toOrder",'isBuy','isDetail'],
	data() {
		return {
			isPackage: false,
		}
	},
	methods: {
		toProduct(item){
			console.log(item);
			return this.goto(this.url+item.getProduct.id,1)
		}
	}
}

</script>
<style>
.order_info{padding: 24rpx 0;border-bottom: 1rpx #F5F5F5 dashed;}
.order_info:last-child{border-bottom: 0;}
.order_item{display: flex;}
.order_item .pro_img{padding-right: 20rpx;}
.order_item .pro_img .img{width: 200rpx;height: 200rpx;display: flex;border-radius: 16rpx;}
.order_item .pro_info{flex: 1;display: flex;min-height: 200rpx;overflow: hidden;flex-direction: column;}
.order_item .pro_info .row{display: flex;justify-content: space-between;}
.order_item .pro_info .name{text-decoration: underline;}

/* 数量 */
.choose-num{display: flex;align-items: center;justify-content: space-between;padding-top: 30rpx;}
.choose-num .num-input{display: flex;align-items: center;}
.choose-num ::v-deep .tui-numbox-icon{border: 2rpx #eee solid;color: #999;height: 56rpx;line-height: 52rpx;width: 56rpx;text-align: center;border-radius: 50%;font-size: 24rpx!important;padding: 0;}
.choose-num ::v-deep .tui-num-input{width: 80rpx;text-align: center;margin: 0 16rpx;background-color: #f7f7f7;height: 48rpx;line-height: 48rpx;border-radius: 8rpx;font-size: 28rpx!important;}
</style>