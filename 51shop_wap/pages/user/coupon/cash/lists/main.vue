<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<view v-if="data.show">
			<dx-tabs :tabs="navbar" @change="ajax" v-model="data.query.status" selectedColor="#57C734" sliderBgColor="#57C734" :selectedSize="32" :size="32" :height="92"></dx-tabs>
			<cashLists :data="data.lists.data" :type="1"></cashLists>
			<hasMore :parentData="data" source="nodata" message="暂无优惠券"></hasMore>
		</view>
	</view>
</template>

<script>
	import "./index.css";
	import dxTabs from "doxinui/components/tabs/tabs"
	import cashLists from "@/components/cashLists.vue"
	export default {
		components:{dxTabs,cashLists},
		data() {
			return {
				formAction: '/shop/user/coupon?scanQrcode=1',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				status: 0,
				listsShow:false,
				navbar:[
					{value: 0,name: '待使用'},
					{value: 1,name: '已使用'},
					{value: 2,name: '已过期'},
					{value: 12,name: '全部'},
				],
				
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
			this.shareSource(this, '商城');
		},
		onShow(){
			this.onShow(this);
		},
		onLoad(options) {
			this.ajax();
			this.type = options.type;
			this.setTitle(options.title);
		},
		methods: {
			wechatCard(item){
				console.log(item);
				 this.postAjax("/shop/user/card-signature",{coupon_userid:item.id,wechat_card_id:item.wechat_card_id}).then(msg=>{
					 if(msg.data.status == 2){
						wx.addCard({
						      cardList: [
									msg.data.cardSignature
						      ],
						      success:(res)=> {
						        //this.postAjax("/shop/order/update",{give_wechatCard:1,order_no:this.detail.order_no});
								this.data.lists.data  = [];
								this.ajax();
						      },
						      fail(res){
						        console.log('添加失败',res);
						      },
						      complete(res){
						        console.log('添加完成', res);
						      }
						    })	
					 }
				 });
			
			},
			ajax() {
				this.listsShow = false;
				this.getAjax(this).then(msg => {
					this.listsShow = true;
				});
			}
		}
	}
</script>
