<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view class="pb10" v-if="data.show">
			<!-- <view class="list-class bd-b bg-f">
				<view :class="['nav-tab',data.query.status == 12 ? 'selected' :'']" @click="changeStatus(12)">全部</view>
				<view :class="['nav-tab',data.query.status == 1 ? 'selected' :'']" @click="changeStatus(1)">待付款</view>
				<view :class="['nav-tab',data.query.status == 3 ? 'selected' :'']" @click="changeStatus(3)">待发货</view>
				<view :class="['nav-tab',data.query.status == 5 ? 'selected' :'']" @click="changeStatus(5)">待收货</view>
				<view :class="['nav-tab',data.query.status == 9 ? 'selected' :'']" @click="changeStatus(9)">待评价</view>
				<view :class="['nav-tab',status == 10 ? 'selected' :'']" @click="changeStatus(10)">售后</view>
			</view> -->
			
			<dx-tabs :tabs="[
				{value: 12,name: '全部'},
				{value: 1,name: '待付款'},
				{value: 3,name: '待发货'},
				{value: 5,name: '待收货'},
				{value: 9,name: '待评价'},
			]"  v-model="data.query.status" @change="change" :height="100" :size="32" :selectedSize="32" selectedColor="#FC4A26" sliderBgColor="#FC4A26"></dx-tabs>
			<view class="pro_info" v-for="(parent,key) in data.lists.data">
				<view class="order_date fs-15">
					<view class="time">
						<text>订单号：</text>
						<text class="Arial">{{parent.order_no}}</text>
					</view>
					<view class="state fc-red pr8">{{parent.status_message}}</view>
				</view>
				
				<view @click="goto(parent.status ==1?'/pages/order/buy/main?order_no='+parent.order_no:'/pages/order/buy/detail?order_no='+parent.order_no,1)">
					<orderPro :data="parent.products"></orderPro>
				</view>
				
				<view class="order_count lh-1 flex-baseline">
					<text class="fs-12 fc-9 pr5">实付款</text>
					<text class="num fc-1 fs-16 fw-bold">￥</text>
					<text class="num fc-1 fs-20 fw-bold">{{ (parseFloat(parent.amount)  +  parseFloat(parent.yunfei)) - parseFloat(parent.payed_amount)}}</text>
				</view>
				<view class="btn-group pb12 mt8">
					<view class="btn-item">
						<view class="btn-nav obtn" @click="goto('/pages/order/buy/detail?order_no='+parent.order_no,1)">查看详情</view>
					</view>
				</view>
			</view>
			<hasMore :parentData="data" source="order"></hasMore>
		</view>
	</view>
</template>

<script>
	import orderPro from "@/components/orderPro";
	import dxTabs from "doxinui/components/tabs/tabs"
	export default {
		components:{orderPro,dxTabs},
		data() {
			return {
				formAction: '/shop/user/order-lists',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				status: 12
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
		onShow() {
			if (this.$store && this.$store.state && this.$store.state.mutations.historyUrl) {
				this.$store.state.mutations.historyUrl = "";
				this.ajax();
			}
		},
		onLoad(options) {
			this.ajax();
		},
		methods: {
			changeOrder(item) {
				let title = item.status == 1 ? "您确认要取消订单吗？" : "您确认要联系客服吗？订单金额24小时内原路退回";
				this.getConfirm(title, () => {
					if(item.status == 1){
						this.postAjax("/ajax/mydel",{tablename:'orders',id:item.id}).then(msg=>{
							if (msg.data.status == 2) {
								this.data.nextPage = 1;
								this.ajax();
							}
						})
					}else{
						this.postAjax("/user/change-order-status", {
							id: item.id,
							status: 0
						}).then(msg => {
							if (msg.data.status == 2) {
								this.data.nextPage = 1;
								this.ajax();
							}
						});
					}
					
				});
			},
			changeStatus(status) {
				this.data.query.status = status;
				this.data.nextPage = 1;
				this.ajax();
			},
			
			change() {
				this.data.nextPage = 1;
				this.ajax();
			},
			ajax() {
				this.getAjax(this,{status:this.status}).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
<style>
@import url("index.css");
</style>