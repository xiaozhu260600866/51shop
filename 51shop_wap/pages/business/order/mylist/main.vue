<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<searchType  :searchType="searchType"></searchType>
			<div class="flex-between lh-40 plr15 main-bg">
				<p class="fs-14 fc-white">我的结算<span class="Arial">({{ data.lists.data.length }})</span></p>
				<p class="fs-14 fc-white">金额：{{amount}}</p>
			</div>
			<div class="pro_info mb10" v-for="(parent,key) in data.lists.data">

				<div class="order_date plr10 bd-be">
					<p class="time fs-14">
						<span>下单日期：</span>
						<span class="Arial">{{parent.created_at}}</span>
					</p>

				</div>
				<div v-if="parent.getOrder">
					 <div @click="goto('/pages/product/show/main?id='+son.getProduct.id,1)" class="order_info ptb5 plr10 bd-be" v-for="(son,key2) in parent.getOrder.products" v-if="son.getProduct">
						<div class="pro_img">
							<image class="img" :src="son.getProduct.firstCover" />
						</div>
						<div class="txt fs-14 nowrap">{{son.getProduct.name}}
							<p class="nowrap fs-12">券码：{{parent.order_no}}</p>
							<p class="nowrap fs-12" v-if="son.is_info">{{son.attribute}}</p>
						</div>
					</div> 
					 <div class="order_count fs-14 plr10">共
						<span class="Arial">1</span>件商品 需付款：
						<span class="price">￥{{getAmount(parent.getOrder)}}
						</span>
						
					</div> 
					<div class="fs-14 lh-20 pb8 text-right plr10" v-if="parent.amount">{{parent.source == '利润' ? '利润' :'结算'}}价：<span class="fs-14 Arial">{{parent.amount}}</span></div>
				</div>
				 <div v-if="parent.getPackage">
					<div @click="goto('/pages/product/show/main?id='+parent.getPackage.getProduct.id,1)" class="order_info ptb5 plr10" v-if="parent.getPackage.getProduct">
						<div class="pro_img">
							<image class="img" :src="parent.getPackage.getProduct.firstCover" />
						</div>
						<div class="txt fs-14 nowrap">{{parent.getPackage.getProduct.name}}
							<p class="nowrap fs-12">券码：{{parent.order_no}}</p>
							<!-- <p class="nowrap fs-12" v-if="son.is_info">{{son.attribute}}</p> -->
						</div>
					</div>
				
					<div class="fs-14 lh-20 pb8 text-right plr10" v-if="parent.amount">{{parent.source == '利润' ? '利润' :'结算'}}价：<span class="fs-14 Arial">{{parent.amount}}</span></div>
				</div>
			
			</div>
			<hasMore :parentData="data"></hasMore>
		</div>
	</view>
</template>

<script>
	import searchType from "@/components/searchType"
	export default {
		components:{
			searchType
		},
		computed: {
			amount() {
				let amount = 0;
				if (this.data.show && this.data.lists.data.length) {
					this.data.lists.data.forEach((v) => {
						if (v.status == 1) {
							amount += parseFloat(v.amount);
						}
					});
				}
				console.log(112);
				return amount;
			}
		},
		data() {
			return {
				formAction: '/merchant/order-lists?status=1',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				searchType:'today',
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
			receipt(item) {
				this.getConfirm("是否确认发货？", msg => {
					console.log(1);
					this.postAjax("/shop/user/change-order-status", {
						status: 8,
						id: item.id
					}).then(msg => {
						if (msg.data.status == 2) {
							this.ajax();
						}
					});
				});
			},
			getAmount(parent){
				if(parent){
					return (parseFloat(parent.amount)  +  parseFloat(parent.yunfei)) - parseFloat(parent.payed_amount)
				}
				
			},
			ajax() {
				this.getAjax(this,{searchType:this.searchType}).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
<style>
@import url("index.css");
</style>