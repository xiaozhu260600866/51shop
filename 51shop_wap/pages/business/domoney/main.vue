<template>
	<view>
		<page :parentData="data" :formAction="formAction">
		</page>
		<div v-if="data.show">
			<div class="action">
				<div class="action-icon">
					<div class="name mt20">
						我的可提现佣金
					</div>
					<div class="price-red fc-red fs30">
						￥
						<text class="price">{{data.price}}</text>
					</div>
				</div>
				<div class="m20"><button class="dx-btn-big dx-btn-red" @click="submit">提交</button></div>
				<p class="fc-6 text-center fs-14">提现金额必须20元以上</p>
			</div>
			<diag ref="diag" top="20">
				<div slot="content" :ruleform="ruleform">
					<div class="layermcont">
						<div class="verifyCode p">
							<weui-input v-model="ruleform.name" label='提现人' type="text" name="name" datatype="require"></weui-input>
							<weui-input v-model="ruleform.amount" label='金额' type="text" name="amount" datatype="require|price"></weui-input>
							<weui-input v-model="ruleform.blank_name" label='开户行' type="text" name="blank_name" datatype="require"></weui-input>
							<weui-input v-model="ruleform.blank_cardno" label='银行卡' type="text" name="blank_cardno" datatype="require"></weui-input>
							<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="formSubmit" myclass="verifyBtn dx-btn-big dx-btn-red" title="确定">
							
							</myform>
						</div>
					</div>
				</div>
			</diag>
		</div>
	</view>
</template>

<script>
	import "./index.css";
	import diag from "xiaozhu/wechatapp/components/diag";
	export default {
		data() {
			return {
				formAction: '/merchant/do-money',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				ruleform: {
					amount: '',
					blank_name: '',
					blank_cardno: '',
					name: ''
				},
				price: 0,
				vaildate: { /*验证规则，目前支前有，require|phone|integer|price*/
					amount: {
						name: '金额',
						vaild: 'require'
					},
					name: {
						name: '姓名',
						vaild: 'require'
					},
					blank_name: {
						name: '开户行',
						vaild: 'require'
					},
					blank_cardno: {
						name: '银行卡号',
						vaild: 'require'
					},
				}
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
		components: {
			diag
		},
		methods: {
			submit() {
				this.$refs.diag.ajax();
			},
			formSubmit() {
				this.postAjax(this.formAction, this.ruleform).then(msg => {
					if (msg.data.status == 2) {
						this.goto("/pages/business/record-list/main");
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
