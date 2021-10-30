<template>
	<div :class="['contact-group',myclass ? myclass : '']">
		<div class="ltel flex1">
			<div class="row">
				<p>客服电话：</p>
				<p class="Arial" @click="phone('0750-3336888')">0750-3336888</p>
				<p class="dx-btn dx-btn-small dx-btn-green-o ml10" @click="phone('0750-3336888')">拨号</p>
			</div>
			<!-- <div class="row">
				<p>客服电话2：</p>
				<p class="Arial" @click="phone('0750-3336333')">0750-3336333</p>
				<p class="dx-btn dx-btn-xs dx-btn-green-o ml10" @click="phone('0750-3336333')">拨号</p>
			</div> -->
		</div>
		<button class="lcontact text-center bg-f p0" hover-class="none" open-type="contact">
			<p class="iconfont icon-feedback fs-30 main-color"></p>
			<p class="fs-14 lh-24">在线咨询</p>
		</button>
	</div>
</template>

<script>
	export default {
		props: ['myclass'],
		data() {
			return {
				
			}
		},
		methods: {
			
		}
	}
</script>

<style>
.contact-group{background: #fff;display: flex;justify-content: center;align-items: center;padding: 20upx 30upx;}
.contact-group .row{display: flex;align-items: center;font-size: 32upx;line-height: 64upx;}
.contact-group .iconfont{line-height: 64upx;}
</style>