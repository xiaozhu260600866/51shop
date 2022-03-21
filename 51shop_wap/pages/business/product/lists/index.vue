<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show">
			<div class="count-head plr15 ptb8 flex-between bg-f">
				<p class="fs-14 fc-6 lh-28">产品：<span class="Arial fs-14">{{data.lists.data.length}}</span></p>
				<div class="dx-btn dx-btn-sm dx-btn-green plr20 bdr14" @click="goto('/pages/business/product/create_edit/index',1)">发布产品</div>
			</div>
			
			<div class="pro_lists bus-prolist bg-f m10 bdr6">
				<div class="porducts" v-for="(item,key) in data.lists.data">
					<div class="pro-date p10 bd-be">
						<p class="fs-13 fc-3 lh-20">发布日期：<span class="Arial fs-13">{{ item.created_at }}</span></p>
					</div>
					<div class="pro_item plr10" @click="goto(item.is_group ? '/pages/group/show/main?id='+item.id :'/pages/product/show/main?id='+item.id,1)">
						<view class="cms_cover">
							<image class="img" :src="getSiteName + '/upload/images/product/300_'+item.thumb_pic " mode="aspectFill"></image>
						</view>
						<view class="pro_info">
							<view class="cms_title Aname fs-14 fc-0">{{ item.name }}</view>
							<view class="cms_price flex-between w-b100">
								<view class="flex1 flex">
									<view class="saleprice">
										<span class="fs-11">￥</span>{{item.fclass == 84 ? '0.00' :item.price}}
									</view>
									<view class="cprice fs-12" v-if="item.new_price">{{item.new_price}}</view>
								</view>
								<view class="fs-11 fc-9 pl5 lh-20">库存<span class="fs-11">{{ item.num }}</span>件</view>
							</view>
						</view>
					</div>
					<div class="pro_nav flex-right ptb8 plr10">
						<div class="dx-btn dx-btn-sm dx-btn-green-o plr20 bdr14" @click="del(item)">删除</div>
						<div class="dx-btn dx-btn-sm dx-btn-green plr20 bdr14 ml10" @click="goto('/pages/business/product/create_edit/index?id='+item.id,1)">编辑</div>
					</div>
					<hasMore :parentData="data"></hasMore>
						
				</div>
			</div>
		</div>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				formAction: '/merchant/product-lists',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
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
		onShow(){
			this.ajax();
		},
		methods: {
			del(item){
				this.getConfirm("确认删除？",msg=>{
					this.postAjax('/ajax/mydel', {
						id: item.id,
						tablename: 'shop_products'
					}).then(msg => {
						if (msg.data.status == 2) {
							this.ajax();
						}
					});
				});
			},
			ajax() {
				this.getAjax(this).then(msg => {
					
				});
			}
		}
	}
</script>
<style>
@import url("index.css");
</style>