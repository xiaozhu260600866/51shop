<template>
	<view class="pb50">
		<page :parentData="data" :formAction="formAction" ref="page"></page>
		
		<view v-if="data.show && ruleform.fclass!=238 && ruleform.dianxin == 0">
			<!-- 普通订单 -->
			<view v-if="ruleform.staff_userid" class="delivery bg-f flex-center flex-middle p15 mb10" @click="goto('/pages/order/map/main?order_no='+ruleform.order_no,1)">
				<view class="iconfont icon-order-delivery fc-blue fs-24"></view>
				<view class="name fs-16 pl10">配送员位置</view>
			</view>
			<view v-if="data.show && ruleform.type == 0">
				<view id="address" class="order-sec plr10 mb0" v-if="ruleform.shipping !=1">
					<block v-if="!address">
						<view class="add-add ptb15" @click="createAddress">
							<p class="add-icon iconfont icon-count-plus mr10"></p>
							<p class="add-txt fs-16">新增收货地址</p>
							<p class="iconfont icon-right fs-12 fc-9"></p>
						</view>
					</block>
					<block v-else>
						<view class="add-info" @click="addressLists" v-if="address.name">
							<view class="licon pr15">
								<span class="iconfont icon-location fs-22 main-color"></span>
							</view>
							<view class="info ptb10">
								<p class="name fs-16">
									<span>{{address.name}}</span>
									<span class="Arial pl10">{{address.phone}}</span>
								</p>
								<view class="add-detail fs-15 fc-6">
									<p class="label">地址：</p>
									<p class="name">{{ address.province }} {{ address.city }} {{ address.area }} {{ address.address }}</p>
								</view>
							</view>
							<p class="iconfont icon-right fs-12 fc-9 pl15"></p>
						</view>
					</block>
				</view>
				<view class="pay_line bg-f mb12" v-if="ruleform.shipping !=1">
					<image :src="getSiteName+'/images/wap/share-tip.png'"></image>
				</view>
				<view class="order-sec">
					<orderPro :data="ruleform.products" url="/pages/product/show/main?id="></orderPro>
				</view>
				
				<!-- 回桶、押桶、买桶选择，三者选一个 -->
				<view class="order-sec">

					<weui-input @radioCallBack="bucketCallBack" v-model="ruleform.bucket_type" name="bucket_type" changeField="label"
					 :type="ruleform.status>=3 ?'txt':'radio'" dataKey="bucketTypeArr" myclass="exchangeSon"></weui-input>
					<view class="relative">
						<weui-input v-model="ruleform.bucket_value" name="bucket_value" changeField="label" :type="ruleform.status>=3?'txt':'radio'"
						 dataKey="exchangeSonArr" myclass="exchangeSon" v-if="ruleform.bucket_type == '回桶'" :row="true" Labelleft></weui-input>
						<view class="flex-middle orderBrand" v-if="ruleform.bucket_value == '其他品牌' && ruleform.bucket_type == '回桶'">
							<weui-input v-model="ruleform.bucket_brand" placeholder="请选择品牌" myclass="Brand" name="bucket_brand" changeField="value"
							 type="select" dataKey="orderBrandArr"></weui-input>
							<weui-input v-model="ruleform.bucket_num" label="数量" myclass="Num" name="bucket_num" changeField="value" type="select"
							 dataKey="orderNumArr"></weui-input>
						</view>
						<view class="pl40 pb10 fs-16 lh-24" v-if="ruleform.bucket_value == '其他品牌' && ruleform.bucket_type == '回桶'">
							<!-- 春节期间免换桶费10元。 -->
							<!-- 换桶：10元每个。<br>新用户下单24小时内返还换瓶费10元/个 -->
							<!-- 温馨提示:<br>换桶费10元/个 -->
							</view>
					</view>
					<view class="tong" v-if="ruleform.bucket_type == '押桶'">
						<view class="list">押桶：{{exchangeSonArr[0].label}}</view>
						<view class="list">
							<view>温馨提示：</view>
							<view class="con fc-6 lh-24">押桶：50元一个。<br>每个空桶每年使用费10元。（不足一年按一年收取使用费）</view>
						</view>
					</view>
					<view class="tong" v-if="ruleform.bucket_type == '买桶'">
						<view class="list">买桶：{{exchangeSonArr[0].label}}</view>
						<view class="list">
							<view>温馨提示：</view>
							<view class="con fc-6 lh-24">买桶：15元一个。</view>
						</view>
					</view>
					
					<!-- <weui-input v-model="ruleform.bucket_num" label="桶数" name="bucket_num" changeField="label" :type="ruleform.status>=3?'txt':'select'" dataKey="bucketNumArr" placeholder="选择桶数"  v-if="ruleform.bucket_type != '回桶'"></weui-input> -->
				</view>
				<!-- 回桶、押桶、买桶选择，三者选一个 -->
				
				<view id="mode" class="buy-info order-sec">
					<view v-if="ruleform.shipping == 1">
						<weui-input v-model="ruleform.addr_name" label="姓名" type="text" name="addr_name" datatype="require"></weui-input>
						<weui-input v-model="ruleform.addr_phone" label="电话" type="text" name="addr_phone" datatype="require|phone"></weui-input>
					</view>
					<!-- <view :class="['weui-cell','weui-cell_input','bd-be']" @click="previewImage(ruleform.order_no+'.png','order')" v-if="ruleform.status >=3">
						<view class="weui-cell__hd">
							<slot name="left" />
							<view class="weui-label fs-16">券码</view>
						</view>
						<view class="weui-cell__bd text-right"></view>
						<view class="weui-cell__ft flex">
							<img :src="getSiteName+'/upload/images/order/'+ruleform.order_no+'.png'" style="width:25px;height:25px">
							<view class="weui-cell__ft_in-access"></view>
						</view>
					</view> -->
					<!-- <weui-input v-model="ruleform.shipping" name="shipping" datatype="require" label="送货方式" changeField="value" type="select"
					 dataKey="shipping" :disabled="ruleform.fclass == 109 || ruleform.fclass == 110 ||ruleform.status >=3  || ruleform.type == 1? true :false"
					 @callback="selectCallBack" v-if='ruleform.is_hot == 0'></weui-input> -->
					<weui-input v-model="ruleform.shipping" name="shipping" datatype="require" label="送货方式" changeField="value" type="select"
					 dataKey="shipping" :disabled="true" @callback="selectCallBack" v-if='ruleform.is_hot == 0'></weui-input>
					<view :class="['weui-cell','weui-cell_input','p10','bd-be','industry']" @click="chooseMerchant" v-if="ruleform.shipping == 1 && ruleform.is_hot == 0">
						<view class="weui-cell__hd">
							<view class="weui-label fs-16">选择{{ruleform.type == 0? "门店":"自提点"}}<span class="fc-red">*</span></view>
						</view>
						<view class="weui-cell__bd">
							<view class="fc-3 fs-16 nowrap text-right">
								{{ ruleform.merchant_user_id ?ruleform.merchant_user_name :'请选择' }}
							</view>
						</view>
						<view class="weui-cell__ft weui-cell__ft_in-access"></view>
					</view>
					<!-- <view class="weui-cell weui-cell_input bd-be" v-if="data.coupons && data.coupons.length == 0">
						<view class="weui-cell__bd"><label class="fs-16">优惠券</label></view>
						<view class="weui-cell__ft"><label class="fs-16 fc-3">无可用</label></view>
					</view>
					<weui-input v-model="ruleform.coupon_value" name="coupon_value" label="优惠券" changeField="value" type="select"
					 dataKey="coupons" v-if="data.coupons && data.coupons.length > 0 && ruleform.is_hot == 0" :disabled="ruleform.status >=3 ? true :false"
					 @callback="couponCallBack"></weui-input> -->
					<weui-input v-model="ruleform.pay_method" name="pay_method" datatype="require" label="付款方式" changeField="value"
					 type="select" dataKey="pay_methods"  v-if="ruleform.fclass != 109 &&ruleform.fclass != 110"></weui-input>
					<weui-input v-model="ruleform.getPayMethod" name="pay_method" datatype="require" label="付款方式" changeField="value"
					 type="select" :disabled="ruleform.status >=3 ? true :false" v-if="ruleform.fclass == 109 || ruleform.fclass == 110"></weui-input>
					<weui-input v-model.lazy="ruleform.myAccount" :disabled="true" label="余额" type="text" name="name" v-if="ruleform.pay_method == 2"></weui-input>
					<weui-input v-model="ruleform.remark" label="买家留言" type="text" name="remark" placeholder="点击给商家留言"></weui-input>
					<!-- <weui-input v-model="ruleform.merchant_user_id" name="merchant_user_id" datatype="require" label="门店" changeField="value" type="select" :data="data.merchantUser" :disabled="ruleform.status >=3 ? true :false" v-if="ruleform.shipping == 1"></weui-input> -->

				</view>
				<view id="calculation" class="order-sec">
					<view class="list-group">
						<p class="txt">商品金额</p>
						<p class="fs-16 price">￥{{ruleform.amount}}</p>
					</view>
					<!-- 根据上述选择，三选其一显示 -->
					<view class="list-group" v-if="ruleform.bucket_type == '回桶'">
						<p class="txt">换桶费</p>
						<p class="fs-16 price">￥{{exchangeBucket}}</p>
					</view>
					<view class="list-group" v-if="ruleform.bucket_type == '押桶'">
						<p class="txt">押桶</p>
						<p class="fs-16 price">￥{{exchangeBucket}}</p>
					</view>
					<view class="list-group" v-if="ruleform.bucket_type == '买桶'">
						<p class="txt">买桶</p>
						<p class="fs-16 price">￥{{exchangeBucket}}</p>
					</view>
					<!-- 根据上述选择，三选其一显示 -->
					<view class="list-group" v-if="ruleform.shipping == 2">
						<p class="txt">运费</p>
						<p class="fs-16 price">+ ￥{{ ruleform.yunfei }}</p>
					</view>
					<!-- <view class="list-group" v-if="ruleform.coupon_value">
						<p class="txt">优惠券</p>
						<p class="fs-16 price">- ￥{{ ruleform.coupon_value }}</p>
					</view>
					<view class="list-group">
						<p class="txt">余额</p>
						<p class="fs-16 price">- ￥{{ wallet}}</p>
					</view> -->
				</view>				
				<view id="footer" v-if="ruleform.status == 1">
					<view class="f_left price fs-18 plr10">￥
						<span class="fs-24 fw-bold">{{amount}}</span>
					</view>
					<view class="f_right">
						<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="submit" myclass="nav" title="提交订单"></myform>
					</view>
				</view>
				<view id="footer" v-else-if="ruleform.status == 5">
					<view class="f_left"></view>
					<view class="f_right flex-middle mlr5"><view class="nav" @click="canReceipt">确认收货</view></view>
				</view>
				<view id="footer" class="bd-t" v-else>
					<view class="f_left"></view>
					<view class="f_right flex-middle mlr5"><view class="nav">{{ruleform.status_message}}</view></view>
				</view>
				
			</view>
			<!-- 普通订单结束 -->
			<!-- 折扣订单 -->
			<view v-if="data.show && ruleform.type == 1">
			<!-- 	<view id="address" class="bg-f mb12">
					<block v-if="!address">
						<view class="add-add ptb15 plr10" @click="createAddress">
							<p class="add-icon iconfont icon-count-plus mr10"></p>
							<p class="add-txt fs-16">新增收货地址</p>
							<p class="iconfont icon-right fs-12 fc-9"></p>
						</view>
					</block>
					<block v-else>
						<view class="add-info plr10" @click="addressLists" v-if="address.name">
							<view class="licon pr15">
								<span class="iconfont icon-location fs-22 main-color"></span>
							</view>
							<view class="info ptb10">
								<p class="name fs-16">
									<span>{{address.name}}</span>
									<span class="Arial pl10">{{address.phone}}</span>
								</p>
								<view class="add-detail fs-15 fc-6">
									<p class="label">地址：</p>
									<p class="name">{{ address.province }} {{ address.city }} {{ address.area }} {{ address.address }}</p>
								</view>
							</view>
							<p class="iconfont icon-right fs-12 fc-9 pl15"></p>
						</view>
					</block>
					<view class="pay_line">
						<image :src="getSiteName+'/images/site/share-tip.png'"></image>
					</view>
				</view> -->
				<view class="order-sec">
					<orderPro :data="ruleform.products" url="/pages/product/show/main?id="></orderPro>
				</view>
				<view class="order-sec">
					<view class="plr15 pt15 pb5 lh-1 fs-17">联系信息</view>
					<weui-input v-model="ruleform.addr_name" label="姓名" type="text" name="addr_name" datatype="require"></weui-input>
					<weui-input v-model="ruleform.addr_phone" label="电话" type="number" name="addr_phone" datatype="require|phone"></weui-input>
					<weui-input v-model="ruleform.addr_address" label="地址" type="text" name="addr_address" datatype="require" v-if="ruleform.products[0].getProduct.address_require"></weui-input>
				</view>
				<view id="mode" class="buy-info order-sec">
					<!-- <view :class="['weui-cell','weui-cell_input','bd-be']" @click="previewImage(ruleform.order_no+'.png','order')" v-if="ruleform.status >=3">
						<view class="weui-cell__hd">
							<slot name="left" />
							<view class="weui-label fs-16">券码</view>
						</view>
						<view class="weui-cell__bd text-right"></view>
						<view class="weui-cell__ft flex">
							<img :src="getSiteName+'/upload/images/order/'+ruleform.order_no+'.png'" style="width:25px;height:25px">
							<view class="weui-cell__ft_in-access"></view>
						</view>
					</view> -->
					<view v-if="ruleform.type == 1">
						<weui-input v-model="ruleform.pay_method" name="pay_method" datatype="require" label="付款方式" changeField="value"
						 type="select" dataKey="pay_methods" :disabled=" ruleform.status>=3 ? true :false" v-if="ruleform.fclass != 109 &&
							ruleform.fclass != 110"></weui-input>
						<weui-input v-model.lazy="ruleform.myAccount" :disabled="true" label="余额" type="text" name="name"
						 v-if="ruleform.pay_method == 2"></weui-input>
						<weui-input v-model="ruleform.remark" label="买家留言" type="text" name="remark" placeholder="点击给商家留言"></weui-input>
					</view>
				</view>
				<view id="calculation" class="order-sec">
					<view class="list-group">
						<p class="txt">商品金额</p>
						<p class="fs-16 price">￥{{ruleform.amount}}</p>
					</view>
					
					<view class="list-group" v-if="ruleform.shipping == 2">
						<p class="txt">运费</p>
						<p class="fs-16 price">+ ￥{{ ruleform.yunfei }}</p>
					</view>
					<view class="list-group" v-if="ruleform.coupon_value">
						<p class="txt">优惠券</p>
						<p class="fs-16 price">- ￥{{ ruleform.coupon_value }}</p>
					</view>
					<!-- <view class="list-group">
						<p class="txt">余额</p>
						<p class="fs-16 price">- ￥{{ wallet}}</p>
					</view> -->
				</view>
				<view id="footer" v-if="ruleform.status == 1">
					<view class="f_left price fs-18 plr10">￥
						<span class="fs-24">{{amount}}</span>
					</view>
					<view class="f_right">
						<myform :ruleform="ruleform" :vaildate="vaildate" @callBack="submit" myclass="nav" :title="isMoonCakeCoupon && coupons.length ? '立即使用' : '提交订单'"></myform>
					</view>
				</view>
			<!-- 	<view id="footer" v-else-if="ruleform.status == 3" @click="changeOrder(ruleform)">
					<view class="f_left"></view>
					<view class="f_right flex-middle mlr5">
						<view class="nav-o">申请售后</view>
					</view>
				</view> -->
				<view id="footer" v-else-if="ruleform.status == 5">
					<view class="f_left"></view>
					<view class="f_right flex-middle mlr5">
						<!-- <view class="nav-o" @click="goto('/pages/order/after-sale/main?order_no='+ruleform.order_no,1)">售后</view> -->
						<view class="nav" @click="canReceipt">确认收货</view>
					</view>
				</view>
				<view id="footer" class="bd-t" v-else>
					<view class="f_left"></view>
					<view class="f_right flex-middle mlr5"><view class="nav-o">完成</view></view>
				</view>
			</view>
		</view>
		<view v-else-if="data.show && ruleform.dianxin ==1">
			<view class="order-sec">
				<orderPro :data="ruleform.products"></orderPro>
			</view>
			<view id="mode" class="order-sec">
				<weui-input v-model="ruleform.addr_name" label="姓名" type="text" name="addr_name" datatype="require"></weui-input>
				<weui-input v-model="ruleform.addr_phone" label="电话" type="text" name="addr_phone" datatype="require|phone"></weui-input>
				<dxAddress v-model="ruleform.addr_address" padding="plr10"></dxAddress>
			</view>
			
			<view id="footer" v-if="ruleform.status == 1">
				<view class="f_right flex1">
					<myform myclass="nav" :ruleform="ruleform" :vaildate="vaildate" @callBack="submit" title="马上办理"></myform>
				</view>
			</view>
			<view id="footer" v-else-if="ruleform.status == 3 || ruleform.status == 5">
				<view class="f_left"></view>
				<view class="f_right">
					<view class="nav" @click="canReceipt">确认收货</view>
				</view>
			</view>
			<view id="footer" v-else>
				<view class="f_left"></view>
				<view class="f_right">
					<view class="nav-o" >完成</view>
				</view>
			</view>
		</view>
		<view v-else-if="data.show && ruleform.fclass ==106 && ruleform.dianxin == 0">
			<coupon :ruleform="ruleform" :data="data" :amount="amount" :vaildate="vaildate"></coupon>
		</view>
	</view>
