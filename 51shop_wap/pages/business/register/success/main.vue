<template>
	<view>
		<page :parentData="data" :formAction="formAction"> </page>
		<div v-if="data.show">
			<div class="payed-content">
				<div class="con">
					<p class="iconfont icon-pay-yes"></p>
					<p class="content">提交成功</p>
					<p class="fs-15  fc-6 mt10">您的申请已经提交，请等待审核，谢谢!</p>
				</div>
			</div>
			<div class="button">
				<myform class="w-b100" :ruleform="ruleform" :vaildate="vaildate" @callBack="goto('/pages/index/main',2)" myclass="dx-btn dx-btn-big dx-btn-green w-b100" title="返回"> </myform>
			</div>
		</div>
	</view>
</template>

<script>
	import "./index.css";
	export default {
		data() {
			return {
				formAction: '/shop/product/class',
				ruleform:{},
				vaildate:{},
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
		methods: {
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			}
		}
	}
</script>
