<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div >
			<!-- <div class="order-class bg-f mb8">
				<p :class="['nav-tab',data.query.status == 5 ? 'selected' :'']" @click="changeStatus(5)">待取货</p>
				<p :class="['nav-tab',data.query.status == 9 ? 'selected' :'']" @click="changeStatus(9)">已完成</p>
			</div> -->
			<searchType v-if="data.query.status == 9" :searchType="searchType"></searchType>
			<view class="flex-between lh-40 mb8 bg-f plr10 fs-15" >
				<view class="p">订单：<span class="unm">{{count.count}}</span>笔</view>
				<view class="p">金额：<span class="price" v-if="data.query.status == 9">{{count.amount}}</span>元</view>
			</view>
			<div class="pro_info mb10" v-for="(parent,key) in data.lists.data" v-if="show">
				<div class="order_date plr10 bd-be">
					<p class="time fs-12">
						<span>下单日期：</span>
						<span class="Arial">{{parent.created_at}}</span>
					</p>
					<p class="state fc-orange fs-12">{{parent.status_message}}</p>
				</div>
				
				
				<div  @click="goto('/pages/product/show/main?id='+son.getProduct.id,1)" class="order_info ptb5 plr10 bd-be" v-for="(son,key2) in parent.products">
					<div class="pro_img">
						<image class="img" :src="son.getProduct.firstCover" />
					</div>
					<div class="txt fs-14 nowrap">{{son.getProduct.name}}
						<!-- <p class="nowrap fs-12" v-if="parent.status >=3">券码：{{parent.order_no}}</p> -->
						<p class="nowrap fs-12" v-if="son.is_info">{{son.attribute}}</p>
					</div>
				</div>
				<div class="od-lists plr15 bd-be ptb5">
					<div class="weui-cell">
						<div class="weui-cell__bd">
							<p class="fs-14 p">下单时间：</p>
						</div>
						<div class="weui-cell__ft">
							<p class="fs-14 p Arial">{{parent.created_at}}</p>
						</div>
					</div>
					<div class="weui-cell" v-if="parent.addr_name">
						<div class="weui-cell__bd">
							<p class="fs-14 p">姓名：</p>
						</div>
						<div class="weui-cell__ft">
							<p class="fs-14 p Arial">{{ parent.addr_name }}</p>
						</div>
					</div>
					<div class="weui-cell" v-if="parent.addr_name">
						<div class="weui-cell__bd">
							<p class="fs-14 p">电话：</p>
						</div>
						<div class="weui-cell__ft">
							<p class="fs-14 p">{{ parent.addr_phone }}</p>
						</div>
					</div>
				</div>
				<div class="order_count fs-14 plr10">
					<p class="fs-14">共<span class="Arial">{{parent.num}}</span>件商品 收益：<span class="price fs-15">￥{{parent.merchant_price}}</span></p>
				</div>
				<!-- <div class="fs-14 lh-20 pb8 text-right plr10" v-if="parent.getMerchantPrice">结算价：<span class="fs-14 Arial" >{{parent.getMerchantPrice}}</span></div> -->
				<div class="btn-group ptb8 plr10 bd-te">
					<div class="btn-item" v-if="parent.status == 5 && parent.rob == 1">
						<div class="btn-nav" @click="finish(parent)">客户取货</div>
					</div>
				</div>
			</div>
			<hasMore :parentData="data"></hasMore>
		</div>
	
	</view>
</template>

<script>
	import "./index.css";
	import searchType from "@/components/searchType"
	export default {
		data() {
			return {
				formAction: '/ztd/order-lists',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				show:false,
				searchType:'',
				date: 0,
			}
		},
		components:{
			searchType
		},
		computed:{
			count(){
				let amount = 0;
				let count = 0;
				if(this.data.lists.data && this.data.lists.data.length){
					 this.data.lists.data.forEach(msg=>{
						 count+=parseInt(msg.num);
						 amount+=parseFloat(msg.merchant_price);
					 });
				}
				let res = {amount:amount.toFixed(2),count:count};
				console.log(res);
				return res;
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
			if(options.status && options.status == 9){
				this.searchType = 'today';
				this.setTitle("已完成订单");
			}else{
				this.setTitle("待取货订单");
			}
			this.ajax();
		},
		methods: {
			search(searchType){
				this.searchType = searchType;
				this.ajax();
			},
			changeStatus(status){
				this.show = false;
				this.data.query.status = status;
				this.ajax();
			},
			finish(item) {
				this.getConfirm("是否确认取货？", msg => {
					console.log(1);
					this.postAjax("/ztd/order-rob", {
						id: item.id
					}).then(msg=>{
						if (msg.data.status == 2) {
							this.ajax();
						}
					});
				});
			},
			ajax() {
				this.getAjax(this,{searchType:this.searchType}).then(msg => {
					this.show = true;
				});
			}
		}
	}
</script>
