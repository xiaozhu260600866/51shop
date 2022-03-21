<template>
	<view class="cancel-body">
		<page :parentData="data" :formAction="formAction"></page>
		<div class="cancel-box" v-if="data.show">
			<div class="cancel-sec bd-b">
				<div class="cancel-box">
					<p class="sec-title fs-16">券码核销</p>
					<p class="sec-input">
						<input type="text" v-model="ruleform.code" datatype="require" />
					</p>
					<button class="dx-btn-big dx-btn-orange" @click="toSubmit">核销</button>
				</div>
			</div>
			<div class="cancel-sec">
				<div class="cancel-box">
					<p class="sec-title fs-16">扫码核销</p>
					<button class="dx-btn-big mt40 dx-btn-orange" @click="scan">扫一扫</button>
				</div>
			</div>
		</div>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				formAction: '/shop/product/class',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				ruleform:{code:'',clearType:1},
				vaildate: { /*验证规则，目前支前有，require|phone|integer|price*/
					code: { name: '券码', vaild: 'require',errorMessage:'券码' },
				},
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
			this.data.show=true;
			//this.ajax();
		},
		methods: {
			ajax() {
				this.getAjax(this).then(msg => {
					console.log(this.data);
				});
			},
			toSubmit() {
				this.vaildForm(this, msg => {
					if (msg) {
						this.postAjax("/merchant/order-cancel", this.ruleform, msg => {
							if (msg.data.status == 2) {
								this.ruleform.code = "";
								this.getSuccess("核销成功");
							}
						});
					}
				});
			},
			scan() {
				wx.scanCode({
					onlyFromCamera: true,
					success: (res) => {
						//this.getError(res.result);
						this.ruleform.code = res.result;
						this.toSubmit();
					},
					fail: res => {
						console.log(res);
					}
				})
			},
		}
	}
</script>
<style>
@import url("index.css");
</style>