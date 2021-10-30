<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div :class="[status == 3 ? 'pb50' : '']" v-if="data.show">
			<!-- <searchType :searchType="searchType"></searchType> -->
			<dx-tabs-date curBg="#33c45d" btnType="success" v-if="data.query.status == 9"></dx-tabs-date>
			<!-- <div class="TypeNav bg-f mb8" v-else>
				<div class="navItem">
					<p :class="['aLink',status == 3 ? 'cur' :'']" @click="changeStatus(3)">待发货</p>
					<p :class="['aLink',status == 5 ? 'cur' :'']" @click="changeStatus(5)">已发货</p>
				</div>
			</div> -->
			<view class="flex-between lh-40 mb8 bg-f plr10 fs-15">
				<view class="p">订单：<span class="unm">{{count.count}}</span>笔</view>
				<view class="p">金额：<span class="price">{{count.amount}}</span>元</view>
			</view>
			
			<view class="de-lists">
				<view class="item" v-for="v in proLists">
					<view class="row">订单号：<text class="Arial">{{v.order_no}}</text></view>
					<view class="row">日期：<text class="Arial">{{v.created_at}}</text></view>
					<view class="ptb10 plr15 bd-be">
						<view class="pro_name">{{v.getOrder ? v.getOrder.product_str:''}}</view>
						<view class="flex-between flex-middle mt5">
							<view class="Arial">×{{v.getOrder? v.getOrder.num : ''}}</view>
							<view class="price">￥{{v.getOrder ? v.getOrder.amount : ''}}</view>
						</view>
					</view>
					<view class="row">
						<view>收益</view>
						<view><text class="price">{{v.amount}}</text>元</view>
					</view>
				</view>
				<hasMore :parentData="data"></hasMore>
			</view>
			<!-- <div id="cartfooter" v-if="status == 3">
				<div class="bd-t lInfo" style="width: 100%;">
					<div class="group left">
						<div class="zheng">
							<checkbox-group @change="checkboxAll">
								<checkbox />
							</checkbox-group>
						</div>
						<p class="txt fs14">全选</p>
					</div>
				
				</div>
				<div class="submit dx-btn fs18 fs-white" @click="cartSubmit" :style="checkLength == 0 ? 'background: #ddd' :'background:#FF4351'">发货</div>
			</div> -->
		</div>
	</view>
</template>

<script>
	import "./index.css";
	import searchType from "@/components/searchType"
	import dxTabsDate from "doxinui/components/tabs/tabs_date"
	export default {
		components:{searchType,dxTabsDate},
		data() {
			return {
				formAction: '/supplier/order-lists',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				status:3,
				checkLength:1,
				date: 0,
				searchType:'',
				getSiteName: this.getSiteName(),
				show:false,
				proLists:[
					{
						order_no:'2020252536353',
						created_at:'2020-11-24 17:55:23',
						amount: 22,
						getOrder:{
							product_str:'桶装水',
							num:1,
							amount: 22,
						}
					}
				]
			}
		},
		computed:{
			count(){
				let amount = 0;
				let count = 0;
				if(this.data.lists.data && this.data.lists.data.length){
					 this.data.lists.data.forEach(msg=>{
						
						 msg.products.forEach(son=>{
							  count+=parseInt(son.num);
							  amount+=parseFloat(son.supplier_price );
						 });
						
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
				this.status = 9;
			}
			this.ajax();
		},
		methods: {
			cartSubmit(){
				this.getConfirm("是否确认发货？",msg=>{
					let ids = [];
					this.data.lists.data.forEach(msg=>{
						if(msg.is_check){
							ids.push(msg.id);
						}
					});
					if(ids.length == 0){
						this.getError("没选择");
						return false;
					}
					this.postAjax("/supplier/order-rob-all",{ids:ids}).then(msg=>{
						if(msg.data.status == 2){
							this.ajax();	
						}
					});
				})
			},
			checkboxChange(e, item, key) {
				let isCheck = e.mp.detail.value.length > 0 ? 1 : 0;
				this.$set(this.data.lists.data[key],"is_check",isCheck);
			},
			checkboxAll(e) {
				let isCheck = e.mp.detail.value.length > 0 ? 1 : 0;
			
				for (var i = 0; i < this.data.lists.data.length; i++) {
					var v = this.data.lists.data[i];
					this.$set(this.data.lists.data[i],"is_check",isCheck);
					
				}
				console.log(this.data.lists.data);
			},
			changeStatus(status){
				this.show = false;
				this.status = status;
				this.ajax();
			},
			rob(item) {
				this.getConfirm("是否确认发货？", msg => {
					console.log(1);
					this.postAjax("/supplier/order-rob", {
						id: item.id
					}).then(msg=>{
						if (msg.data.status == 2) {
							this.ajax();
						}
					});
				});
			},
			ajax() {
				this.getAjax(this,{status:this.status,searchType:this.searchType}).then(msg => {
					this.show = true;
				});
			}
		}
	}
</script>
