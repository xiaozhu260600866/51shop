<script>

	export default {
		onLaunch: function() {
			
		},
		onShow: function() {
			
			
		},
		onHide: function() {
			
		}
	}
</script>

<style>
	@import url('@/public/css/app.css');
	@import url('doxinui/css/global_upx.css');
	@import url('doxinui/css/global_icon.css');
	@import url('@/public/weui/weui.css');
	@import url("@/public/font/iconfont.css");
	
	/*每个页面公共css */
	/*  #ifdef  MP-WEIXIN */
	/*  #endif  */

	/*  #ifdef  H5 */
	.btn{padding-top: 0!important;padding-bottom: 0!important;}
	body,uni-page-body{background: #f2f2f2!important;min-height: 100%;height: auto!important;}
	.wap-top-fixed{top: 44px!important;}
	.class-left,.class-right{top: 94px!important;bottom: 50px!important;}
	uni-tabbar, uni-tabbar .uni-tabbar{z-index: 99!important;}
	.list-group .group-item uni-input input{height: 32px!important;line-height: 32px!important;}
	#footer-btn section,#footer section{width: 100%;}
	.recharge-cells{padding: 0 15px!important;}
	/*  #endif  */
	
	
	/* 脚部 */
	uni-tabbar .uni-tabbar{background: #fff!important;height: 56px;}
	uni-tabbar .uni-tabbar__icon{margin-top: 2px;height: 28px;width: 28px;}
	uni-tabbar .uni-tabbar__label {font-size: 12px !important;color: #333!important;line-height: 18px;}
	
	
</style>
