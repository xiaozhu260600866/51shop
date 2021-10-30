<template>
	<view>
		<page :parentData="data" :formAction="formAction"> </page>
		<div v-if="data.show">
			<div class="pay-frame m15">
				<weui-input v-model="ruleform.amount" label="消费金额" placeholder="请输入消费金额" type="text" name="amount" datatype="require|price"></weui-input>
				<weui-input v-model="ruleform.password" label="支付密码" type="password" placeholder="请输入支付密码" name="password" datatype="require"></weui-input>
			</div>
			<div class="pay-btn m15 mt30">
				<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="formSubmit" title="支付" myclass="dx-btn dx-btn-big dx-btn-red w-b100"></myform>
			</div>
		</div>
	</view>
</template>

<script>
	import "./index.css";
	export default {
		data() {
			return {
				formAction: '/user',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				ruleform:{},
				vaildate:{},
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
		methods: {
			formSubmit() {
				if (this.data.query.scene) {
					this.ruleform.merchnat_id = this.data.query.scene;
				} else {
					this.ruleform.merchnat_id = this.data.query.merchant_id;
				}
				console.log(this.ruleform);
				this.postAjax("/merchant/cash", this.ruleform).then(msg=>{
					if (msg.data.status == 2) {
						//this.goto("/pages/user/recharge/list/main");
						this.goto("/pages/order/payed/main?order_no=" + msg.data.order_no + '&status=1');
					}
				});
			},
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
