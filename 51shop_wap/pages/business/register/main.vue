<template>
	<view>
		<page :parentData="data" :formAction="formAction"></page>
		<div v-if="data.show" class="register">
			<weui-input v-model="ruleform.company_name" label="公司名称" type="text" name="company_name" datatype="require"></weui-input>
			<weui-input v-model="ruleform.industry" name="industry" datatype="require" label="行业" changeField="label" type="select"
			 dataKey="industry"></weui-input>
			<weui-input v-model="ruleform.name" label="联系人" type="text" name="name" datatype="require"></weui-input>
			<weui-input v-model="ruleform.username" label="手机" type="number" name="username" datatype="require|phone"></weui-input>
			<weui-input v-model="ruleform.tel" label="固话" type="number" name="tel"></weui-input>
			<weui-input v-model="ruleform.password" label="密码" type="password" name="password" datatype="require"></weui-input>
			<weui-input v-model="ruleform.code" label="验证码" type="sms" name="code" action="/auth/sendSms" :phone="ruleform.username"
			 datatype="require"></weui-input>
			<weui-input v-model="ruleform.address" label="选择地址" navClass="dx-btn dx-btn-green ptb0 plr15" type="location" name="address"
			 @callback="locationRes"></weui-input>
			<weui-input v-model="ruleform.start_hour" label="营业开始时间" type="time" name="start_hour"></weui-input>
			<weui-input v-model="ruleform.end_hour" label="营业结束时间" type="time" name="end_hour"></weui-input>
			<weui-input v-model="ruleform.pic" label="logo图片" type="upload" upurl='user' allowUpLoadNum="1" name="pic" datatype="array"
			 placeholder="logo图片"></weui-input>
			<weui-input v-model="ruleform.card_cover" label="营业执照" type="upload" upurl='user' allowUpLoadNum="1" name="card_cover"
			 datatype="array" placeholder="执照"></weui-input>
			<weui-input v-model="ruleform.swiper_cover" label="滚动图片" type="upload" upurl='user' allowUpLoadNum="3" name="swiper_cover"
			 datatype="array" placeholder="滚动图片"></weui-input>
			<div class="group detail">
				<p class="g-name">商家介绍</p>
				<weui-input v-model="ruleform.remark" type="textarea" name="remark" placeholder="请输入商家介绍"></weui-input>
			</div>
			<weui-input v-model="ruleform.remark_pic" label="介绍图片" type="upload" upurl='user' allowUpLoadNum="10" name="remark_pic"
			 placeholder="介绍图片"></weui-input>
			<div class="login-btn m20">
				<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="formSubmit" myclass="dx-btn dx-btn-big dx-btn-green-o w-b100 mt10 fs-17"></myform>
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
				getSiteName: this.getSiteName(),
				ruleform: {},
				vaildate: {},
				industry: []
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
			wx.removeStorageSync('smsCode');
			this.ajax();
		},
		methods: {
			locationRes(res){
				this.ruleform.location_x = res.latitude;
				this.ruleform.location_y = res.longitude;
			},
			formSubmit() {
				if (this.ruleform.code != wx.getStorageSync('smsCode')) {
					this.getError("验证码不正确");
					return false;
				}
				this.ruleform.role = 5;
				this.postAjax("/user/register", this.ruleform).then(msg=>{
					if (msg.data.status == 2) {
						let userinfo = wx.getStorageSync('userInfo');
						userinfo.is_bing = 1;
						userinfo.phone = this.ruleform.username;
						this.$store.state.mutations.historyUrl = true;
						wx.setStorageSync("userInfo", userinfo);
						this.goto("/pages/business/register/success/main");
					}
				});
			},
			ajax() {
				this.getAjax(this).then(msg => {
					this.industry = msg.industryArr
				});
			}
		}
	}
</script>
