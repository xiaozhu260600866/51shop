<template>
	<view :class="['order_pro', myclass ? myclass : '']">
		<view class="order_info" v-for="(item,key) in data">
			<!-- 如果是套餐显示 -->
			<view class="order_detail pt8" @click="toProduct(item)" v-if="isPackage">
				<view class="name fs-18 lh-1_5">{{item.getProduct.name}}</view>
				<view class="specs fc-9 fs-14 mt8" v-if="item.is_info">规格：{{item.attribute}}</view>
			</view>
			<!-- 如果是商品显示 -->
			<view class="order_item" @click="toProduct(item)" v-else>
				<view class="pro_img">
					<image class="img" :src="item.getProduct.firstCover" mode="aspectFill" />
				</view>
				<view class="pro_info fs-13">
					<view class="left flex1 lh-1_4">
						<view class="name nowrap fs-15">{{item.getProduct.name}}</view>
						<view class="specs fc-9 mt5" v-if="item.is_info">{{item.attribute}}</view>
					</view>
					<view class="flex-between flex-middle">
						<view>￥{{item.amount}}</view>
						<view class="scount">小计：<text class="fc-red fs-14">￥{{toFixed(item.amount * item.num ) }}</text></view>
					</view>
				</view>
			</view>
			<view class="choose-num">
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
	props: ['data','url','myclass',"toOrder",],
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
.order_pro{padding: 0 30rpx;}
.order_info{padding: 24rpx 0;border-bottom: 1rpx #F5F5F5 dashed;}
.order_info:last-child{border-bottom: 0;}
.order_item{display: flex;}
.order_item .pro_img{padding-right: 20rpx;}
.order_item .pro_img .img{width: 160rpx;height: 160rpx;display: flex;border-radius: 6rpx;}
.order_item .pro_info{flex: 1;display: flex;min-height: 160rpx;overflow: hidden;flex-direction: column;justify-content: space-between;}
.order_item .pro_info .row{display: flex;justify-content: space-between;}

/* 数量 */
.choose-num{display: flex;align-items: center;justify-content: space-between;padding-top: 30rpx;}
.choose-num .num-input{display: flex;align-items: center;}
.choose-num ::v-deep .tui-numbox-icon{border: 2rpx #eee solid;color: #999;height: 56rpx;line-height: 52rpx;width: 56rpx;text-align: center;border-radius: 50%;font-size: 24rpx!important;padding: 0;}
.choose-num ::v-deep .tui-num-input{width: 80rpx;text-align: center;margin: 0 16rpx;background-color: #f7f7f7;height: 48rpx;line-height: 48rpx;border-radius: 8rpx;font-size: 28rpx!important;}
</style>