<template>
    <view>
        <view class="share-overlay" @click="toggleInfoDiag" v-if="infoDiag"></view>
		<view class="infoanimation choose" v-if="infoDiag">
			<view class="choose-floor">
				<view class="choose-off flex-right fw-bold" @click="toggleInfoDiag"><text class="dxi-icon dxi-icon-off"></text></view>
				<view class="choose-sit">
					<view class="pro-img">
						<image :src="siteName+'/upload/images/product/'+productInfo[infokey].pic" class="img"></image>
					</view>
					<view class="choose-detail fs-13">
						<view class="pt3">
							<view class="price lh-1">￥
								<text class="Arial fw-bold fs-20">{{ productInfo[infokey].price }}</text>
							</view>
							<view class="pt5">已选择：{{ productInfo[infokey].name }}</view>
						</view>
						<view class="fc-7 lh-1_3">库存
							<text class="Arial">{{ productInfo[infokey].num }}</text>
						</view>
					</view>
				</view>
				<view class="choose-class pt15">
					<view class="fs-15 pb10">规格</view>
					<view class="classG">
						<block v-for="(item,key) in productInfo">
							<view class="nav" :class="{'yes-act':key==infokey}" @click="changeInfoKey(key)">{{ item.name }}</view>
						</block>
					</view>
				</view>
				<view class="choose-num pt15" v-if="!product.hidd_buy_num">
					<view class="fs-15">数量</view>
					<!-- <view class="num-input">
						<view class="nav iconfont icon-count-minus minus_disabled" id="minus" @click="infonum<=1 ? 1:infonum-=1"></view>
						<input class="input" name="num" id="buyNum" type="tel" v-model.lazy="infonum" />
						<view class="nav iconfont icon-count-plus" id="plus" @click="infonum++"></view>
					</view> -->
					<view class="num-input">
						<tui-numberbox v-model="infonum"></tui-numberbox>
					</view>
				</view>
			</view>
			<view class="button plr15 pt10">
				<dx-button type="primary" size="lg" round block @click="infoSubmit">确认</dx-button>
			</view>
		</view>
    </view>
</template>
<script>
import tuiNumberbox from "xiaozhu/uniapp/thorui/components/numberbox/numberbox";
export default {
    props: ["productInfo","product"],
	components: {tuiNumberbox},
    data() {
        return {
            infokey: 0,
            infonum: 1,
            infoDiag: false,
            siteName: this.getSiteName(),
            cartNum: 0,
            showType: ''
        }
    },
    onLoad() {
        this.infoDiag = false;
    },
    methods: {
		changeInfoKey(key){
			this.infokey = key;
		},
        infoSubmit() {
            let info = this.productInfo[this.infokey];
            info.bnum = this.infonum;
            this.addCartAjax({ infoId: info.id, num: info.bnum, type: "add", is_info: 1 });
        },
        addCartAjax(data) {
            let showType = this.showType;
            let url = "";
            if (showType == "addCart") {
                url = "/shop/cart/to-add-cart"
            } else {
                url = "/shop/product/show";
            }
			this.postAjax(url,data).then(msg=>{
				if (msg.data.status == 2) {
				    if (showType != "addCart") {
				        this.infoDiag = false;
				        this.goto('/pages/order/buy/main?order_no=' + msg.data.order_no,1);
				        return false;
				    }
				    this.cartNum = msg.data.num;
				    if (this.productInfo.length > 0) {
				        this.toggleInfoDiag();
				    }
				    this.$emit('callback', this.cartNum);
				}
			});
           
        },
        toggleInfoDiag() {
            this.infoDiag = !this.infoDiag;
        }
    }

}
</script>
<style scoped>
@import url("info.css");
</style>