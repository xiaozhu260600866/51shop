<template>
	<view>
		<!-- 竖排列表 -->
		<view :class="[myclass]" v-if="order">
			<view class="tui-product-list">
				<view class="tui-product-container">
					<block v-for="(item,index) in data" :key="index" v-if="(index+1)%2!=0 || isList">
						<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="item" @callBack="goto('/pages/package/show/main?id='+item.id,1)">
							<div slot="content">
								<view class="tui-pro-item" :class="[isList?'tui-flex-list':'']" hover-class="hover" :hover-start-time="150">
									<image :src="item.firstCover" class="tui-pro-img" :class="[isList?'tui-proimg-list':'']"
									 mode="widthFix" />
									<view class="tui-pro-content">
										<view class="tui-pro-tit lh-20">{{item.name}}</view>
										<view class="dx-pro-group flex-between">
											<view class="tui-pro-price lh-22">
												<text class="tui-sale-price">￥{{item.amount}}</text>
												<text class="tui-unit" v-if="item.unit">/{{item.unit}}</text>
											</view>
											<view class="tui-pro-pay lh-16" v-if="item.self_num_ > 0 || item.self">已售{{item.self + item.self_num_}}</view>
										</view>
										<view class="buy_out" v-if="item.num <= 0">
											<image class="img" :src="getSiteName() +'/images/wap/buy_out.png'"></image>
										</view>
									</view>
								</view>
							</div>
						</myform>
					</block>
				</view>
				<view class="tui-product-container" v-if="!isList">
					<block v-for="(item,index) in  data" :key="index" v-if="(index+1)%2==0">
						<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="item" @callBack="goto('/pages/package/show/main?id='+item.id,1)">
							<div slot="content">
								<view class="tui-pro-item" :class="[isList?'tui-flex-list':'']" hover-class="hover" :hover-start-time="150">
									<image :src="item.firstCover" class="tui-pro-img" :class="[isList?'tui-proimg-list':'']"
									 mode="widthFix" />
									<view class="tui-pro-content">
										<view class="tui-pro-tit lh-20">{{item.name}}</view>
										<view class="dx-pro-group flex-between">
											<view class="tui-pro-price lh-22">
												<text class="tui-sale-price">￥{{item.amount}}</text>
												<text class="tui-unit" v-if="item.unit">/{{item.unit}}</text>
											</view>
											<view class="tui-pro-pay lh-16" v-if="item.self_num_ > 0 || item.self > 0 ">已售{{item.self_num_ + item.self}}</view>
										</view>
										<view class="buy_out" v-if="item.num <= 0">
											<image class="img" :src="getSiteName() +'/images/wap/buy_out.png'"></image>
										</view>
									</view>
								</view>
							</div>
						</myform>
					</block>
				</view>
			</view>
		</view>
		<view v-else>
			<view class="tui-product-list" v-if="type == 1  || !type">
				<view class="tui-product-container">
					<block v-for="(item,index) in data.lists.data" :key="index" v-if="(index+1)%2!=0 || isList">
						<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="item" @callBack="gotoProduct(item)">
							<div slot="content">
								<view class="tui-pro-item" :class="[isList?'tui-flex-list':'']" hover-class="hover" :hover-start-time="150">
									<image :src="item.firstCover" class="tui-pro-img" :class="[isList?'tui-proimg-list':'']"
									 mode="widthFix" />
									<view class="tui-pro-content">
										<view class="tui-pro-tit lh-20">{{item.name}}</view>
										<view class="dx-pro-group flex-between">
											<view class="tui-pro-price lh-22">
												<text class="tui-sale-price">￥{{item.price}}</text>
												<text class="tui-unit" v-if="item.unit">/{{item.unit}}</text>
											</view>
											<view class="tui-pro-pay lh-16" v-if="item.self_num_ > 0 || item.self">已售{{item.self + item.self_num_}}</view>
										</view>
										<view class="buy_out" v-if="item.num <= 0">
											<image class="img" :src="getSiteName() +'/images/wap/buy_out.png'"></image>
										</view>
									</view>
								</view>
							</div>
						</myform>
					</block>
				</view>
				<view class="tui-product-container" v-if="!isList">
					<block v-for="(item,index) in  data.lists.data" :key="index" v-if="(index+1)%2==0">
						<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="item" @callBack="gotoProduct(item)">
							<div slot="content">
								<view class="tui-pro-item" :class="[isList?'tui-flex-list':'']" hover-class="hover" :hover-start-time="150">
									<image :src="item.firstCover" class="tui-pro-img" :class="[isList?'tui-proimg-list':'']"
									 mode="widthFix" />
									<view class="tui-pro-content">
										<view class="tui-pro-tit lh-20">{{item.name}}</view>
										<view class="dx-pro-group flex-between">
											<view class="tui-pro-price lh-22">
												<text class="tui-sale-price">￥{{item.price}}</text>
												<text class="tui-unit" v-if="item.unit">/{{item.unit}}</text>
											</view>
											<view class="tui-pro-pay lh-16" v-if="item.self_num_ > 0 || item.self > 0 ">已售{{item.self_num_ + item.self}}</view>
										</view>
										<view class="buy_out" v-if="item.num <= 0">
											<image class="img" :src="getSiteName() +'/images/wap/buy_out.png'"></image>
										</view>
									</view>
								</view>
							</div>
						</myform>
					</block>
				</view>
			</view>
		
			<!-- 横排列表 -->
			<view class="pro_crosswise" v-if="type == 2">
				<block v-for="(item,key) in data.lists.data">
					<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="item" @callBack="gotoProduct(item)">
						<view slot="content" class="pro_item">
							<view class="cms_cover">
								<img class="img" :src="item.firstCover" mode="aspectFill" />
							</view>
							<view class="pro_info">
								<view class="w-b100">
									<view class="cms_title fs-15 nowrap">{{ item.name }}</view>
									<!-- <view class="cms_type">
										<span class="tag" v-for="tag in item.getTag">{{tag}}</span>
									</view> -->
									<view class="cms_city fs-13 fc-9 lh-1 mt5">
										<text class="dxi-icon dxi-icon-location-fill fs-12 pr5"></text>
										<text class="area">{{item.takeMerchant.userInfo.area}}</text>
									</view>
								</view>
								<view class="cms_price flex-end w-b100 lh-1">
									<view class="pr10 nowrap fc-9">
										<text class="fs-12 price">￥</text>
										<text class="fs-20 price">{{item.price.split(".")[0]}}</text>
										<text class="fs-12 price">.{{item.price.split(".")[1]}}</text>
									</view>
									<view class="oldprice fs-12 fc-9 pb3">￥{{item.new_price}}</view>
									<!-- <view class="fc-9 pl5 lh-20" v-if="!item.wechat_card_id">
										<span class="fs-12">已售</span>
										<span class="fs-12 Arial">{{ item.self_num_ + item.self }}</span>
										<span class="fs-10 plr3">|</span>
										<span class="fs-12">剩余</span>
										<span class="fs-12 Arial">{{item.num}}</span>
									</view> -->
								</view>
							</view>
							<view class="buy_out" v-if="item.num <= 0">
								<image class="img" :src="getSiteName() +'/images/wap/buy_out.png'"></image>
							</view>
						</view>
					</myform>
				</block>
			</view>
			<!-- 横排列表2,只传入data -->
			<view class="pro_crosswise" v-if="type == 4">
				<view class="porducts">
					<view v-for="(item,key) in data">
						<myform :ruleform="ruleform" :vaildate="vaildate" :append="true" :data="item" @callBack="gotoProduct(item)">
							<view slot="content" class="pro_item">
								<view class="cms_cover">
									<img class="img" :src="item.firstCover" mode="aspectFill" />
								</view>
								<view class="pro_info">
									<view class="w-b100">
										<view class="cms_title Aname fs-17 fc-0">{{ item.name }}</view>
										<view class="cms_type">
											<span class="tig" v-for="tag in item.getTag">{{tag}}</span>
										</view>
									</view>
									<view class="cms_price flex-between w-b100">
										<view class="flex1 pr10 nowrap fc-9">
											<span class="fs-12 price">￥</span>
											<span class="fs-20 price">{{item.price.split(".")[0]}}</span>
											<span class="fs-12 price">.{{item.price.split(".")[1]}}</span>
										</view>
										<view class="fc-9 pl5 lh-20" v-if="!item.wechat_card_id">
											<span class="fs-12">已售</span>
											<span class="fs-12 Arial">{{ item.self_num_ + item.self }}</span>
											<!-- <span class="fs-10 plr3">|</span>
											<span class="fs-12">剩余</span>
											<span class="fs-12 Arial">{{item.num}}</span> -->
										</view>
									</view>
								</view>
								<view class="buy_out" v-if="item.num <= 0">
									<image class="img" :src="getSiteName() +'/images/wap/buy_out.png'"></image>
								</view>
							</view>
						</myform>
					</view>
				</view>
			</view>
			
			<!-- 大图产品 -->
			<view class="pro_big-list pt10" v-if="type == 3">
				<view class="pro-item p10 pt0" v-for="(item,key) in data.lists.data">
					<view class="bg-f pro-box bdr6" @click="gotoProduct(item)">
						<view class="pro-cover">
							<image class="img" :src="item.firstCover" mode="widthFix"></image>
							<view class="status ing" v-if="item.num && item.canHot">抢购中</view>
							<view class="status ed" v-if="item.num == 0 || !item.canHot">已售磬</view>
						</view>
						<view class="plr10 ptb8 pb10">
							<view class="pro-title fs-16 fw-bold lh-24">{{ item.name }}</view>
							<view class="pro-mark fs-13 fc-6 lh-20 wrap2 mt5 mb10" v-if="item.hot_remark">{{item.hot_remark}}</view>
							<view class="pro-price flex-between">
								<view class="saleprice flex-baseline h-30">
									<view class="Arial fs-15 flex-baseline">
										<text class="">￥</text>
										<text class="fs-22">{{item.price.split(".")[0]}}</text>
										<text class="">.{{item.price.split(".")[1]}}</text>
									</view>
									<view class="tui-factory-price" v-if="item.new_price">门市价￥<text class="Arial">{{item.new_price}}</text></view>
									<view class="fs-12 fc-9 pl5">销量<text class="Arial pl3">{{item.self_num_ + item.self}}</text></view>
								</view>
								<!-- <view class="return-price" v-if="data.dis">
									<text class="iconfont icon-hot-return"></text>
									<text>赚</text>
									<text class="Arial">￥{{item.firstDisPrice.price}}</text>
								</view> -->
								<!-- <view class="dx-btn dx-btn-red dx-btn-sm bdr15 m0 plr15" v-if="item.num && item.canHot">立即抢购</view>
								<view class="nobuy-btn dx-btn dx-btn-default fc-white dx-btn-xs plr15 bdr14" v-if="item.num == 0 || !item.canHot">已售磬</view> -->
							</view>
						</view>
					</view>
				</view>
			</view>
			<!-- 大图产品传入 -->
			<view class="pro_big-list pt10" v-if="type == 5">
				<view class="pro-item p10 pt0" v-for="(item,key) in data">
					<view class="bg-f pro-box bdr6" @click="gotoProduct(item)">
						<view class="pro-cover">
							<image class="img" :src="item.firstCover" mode="widthFix"></image>
							<view class="status ing" v-if="item.num && item.canHot">抢购中</view>
							<view class="status ed" v-if="item.num == 0 || !item.canHot">已售磬</view>
						</view>
						<view class="pt10">
							<view class="pro-title fs-16 fw-bold lh-24 nowrap">{{ item.name }}</view>
							<view class="pro-mark fs-13 fc-6 lh-20 wrap2 mt5 mb10" v-if="item.hot_remark">{{item.hot_remark}}</view>
							<view class="pro-price flex-between mt5">
								<view class="saleprice flex-baseline h-30">
									<view class="Arial fs-15 flex-baseline">
										<text class="">￥</text>
										<text class="fs-22">{{item.price.split(".")[0]}}</text>
										<text class="">.{{item.price.split(".")[1]}}</text>
									</view>
									<view class="tui-factory-price" v-if="item.new_price">门市价￥<text class="Arial">{{item.new_price}}</text></view>
									<view class="fs-12 fc-9 pl5">销量<text class="Arial pl3">{{item.self_num_ + item.self}}</text></view>
								</view>
								<view class="dx-btn dx-btn-red dx-btn-sm bdr15 m0 plr15" v-if="item.num && item.canHot">购买</view>
							</view>
						</view>
					</view>
				</view>
			</view>
		
		</view>
	</view>
