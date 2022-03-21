<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show" class="pro_create bg-f">
			<weui-input v-model="ruleform.name" label="产品名称" type="text" name="name" datatype="require"></weui-input>
			<weui-input v-model="ruleform.yunfei" label="运费" type="text" name="yunfei" datatype="require"></weui-input>
			<weui-input v-model="ruleform.num" label="库存" type="text" name="num" datatype="require"></weui-input>
			<weui-input v-model="ruleform.price" label="售价" type="text" name="price" datatype="require"></weui-input>
			<weui-input v-model="ruleform.new_price" label="原价" type="text" name="new_price"></weui-input>
			<weui-input v-model="ruleform.merchant_price" label="结算价" type="text" name="merchant_price" datatype="require"></weui-input>
			
			
			<!-- <weui-input v-model="ruleform.profit_price" label="利润价" type="text" name="profit_price" datatype="require"></weui-input> -->
			<weui-input v-model="ruleform.thumb_pic" label="缩略图" type="upload" upurl='product' allowUpLoadNum="1" name="thumb_pic"
			 datatype="array" placeholder="logo图片"></weui-input>
			<weui-input v-model="ruleform.cover" label="滚动图片" type="upload" upurl='product' allowUpLoadNum="3" name="cover"
			 datatype="array" placeholder="滚动图片"></weui-input>
			 <weui-input v-model="ruleform.product_cover" label="产品介绍图片" type="upload" upurl='product' allowUpLoadNum="5" name="product_cover"
			  datatype="array" placeholder="产品介绍图片"></weui-input>
		</div>
		<div class="pro_create bg-f mtb12">
			<div class="group detail">
				<p class="g-name fs-16">产品介绍</p>
				<weui-input v-model="ruleform.content" type="textarea" name="content" placeholder="请输入商家介绍"></weui-input>
			</div>
		</div>
		<div class="login-btn m20">
			<myform class="w-b100" :ruleform="ruleform" :vaildate="vaildate" @callBack="formSubmit" myclass="dx-btn dx-btn-big dx-btn-green w-b100 mt10 fs-17"></myform>
		</div>
		</div>
	</view>
</template>

<script>
	export default {
		 watch: {
		　　ruleform: {
		　　　　handler(ruleform, oldValue) {
		　　　　　  if(ruleform.price && ruleform.merchant_price && ruleform.ps_price && ruleform.first_distor && ruleform.second_distor){
					  console.log("01");
					  let amount =  parseFloat(ruleform.price) -  parseFloat(ruleform.merchant_price)-parseFloat(ruleform.ps_price);
					  let first_amount = parseFloat(ruleform.price) * parseFloat(ruleform.first_distor) / 100;
					  let second_amount = parseFloat(ruleform.price) * parseFloat(ruleform.second_distor) / 100;
					    console.log(amount);
					  if(amount > 0){
						 this.ruleform.profit_price = 	amount - first_amount - second_amount;
					  }
					}
				  
		　　　　},
		　　　　deep: true
		　　}
		}, 
		data() {
			return {
				formAction: '/merchant/product-edit',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				ruleform: {is_share_distribution:1,is_distribution_ratio:1,type:1,ps_price:6},
				vaildate: {},
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
			if (options.id) {
				this.ajax();
			} else {
				this.ajax();
				this.formAction = "/merchant/product-create";
			}

		},
		methods: {
			formSubmit() {
				/* this.ruleform.disConfig ={
					type:0,
					first_distor:this.ruleform.first_distor,
					first_integral:0,
					second_distor:this.ruleform.second_distor,
					second_integral:0,
					third_distor:0,
					third_integral:0
				} */
				
				let allow_max_merchant_price = parseFloat(this.ruleform.price) * 97 / 100;
				if(this.ruleform.merchant_price > allow_max_merchant_price){
					this.getError("结算价请预留百份之3的利润");
					return false;
				}
				this.postAjax(this.formAction, this.ruleform).then(msg => {
					if (msg.data.status == 2) {
						this.back();
					}
				});
			},
			ajax() {
				this.getAjax(this).then(msg => {
					if (msg.detail) {
						this.ruleform = msg.detail;
						this.ruleform.cover = this.ruleform.cover ? this.ruleform.cover.split(",") : [];
						this.ruleform.product_cover = this.ruleform.product_cover ? this.ruleform.product_cover.split(",") : [];
						this.ruleform.thumb_pic = this.ruleform.thumb_pic ? this.ruleform.thumb_pic.split(",") : []
					}else{
						this.$set(this.ruleform,"first_distor",msg.first_distor);
						this.$set(this.ruleform,"second_distor",msg.second_distor);
					}
				});
			}
		}
	}
</script>
<style>
@import url("index.css");
</style>