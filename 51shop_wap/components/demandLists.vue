<template>
	<view>
		<view class="demandL" :class="{demandEdit:edit}" v-for="(item,key) in data" v-if="type == 1  && key < 3 || type ==2 && key>2 || !type && key < 1000">
			<view class="demandL-box">
				<view class="demandL-list mb10" @click="goto('/pages/demand/show/index?id='+item.id,1)">
					<view class="list-left">
						<image class="img" :src="item.getLogo" mode="aspectFill"></image>
					</view>
					<view class="list-right">
						<view class="Atitle fs-16 lh-20 fw-bold">{{ item.name}}</view>
						<view class="fs-13 mt5 nowrap fc-9"><text class="iconfont icon-hongbao fs-14 pr2 fc-red" v-if="item.red_packet"></text>{{ item.category }}</view>
						<view class="fs-15 wrap2 fc-3 mt5 lh-20">{{ item.title }}</view>
					</view>
				</view>
				
				<!-- <view class="info-right" @click="phone(item.phone)">
					<dx-button type="success" size="mini"><text class="iconfont icon-tel2 pr5"></text>拔打电话</dx-button>
				</view>
				<view class="demand-con fs-15" @click="goto('/pages/demand/show/index?id='+item.id,1)">
					<view class="mb3 content fc-2">{{ item.title }}</view>
					<dx-images :data="item.getCover" listImgW="160rpx" listImgH="160rpx" :LRPadding="-5"></dx-images>
				</view>
				<view class="fs-12 fc-9 pt3">
					<view class="row"><text class="Arial pr3">{{ item.created_at }}</text>发布</view>
					<view class="row" @click="location(item.location_x,item.location_y,item.address)"><text class="iconfont icon-location-o main-color pr8"></text>{{ item.address }}</view>
				</view> -->
				<view class="read">
					<view class="left fc-9 fs-13 flex1">
						<text class="span Arial">{{item.views}}</text>
						<text>查看</text>
						<text class="Arial pl15">{{item.created_at}}</text>
					</view>
					<view class="right">
						<view class="read-nav" :class="item.thumb ? 'fc-green': ''" @click="thumb2(item,key)">
							<text :class="['iconfont',item.thumb ? 'icon-demand-thumb-fill':'icon-demand-thumb']"></text>
							<text class="span Arial">{{item.thumbCount}}</text>
						</view>
						<view class="read-nav" @click="phone(item.phone)">
							<text class="iconfont icon-float-tel"></text>
							<text class="span">联系</text>
						</view>
					</view>
				</view>
			</view>
			<view class="edit_btn" v-if="edit">
				<dx-button size="mini" myclass="plr20" block round @click="del(item)">删除</dx-button>
				<dx-button type="success" size="mini" myclass="plr20 ml15" block round  v-if="item.status !=4" @click="goto('/pages/user/demand/create_edit/index?id='+item.id+'&category='+item.category,1)">编辑</dx-button>
			</view>
		</view>
	</view>
</template>

<script>
	import dxImages from "doxinui/components/images/images"
	export default {
		components:{dxImages},
		props:["data","edit","type"],
	
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
.demandL{background-color: #fff;position: relative;}
.demandEdit{margin: 24upx;border-radius: 12upx;overflow: hidden;}
.demandL-box{padding: 20upx 0;margin: 0 30upx;border-bottom: 2upx #eee solid;}
.demandEdit .demandL-box{border-bottom: 0;}
.demandL-list{display: flex;}
.demandL-list .list-left{margin-right: 30upx;}
.demandL-list .list-left .img{width: 200upx;height: 200upx;border-radius: 12upx;display: flex;}
.demandL-list .list-right{flex: 1;overflow: hidden;width: 100%;}

.demand-con{text-align: justify;}
.demand-con .content{overflow: hidden;text-overflow: ellipsis;display: -webkit-box;-webkit-box-orient: vertical;-webkit-line-clamp: 4;}

.demandL .read{line-height: 16px;display: flex;justify-content: space-between;align-items: center;line-height: 36upx;}
.demandL .read .right{display: flex;align-items: center;}
.demandL .read-nav{display: flex;align-items: center;color: #636363;margin-left: 20upx;height: 48upx;line-height: 48upx;padding: 0 26upx 0 20upx;border-radius: 24upx;border: 2upx #ddd solid;}
.demandL .read-nav .iconfont{padding-right: 10upx;font-size: 28upx;}
.demandL .read-nav .span{font-size: 12px;color: #636363;}

.edit_btn{padding: 16upx 30upx;display: flex;justify-content: flex-end;border-top: 1upx #eee solid;}
</style>