</template>
<script>
	export default{
		props:["data",'type','order','myclass'],
		data() {
			return {
				ruleform:{},
				vaildate:{},
				order: false
			}
		},
		methods:{
			
		}
	}
</script>
<style>
	/* 竖排列表*/
	.tui-product-list {display: flex;justify-content: space-between;flex-direction: row;flex-wrap: wrap;box-sizing: border-box;padding: 20upx 0;}
	.tui-product-container {flex: 1;margin: 0 16upx;}
	.tui-product-container:last-child {margin-left: 0;}
	.tui-pro-item {width: 100%;margin-bottom: 16upx;background: #fff;box-sizing: border-box;border-radius: 12upx;overflow: hidden;transition: all 0.15s ease-in-out;}
	.tui-flex-list {display: flex;margin-bottom: 1upx !important;}
	.tui-pro-img {width: 100%;display: block;min-height: ;}
	.tui-proimg-list {width: 260upx;height: 260upx;flex-shrink: 0;border-radius: 12upx;}
	.tui-pro-content {display: flex;flex-direction: column;justify-content: space-between;box-sizing: border-box;padding: 20upx;position: relative;}
	.tui-pro-content .buy_out{position: absolute;top: 0;right: 0;width: 65px;height: 65px;z-index: 1;}
	.tui-pro-content .buy_out .img{width: 100%;height: 100%;display: flex;}
	.tui-pro-tit {color: #2e2e2e;font-size: 32upx;word-break: break-all;overflow: hidden;text-overflow: ellipsis;display: -webkit-box;-webkit-box-orient: vertical;-webkit-line-clamp: 2;z-index: 2;}
	.dx-pro-group {padding-top: 18upx;z-index: 2;}
	.tui-sale-price {font-size: 34upx;font-weight: 500;color: #e41f19;}
	.tui-unit {font-size: 24upx;color: #999;padding-left: 12upx;}
	.tui-factory-price {font-size: 24upx;color: #a0a0a0;text-decoration: line-through;padding-left: 12upx;}
	.tui-pro-pay {padding-top: 10upx;font-size: 24upx;color: #999;}
	/* 竖排列表*/
	
	/* 横排列表 */
	.pro_crosswise .pro_item,.pro_crosswise .pro_item myform{background: #fff;display: flex;padding: 15px;position: relative;margin-bottom: 18rpx;}
	.pro_crosswise .cms_cover{padding-right: 20upx;position: relative;}
	.pro_crosswise .cms_cover .img{width: 200upx;height: 200upx;display: flex;}
	.pro_crosswise .pro_info{width: 100%;flex: 1;overflow: hidden;display: flex;flex-wrap: wrap;align-content: space-between;}
	.pro_crosswise .cms_title{color: #333;line-height: 40upx;width: 100%;}
	.pro_crosswise .cms_type{margin-top: 20upx;display: flex;flex-wrap: wrap;}
	.pro_crosswise .cms_type .tag{font-size: 24upx;color: #fff;padding: 0 16upx;border-radius: 6upx;display: inline-block;height: 40upx;line-height: 40upx;margin: 0 10upx 10upx 0;background-color: #33c45d;}
	.pro_crosswise .cms_price{line-height: 40upx;align-items: flex-end;}
	.pro_crosswise .oldprice{text-decoration: line-through;}
	.pro_info{position: relative;z-index: 2;}
	.pro_info .pro-cart{position: absolute;right: 0;bottom: 48upx; width: 60upx;height: 60upx;line-height: 60upx;text-align: center;border-radius: 50%;
		background: -webkit-linear-gradient(#5dfd8c, #33c45d); /* Safari 5.1 - 6.0 */
		background: -o-linear-gradient(#5dfd8c, #33c45d); /* Opera 11.1 - 12.0 */
		background: -moz-linear-gradient(#5dfd8c, #33c45d); /* Firefox 3.6 - 15 */
		background: linear-gradient(#5dfd8c, #33c45d); /* 标准的语法 */
	}
	.pro_crosswise .buy_out{position: absolute;top: 0;right: 0;width: 200upx;height: 200upx;z-index: 1;}
	.pro_crosswise .buy_out .img{width: 100%;height: 100%;display: flex;}
	/* 横排列表 */
	
	
	/* 大图列表 */
	.pro_big-list .pro-box{}
	.pro_big-list .pro-cover{position: relative;}
	.pro_big-list .pro-cover .img{width: 100%;height: 360upx;display: flex;border-radius: 12upx 12upx 0 0;}
	.pro_big-list .pro-cover .end-date{position: absolute;top: 20upx;left: 0;background: rgba(0,0,0,0.7);border-radius: 0 12upx 12upx 0;padding: 16upx 24upx 16upx 20upx;}
	.pro_big-list .pro-cover .end-date .num{width: 32upx;background: #fff;border-radius: 6upx;text-align: center;}
	.pro_big-list .pro-item{position: relative;}
	.pro_big-list .pro-box{display: block;}
	.pro_big-list .rush-tip{position: absolute;left: 4upx;top: 30upx;border-radius: 0 10upx 10upx 0;background: #1677D2;}
	.pro_big-list .time-tip{position: absolute;right: 4upx;top: 30upx;border-radius: 10upx 0 0 10upx;}
	.pro_big-list .pro-title{text-align: justify;}
	.pro_big-list .pro-price{align-items: center;}
	.pro_big-list .bg-img{background-size: 100%;width: 100%;height: 360upx;background-position: center center;background-repeat: no-repeat;border-radius: 12upx 12upx 0 0;}
	.pro_big-list .return-price{height: 60upx;line-height: 60upx;padding-right: 20upx;color: #fff;border-radius: 12upx;box-shadow: 0 0 20upx 0 hsla(0, 6%, 58%, .2);font-size: 30upx;background-image: linear-gradient(to right, #fe7f1e, #ffa431);}
	.pro_big-list .return-price .iconfont{background-color: #ffa431;padding: 10rpx;color: #fff;font-size: 40rpx;display: inline-block;line-height: 40rpx;border-radius: 12rpx;margin-right: 10upx;}
	.pro_big-list .status{position: absolute;bottom: 20upx;right: 20upx;color: #fff;font-size: 24upx;line-height: 40upx;padding: 0 12upx;border-radius: 12upx;}
	.pro_big-list .status.ing{background: #00af48;}
	.pro_big-list .status.ed{background: #ddd;}
	/* 大图列表 */
	
</style>