<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div >
			<div class="order-class bd-b bgf">
				<p :class="['nav-tab',data.query.searchMerchantType == 3 ? 'selected' :'']" @click="changeStatus(3)">客户自提</p>
				<p :class="['nav-tab',data.query.searchMerchantType == 12 ? 'selected' :'']" @click="changeStatus(12)">配送员提货</p>
				<!-- <p :class="['nav-tab',data.query.searchMerchantType == 5 ? 'selected' :'']" @click="changeStatus(5)">套餐提货</p> -->
			</div>
			<div class="pro_info mb10" v-for="(parent,key) in data.lists.data" v-if="show">
				<div class="order_date plr10 bd-be">
					<p class="time fs-12">
						<span>下单日期：</span>
						<span class="Arial">{{parent.created_at}}</span>
					</p>
					<p class="state fc-orange fs-12">{{parent.status_message}}</p>
				</div>
				
				<div class="order_info p10" v-if="data.query.searchMerchantType == 5">
					<div class="pro_img mr10">
						<image class="img" :src="parent.getProduct.firstCover" />
					</div>
					<div class="pro_right">
						<div class="tInfo w-b100">
							<p class="fs-14 lh-20 wrap2">{{parent.getProduct.name}}</p>
							<p class="nowrap fs-12 mt5 fc-9" v-if="parent.is_info">{{parent.attribute1}}</p>
						</div>
						<div class="w-b100">
							<p class="fs-12 fc-6">数量 × <span>{{parent.num}}</span></p>
						</div>
					</div>
				</div>
				<div class="order_info p10" v-else @click="goto('/pages/product/show/main?id='+son.getProduct.id,1)"
				 v-for="(son,key2) in parent.products">
					<div class="pro_img mr10">
						<image class="img" :src="son.getProduct.firstCover" />
					</div>
					<div class="pro_right">
						<div class="tInfo w-b100">
							<p class="fs-14 lh-20 wrap2">{{son.getProduct.name}}</p>
							<p class="nowrap fs-12 mt5 fc-9" v-if="son.is_info">{{son.attribute1}}</p>
						</div>
						<div class="w-b100">
							<p class="fs-12 fc-6">数量 × <span>{{son.num}}</span></p>
						</div>
					</div>
				</div>
				<div class="order_count fs-14 plr10">
					<p class="fs-14">共<span class="Arial">{{parent.num}}</span>件商品 需付款：<span class="price fs-15">￥{{ (parseFloat(parent.amount)  +  parseFloat(parent.yunfei)) - parseFloat(parent.payed_amount)}}</span></p>
				</div>
				<div class="fs-14 lh-20 pb8 text-right plr10" v-if="parent.merchant_price">结算价：<span class="fs-14 Arial" >{{parent.merchant_price}}</span></div>
				<div class="btn-group ptb8 plr10 bd-te" v-if="parent.status == 3 && parent.merchant_user_id">
					<div class="btn-item" v-if="parent.status == 3">
						<div class="btn-nav" @click="receipt(parent)">提货通知</div>
					</div>
				</div>
			</div>
			<hasMore :parentData="data"></hasMore>
		</div>
	
	</view>
</template>

<script>
	import "./index.css";
	export default {
		data() {
			return {
				formAction: '/merchant/order-lists',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				show:false
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
			changeStatus(searchMerchantType){
				this.show = false;
				this.data.query.searchMerchantType = searchMerchantType;
				this.ajax();
			},
			receipt(item) {
				this.getConfirm("是否确认发货？", msg => {
					console.log(1);
					this.postAjax("/shop/user/change-order-status", {
						status: 8,
						id: item.id
					}).then(msg=>{
						if (msg.data.status == 2) {
							this.ajax();
						}
					});
				});
			},
			ajax() {
				this.getAjax(this).then(msg => {
					this.show = true;
				});
			}
		}
	}
</script>