</template>
<script>
	import dxAddress from "xiaozhu/uniapp/components/addressAndCity";
	import "./index.css";
	import other from "./layouts/other";
	import dianxin from "./layouts/dianxin";
	import coupon from "./layouts/coupon";
	import uParse from '@/components/gaoyia-parse/parse.vue'
	import orderPro from "@/components/orderPro";
	export default {
		components: {
			other,
			dianxin,
			coupon,
			uParse,
			orderPro,
			dxAddress
		},
		data() {
			return {
				formAction: '/shop/order/detail',
				mpType: 'page', //用来分清父和子组件
				data: this.formatData(this),
				getSiteName: this.getSiteName(),
				maskCheck:false,
				value: 1,
				shipping: [
					{label: '自提',value: 1},
					{label: '送货上门',value: 2},
				],
				shipping2: [
					{label: '送货上门（满50元免配送费）',value: 2},
				],
				sendTypeArr: [
					{label: '配送到户',value: '配送到户',},
					{label: '退回商家（3折）',value: '退回商家（3折）',},
					{label: '公益赠送',value: '公益赠送'},
				],
				pay_methods: [
					/* {label: '翼支付',value: 3}, */
					{label: '微信支付',value: 1},
					 {label: '余额支付',value: 2},
				],
				bucketTypeArr: [
					{label: '回桶',value: '回桶'},
					// {label: '押桶',value: '押桶'},
					{label: '买桶',value: '买桶'},
				],
				exchangeSonArr: [
					{label: '华山泉*4',value: '华山泉*4',amount:0},
					{label: '其他品牌',value: '其他品牌',amount:10},
				],
				orderBrandArr: [
					{label: '华山泉',value: '华山泉'},
					{label: '红百立',value: '红百立'},
					{label: '绿百立',value: '绿百立'},
					{label: '全清纯',value: '全清纯'},
					{label: '七翁井',value: '七翁井'},
					{label: '七井清泉',value: '七井清泉'},
					{label: '怡宝',value: '怡宝'},
					{label: '加伦加',value: '加伦加'},
					{label: '长寿村',value: '长寿村'},
					{label: '一品怡',value: '一品怡'},
					{label: '农夫山泉',value: '农夫山泉'},
					{label: '鼎湖山泉',value: '鼎湖山泉'},
					{label: '屈臣氏',value: '屈臣氏'},
				],
				orderNumArr: [
					{label: '1',value: '1'},
					{label: '2',value: '2'},
					{label: '3',value: '3'},
					{label: '4',value: '4'},
					{label: '5',value: '5'},
					{label: '6',value: '6'},
					{label: '7',value: '7'},
					{label: '8',value: '8'},
					{label: '9',value: '9'},
				],
				bucketNumArr: [
					{label: '1个 50元',value: '1个 50元',amount:50},
					{label: '2个 100元',value: '2个 100元',amount:100},
					{label: '3个 150元',value: '3个 150元',amount:150},
					{label: '4个 200元',value: '4个 200元',amount:200},
					{label: '5个 250元',value: '5个 250元',amount:250},
					{label: '6个 300元',value: '6个 300元',amount:300},
					{label: '7个 350元',value: '7个 350元',amount:350},
					{label: '8个 400元',value: '8个 400元',amount:400},
					{label: '9个 450元',value: '9个 450元',amount:450},
					{label: '10个 500元',value: '10个 500元',amount:500},
				],
				ruleform: {
					bucketType:1,
					echange: 1,
					orderNum: '1',
					need:'须知须知须知须知须知须知',
					yatong:'华山泉*4',
					maitong:'华山泉*4',
				},
				vaildate: {},
				ps_timeArr: [],
				actIndex: 0,
				order_no: 0,
				address_id: 0,
				address: {},
				coupons: [],
				openNotice: false,
				isMoonCakeCoupon:false,
			}
		},
		computed: {
			exchangeBucket(){
				let res = 0 ;
				if(this.ruleform.bucket_type== '回桶' &&this.ruleform.bucket_value == '其他品牌'){
					//res+=10 * parseInt(this.ruleform.bucket_num);
					res+=0;
				}else if(this.ruleform.bucket_type== '押桶' ){
					  res+= 50 * parseInt(this.ruleform.num);;
				}else if(this.ruleform.bucket_type == '买桶'){
					  res+= 15 * parseInt(this.ruleform.num);;
				}
				return res;
			},
			amount() {
				//如果是微信支付
				let coupon_value = parseFloat(this.ruleform.coupon_value);
				this.ruleform.amount = parseFloat(this.ruleform.amount);
				let order_amount = this.ruleform.amount;
				if (this.ruleform.shipping == 2) {
					order_amount += parseFloat(this.ruleform.yunfei);
				}
				/*如果有优惠券*/
				if (coupon_value) {
					order_amount -= coupon_value;
				}
				/*如果余额支付*/
				if (this.ruleform.pay_method == 2) {
					order_amount -= this.ruleform.myAccount;
				}
				if(this.exchangeBucket >0){
					order_amount+= parseFloat(this.exchangeBucket);
				}

				return order_amount < 0 ? 0 : order_amount.toFixed(2);
			},
			wallet() {
				let coupon_value = parseFloat(this.ruleform.coupon_value);
				this.ruleform.amount = parseFloat(this.ruleform.amount);
				let order_amount = this.ruleform.amount;
				if (this.ruleform.shipping == 2) {
					order_amount += parseFloat(this.ruleform.yunfei);
				}
				if (coupon_value) {
					order_amount -= coupon_value;
				}
				/*如果余额支付*/
				if (this.ruleform.pay_method == 2) {
					let res = this.ruleform.myAccount - order_amount;
					if (res > 0) {
						return order_amount;
					} else {
						return this.ruleform.myAccount;
					}
					return order_amount;
				} else {
					return 0;
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
			this.shareSource(this, '商城');
		},
		onLoad(options) {
			wx.removeStorageSync('package');
			this.vaildate = {};
			if (options.order_no) {
				this.ruleform.order_no = options.order_no;
				this.order_no = options.order_no;
			}
			if (options.address_id) {
				this.address_id = options.address_id;
			}
			if (options.merchant_user_id) {
				this.ruleform.merchant_user_id = options.merchant_user_id;
				this.ruleform.merchant_user_name = options.merchant_user_name;
				if(options.deliver_type_)this.ruleform.deliver_type_ = options.deliver_type_;
			} else {
				this.ajax();
			}
		},
		onShow(){
			this.ruleform.bucket_type = "";
		},
		methods: {
			bucketCallBack(){
				
				
			},
			changeMask(){
				this.maskCheck = !this.maskCheck;
			},
			changeOrder(item) {
				let title = item.status == 1 ? "您确认要取消订单吗？" : "您确认要申请售后吗？订单金额24小时内原路退回";
				this.getConfirm(title, () => {
					if(item.status == 1){
						this.postAjax("/ajax/mydel",{tablename:'orders',id:item.id}).then(msg=>{
							if (msg.data.status == 2) {
								this.data.nextPage = 1;
								this.ajax();
							}
						})
					}else{
						this.postAjax("/user/change-order-status", {
							id: item.id,
							status: 0
						}).then(msg => {
							if (msg.data.status == 2) {
								this.data.nextPage = 1;
								this.ajax();
							}
						});
					}
					
				});
			},
			couponCallBack(res) {
				this.ruleform.coupon_userid = res.id;
			},
			selectCallBack(item) {
				if (item.value == 2) {
					delete this.vaildate.merchant_user_id;
				}
			},
			chooseMerchant() {
				if (this.ruleform.type == 0) {
					this.goto('/pages/business/choose/main?order_no=' + this.ruleform.order_no + '&type=' + this.ruleform.type, 1);
				} else {
					this.goto('/pages/ztd/choose/main?order_no=' + this.ruleform.order_no + '&type=' + this.ruleform.type+'&shipping='+this.ruleform.shipping, 1);
				}
			},
			text(){
				this.ruleform.merchant_user_id = "";
				this.ruleform.merchant_user_name = "";
			    this.ruleform.deliver_type_ ="";
			},
			ajax() {
				this.getAjax(this).then(msg => {
					this.address = msg.address;
					this.ruleform = msg.detail;
					this.ruleform.myAccount = msg.user.amount;
					this.ruleform.coupon_value = this.ruleform.coupon_value != "0.00" ? this.ruleform.coupon_value : '';
					
					if(this.ruleform.fclass !=238){
						this.ruleform.addr_name = msg.user.userInfo.name;
						this.ruleform.addr_phone = msg.user.userInfo.phone;
						this.ruleform.addr_address = msg.user.userInfo.address;
					}
					if(this.ruleform.status !=1){
						this.maskCheck = true;	
					}
					this.coupons = this.data.coupons;
					if(this.coupons.length){
						this.coupons.forEach(v=>{
							if(v.wechat_card_id){
								this.isMoonCakeCoupon = true;
							}
						});
					}
					if( this.isMoonCakeCoupon && msg.coupons.length){
						this.ruleform.deliver_name = '';	
						this.ruleform.coupon_userid = msg.coupons[0].id;
						this.ruleform.coupon_value = msg.coupons[0].value;
					}else{
						this.ruleform.deliver_name = '送货上门（满50元免配送费）';
					}
					
					
					this.$set(this.ruleform, "provinces", [0, 0]);
					
					if (this.ruleform.merchant_user_id && !this.ruleform.merchant_user_name) {
						this.ruleform.merchant_user_name = this.ruleform.merchantName.company_name;
					}
					if (this.ruleform.type == 1) {
						let ztd = uni.getStorageSync('ztd');
						if (ztd) {
							this.ruleform.merchant_user_id = ztd.id;
							this.ruleform.merchant_user_name = ztd.userInfo.company_name;
							this.ruleform.deliver_type_ = ztd.deliver_type_;
						}

						let hour = new Date().getHours();
						this.ps_timeArr = [];
						if (hour < 11) {
							// this.ps_timeArr.push({
							// 	label: '下午五点',
							// 	value: '下午五点'
							// });
							this.ps_timeArr.push({
								label: '明天上午11点',
								value: '明天上午11点'
							});
							this.ps_timeArr.push({
								label: '明天下午5点',
								value: '明天下午5点'
							});
							if (!this.ruleform.ps_time) {
								this.ruleform.ps_time = "";
							}
						} else {
							this.ps_timeArr.push({
								label: '明天上午11点',
								value: '明天上午11点'
							});
							this.ps_timeArr.push({
								label: '明天下午5点',
								value: '明天下午5点'
							});
							if (!this.ruleform.ps_time) {
								this.ruleform.ps_time = "";
							}
						}

					}
					console.log('q001');
					this.ruleform.bucket_num = this.ruleform.num;
					this.orderBrandArr = msg.brand;
					msg.detail.products.forEach(v=>{
						if(v.getProduct &&  v.getProduct.brand_name){
						
							this.exchangeSonArr[0]={label: v.getProduct.brand_name+ '×'+this.ruleform.bucket_num,value: v.getProduct.brand_name,amount:0}
							if(!this.ruleform.bucket_value)this.ruleform.bucket_value = this.exchangeSonArr[0].value;
							this.ruleform.bucket_brand = ""
						}
					})
				});
			},
			createAddress() {
				if (this.ruleform.status >= 3) {
					return false;
				}
				uni.setStorageSync('order_no', this.ruleform.order_no);
				return this.goto("/pages/user/address/create_edit/main", 1);
				this.createAddressUniapp(res => {
					if (res) {
						this.goto('/pages/order/buy/main?order_no='+this.ruleform.order_no)
					} else {
						this.goto("/pages/user/address/create_edit/main", 1);
					}
				});
			},
			submit(e) {
			
				if(!this.ruleform.deliver_name && this.ruleform.wechat_card_id && this.coupons.length){
					return this.getError('请选择兑回方式');
					
				}
				if(this.ruleform.type == 0 && !this.ruleform.bucket_type){
					return this.getError('请选择回桶类型');
				}
				if(this.ruleform.type == 0 && this.ruleform.bucket_type=='回桶' &&  this.ruleform.bucket_value=='其他品牌' && !this.ruleform.bucket_brand){
					return this.getError('请选择品牌');
				}
				
				let ruleform = this.ruleform;
				ruleform.area = uni.getStorageSync("waterCityData").area;
				if(ruleform.dianxin == 0){
					if(this.ruleform.fclass == 238 && !this.maskCheck){
						 this.getError("请同意口罩国际寄递风险需知");
						 return false;
					}
					if(ruleform.type ==0){
						if (ruleform.shipping != 1  && ruleform.fclass !=238) {
							if (!this.address || !this.address.id) {
								this.getError("请填写收货地址");
								setTimeout(() => {
									this.createAddress();
								}, 500)
								return false;
							}
							ruleform.address_id = this.address.id;
						}
					}else{
						
						// if (!this.address || !this.address.id) {
						// 	this.getError("请填写收货地址");
						// 	setTimeout(() => {
						// 		this.createAddress();
						// 	}, 500)
						// 	return false;
						// }
						// ruleform.address_id = this.address.id;
						
					}
					
					if (ruleform.coupon_value) {
						this.data.coupons.forEach((v, i) => {
							if (v.checked) {
								this.ruleform.coupon_userid = v.id;
							}
						});
					}
					if (this.ruleform.type == 0 && this.ruleform.shipping == 1 && !this.ruleform.merchant_user_id) {
						this.getError("请选择自提点");
						return false;
					}
					// if (this.ruleform.type == 1 &&  !this.ruleform.merchant_user_id) {
					// 	this.getError("请选择自提点");
					// 	return false;
					// }
				}
				this.ruleform.bucket_amount = this.exchangeBucket;
				this.postAjax(this.formAction, ruleform).then(msg => {
					if (msg.data.status == 2) {
						if (ruleform.dianxin == 1 || ruleform.fclass == 106 || ruleform.fclass == 109 || ruleform.fclass == 110) {
							return this.goto("/pages/order/payed/main?order_no=" + ruleform.order_no);
							return false;
						}
						if (this.amount == 0) {
							if (ruleform.is_group) {
								this.goto('/pages/group/group/index?order_no=' + ruleform.order_no);
							} else {
								this.goto('/pages/order/payed/main?order_no=' + ruleform.order_no);
							}
							return false;
						}
						if (ruleform.pay_method == 2 && parseFloat(ruleform.myAccount) > ruleform.amount) {
							this.goto("/pages/order/payed/main?order_no=" + ruleform.order_no);
							return false;
						}
						if (this.ruleform.pay_method == 3) {
							this.goto("/pages/ypay/main?order_no=" + ruleform.order_no + '&source=order', 1);
						} else {
							this.goto("/pages/order/pay_center/main?order_no=" + ruleform.order_no);
						}

					}
				});;

			},
			addressLists() {
				if (this.ruleform.status >= 3) {
					return false;
				}
				uni.setStorageSync('order_no', this.ruleform.order_no);
				this.goto('/pages/user/address/lists/main?order_no=' + this.ruleform.order_no);
			},
			canReceipt() {
				this.receipt("确认收货吗");
			},
			radioChange: function(event) {
				this.ruleform.pay_method = event.mp.detail.value;
			},
			receipt(title) {
				this.getConfirm(title, msg => {
					this.postAjax("/shop/user/change-order-status", this.ruleform).then(msg => {
						if (msg.data.status == 2) {
							this.goto("/pages/order/lists/main?historyUrl=del&status=" + msg.data.detail.status);
						}
					});
				});

			}
		}
	}
</script>
