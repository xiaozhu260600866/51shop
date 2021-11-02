<template>
	<view>
		<view class="proL" :class="{proLEdit:edit}" v-for="(item,key) in data">
			<view class="proL-box">
				<view class="proL-list" @click="goto('/pages/product/show/main?id='+item.id,1)">
					<view class="list-left">
						<image class="img" :src="item.firstCover" mode="aspectFill"></image>
					</view>
					<view class="list-right">
						<view class="Atitle fs-15 lh-1_4 wrap2 w-b100">{{ item.name}}</view>
						<view class="Bsec w-b100">
							<view class="lprice fs-12">
								<view class="price pr3">￥<text class="fs-20">{{item.price}}</text></view>
								<view class="price_old num fc-9">￥{{item.new_price}}</view>
							</view>
							<view class="rnav">
								<dx-button type="primary" size="small" round>马上抢</dx-button>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view class="edit_btn" v-if="edit">
				<dx-button size="mini" myclass="plr20" block round @click="del(item)">删除</dx-button>
				<dx-button type="success" size="mini" myclass="plr20 ml15" block round  v-if="item.status !=4" @click="goto('/pages/user/proL/create_edit/index?id='+item.id+'&category='+item.category,1)">编辑</dx-button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		props:["data","edit"],
	
		data() {
			return {
				
			}
		},
		methods: {
			thumb2(item,key){
				this.$emit("thumb",key);
				
			},
			del(item) {
				uni.showModal({
					title: '提示',
					content: '您确定要删除这个地址吗',
					success: res => {
						if (res.confirm) {
							this.postAjax('/ajax/mydel', {
								id: item.id,
								tablename: 'article_orders'
							}).then(msg => {
								if (msg.data.status == 2) {
									this.getParent(this).ajax();
								}
							});
						}
					}
				})
			},
		}
	}
</script>
<style scoped="">
.proL{background-color: #fff;position: relative;}
.proL-box{padding: 30rpx;position: relative;}
.proL-box::after{content: '';display: block;position: absolute;left: 30rpx;right: 0;bottom: 0;width: 100%;height: 2rpx;transform: scaleY(.5);background-color: #eee;}
.proL-list{display: flex;}
.proL-list .list-left{margin-right: 30rpx;}
.proL-list .list-left .img{width: 200rpx;height: 200rpx;border-radius: 12rpx;display: flex;}
.proL-list .list-right{flex: 1;display: flex;flex-wrap: wrap;align-content: space-between;}
.proL-list .Bsec{display: flex;justify-content: space-between;align-items: flex-end;line-height: 1;}
.proL-list .Bsec .lprice{display: flex;align-items: baseline;}
.proL-list .Bsec .price_old{text-decoration: line-through;}


.proLEdit{margin: 24rpx;border-radius: 12rpx;overflow: hidden;}
.proLEdit .proL-box{border-bottom: 0;}
.edit_btn{padding: 16rpx 30rpx;display: flex;justify-content: flex-end;border-top: 1rpx #eee solid;}
</style>