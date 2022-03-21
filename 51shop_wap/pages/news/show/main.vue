<template>
	<view>
		<page :parentData="data" :formAction="formAction" Fbottom="bottom: 25px">
			<view slot="floatBtn">
				<floatBtn type="2" icon="icon-float-feedback" myclass="float-nav-red" iSize="fs-22" openType="contact" title="咨询" shadow></floatBtn>
			</view>
		</page>
		<div v-if="data.show">
			<div class="content">
				<div class="plr15" v-if="data.detail.fclass != 76">
					<p class="fs-18 mt10 mb5 lh-24">{{ data.detail.title }}</p>
					<p class="fs-13 fc-9">{{ data.detail.getTime }} </p>
				</div>
				<div class="index-video" v-if="data.detail.video_url">
					<video class="video" :src="data.detail.video_url" autoplay="true" enable-danmu danmu-btn controls></video>
				</div>
				<div class="brief p15 fs-14 fc-6">
					<u-parse :content="data.detail.content" />
				</div>
				
			</div>
		</div>
	</view>
</template>

<script>
	import uParse from '@/components/gaoyia-parse/parse.vue'
	export default {
		components: {
			uParse,
		},
		data() {
			return {
				formAction: '/newShow.html',
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
					 this.setTitle(msg.detail.title);
					 if (msg.detail.video_url) {
					 	this.getQQvideoLink(msg.detail.video_url, data => {
					 		msg.detail.video_url = data.url;
					 		console.log(msg.detail.video_url);
					 	});
					 }
				});
			}
		}
	}
</script>
<style>
@import url("./index.css");
@import url("@/components/gaoyia-parse/parse.css");
</style>