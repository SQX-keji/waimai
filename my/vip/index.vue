<template>
	<view>
		<view class="">
			<view class="margin-lr margin-top" style="position: relative;border-radius: 10upx;overflow: hidden;">
				<image src="../static/vip/vip.png" style="width: 100%;height: 250rpx;"></image>
				<view class=" u-flex u-p-l-30 u-p-t-80 u-p-b-30" style="position: absolute;top: 0;width: 100%;">
					<view class="u-m-r-20">
						<u-avatar :src="avatar" size="100"></u-avatar>
					</view>
					<view class="u-flex-1 ">
						<view class="u-font-18 text-white text-bold">{{userName}}</view>
						<view class="u-font-14 u-m-t-10 u-tips-color" style="color: #C59D7C;" v-if="!isVip">您目前还未开通会员
						</view>
					</view>
				</view>
			</view>
			<!-- <view class="flex justify-center margin-top-sm flex-wrap padding-lr-sm">
				<view style="display: inline-block;width: 216rpx;height: 250rpx;margin: 10rpx 10rpx;">
					<view class="text-center flex flex-direction justify-between padding-tb radius active"
						style="color: #DFC5A7;width: 216rpx;height: 250rpx;background: linear-gradient(-30deg, #2B2A30, #4A4A4A);border: 1px;">
						<view class="text-bold">月会员</view>
						<view class="text-bold">¥<text class="text-xxl">{{vipList.value}}</text></view>
						<view>立即购买</view>
					</view>
				</view>
			</view> -->
			<view class="flex justify-center align-center margin-top">
				<view class="" @click="showpay=true"
					style="position: relative;left: 0;right: 0;border-radius: 10upx;overflow: hidden;display: inline-block;margin: auto;">
					<image src="../static/vip/bg.png" style="width: 459rpx;height: 129rpx;"></image>
					<view class="flex align-center"
						style="position: absolute;top: -10rpx;left:30rpx;bottom: 0;margin: auto; width: 100%;height: 100%;">
						<view class="text-bold">¥<text class="text-xxl">{{vipList.value}}</text></view>
					</view>
				</view>
			</view>


			<view class="padding-tb radius margin-top margin-lr"
				style="background-color: #343339;border-radius: 10upx;">
				<view class="text-center text-xl text-bold " style="color: #CAB49C;">开通专享特权</view>
				<view class="flex  flex-wrap">
					<view v-for="(item,index) in MemberList" :key="index"
						style="width: 33%;text-align: center;margin-top: 34upx;">
						<image :src="item.memberImg" mode="" style="margin: 0 auto;height: 45rpx;width: 45rpx;"></image>
						<view class="grid-text margin-top-sm" style="color: #DFC5A7;">{{item.memberName}}</view>
					</view>
				</view>
			</view>
		</view>
		<view style="height: 110rpx;"></view>
		<view class="flex justify-between cu-bar foot bg padding-lr" v-if="!isVip">
			<view style="color: #DFC5A7;">
				实付：<text style="font-size: 38upx;margin-top: 8upx;">{{price}}</text>元
			</view>
			<view class="">
				<u-button :custom-style="customStyle" @click="showpay=true" shape="circle" :hair-line="false">立即开通
				</u-button>
			</view>
		</view>

		<!-- 支付方式 -->
		<u-popup v-model="showpay" mode="bottom" :closeable="closeable">
			<view class="popup_pay">
				<view style="background-color: #fff;">
					<view style="padding: 0 20upx;margin-top: 60rpx;margin-bottom: 20rpx;">
						<view
							style="display: flex;height: 100upx;align-items: center;padding: 20upx 0;justify-content: center;"
							v-for="(item,index) in openLists" :key='index'>
							<image :src="item.image" style="width: 55upx;height: 55upx;border-radius: 50upx;">
							</image>
							<view style="font-size: 30upx;margin-left: 20upx;width: 70%;">
								{{item.text}}
							</view>
							<radio-group name="openWay" style="margin-left: 45upx;" @tap='selectWay(item)'>
								<label class="tui-radio">
									<radio color="#1777FF" :checked="openWay === item.id ? true : false" />
								</label>
							</radio-group>
						</view>
					</view>
				</view>
				<view class="pay_btn" @click="pay()">确认支付</view>
			</view>
		</u-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				showpay: false,
				closeable: true,
				openLists: [],
				customStyle: {
					width: '250rpx',
					color: '#402321',
					background: "#DFC5A7",
					border: 0,
					fontWeight: '700'
				},
				gridData: [{
						title: '专享折上折',
						image: '../static/1.png'
					},
					{
						title: '特权礼物',
						image: '../static/2.png'
					},
					{
						title: '身份标识',
						image: '../static/3.png'
					},
					{
						title: '超值专享券',
						image: '../static/4.png'
					},
					{
						title: '商家特权',
						image: '../static/5.png'
					},
					{
						title: '定制挂件',
						image: '../static/6.png'
					}
				],
				avatar: '',
				userName: '匿名',
				vipList: [],
				selNum: 1,
				newPrice: 0,
				money: 0,
				price: 0,
				MemberList: [],
				isVip: false,
				openWay: 1
			}
		},
		onLoad() {
			// #ifdef APP
			this.openLists = [{
				image: '../../static/images/my/jinbi.png',
				text: '零钱',
				id: 1
			}, {
				image: '../../static/images/my/weixin.png',
				text: '微信',
				id: 2
			}, {
				image: '../../static/images/my/zhifubao.png',
				text: '支付宝',
				id: 3
			}]
			// #endif

			// #ifdef MP-WEIXIN
			this.openLists = [{
				image: '../../static/images/my/jinbi.png',
				text: '零钱',
				id: 1
			}, {
				image: '../../static/images/my/weixin.png',
				text: '微信',
				id: 2
			}]
			// #endif

			// #ifdef H5
			this.openLists = [{
				image: '../../static/images/my/jinbi.png',
				text: '零钱',
				id: 1
			}, {
				image: '../../static/images/my/weixin.png',
				text: '微信',
				id: 2
			},{
				image: '../../static/images/my/zhifubao.png',
				text: '支付宝',
				id: 3
			}]
			// #endif

			this.avatar = uni.getStorageSync('avatar')
			this.userName = uni.getStorageSync('userName')

			this.getVipList()
			this.getMemberList()
			this.getMoney()
			this.getIsVip()
			this.getUserInfo()
		},
		methods: {
			selectWay: function(item) {
				this.openWay = item.id;
			},
			getUserInfo() {
				this.$Request.get("/app/user/selectUserById").then(res => {
					if (res.code == 0) {
						this.avatar = res.data.avatar ? res.data.avatar : '../../static/logo.png'

						uni.setStorageSync('avatar', res.data.avatar)
					}
				});
			},
			//获取VIP列表
			getVipList() {
				this.$Request.get('/app/common/313').then(res => {
					if (res.code == 0) {
						this.vipList = res.data
						this.price = this.vipList.value
					}
				})
			},
			// 获取特权列表
			getMemberList() {
				this.$Request.get('/app/member/selectMemberList').then(res => {
					if (res.code == 0) {
						this.MemberList = res.data
					}
				})
			},
			getIsVip() {
				this.$Request.get("/app/user/selectUserMessage").then(res => {
					if (res.code == 0) {
						this.isVip = res.data.isVip==1?true:false
					}
				});
			},
			// 我的金币
			getMoney() {
				this.$Request.get("/app/userMoney/selectMyMoney").then(res => {
					if (res.code == 0 && res.data) {
						this.money = res.data.money
					}
				});
			},
			select(e) {
				this.selNum = e.id
				this.price = e.money
			},
			pay() {
				let userId = uni.getStorageSync('userId')

				this.showpay = false
				if (this.openWay == 1) { //余额支付
					this.$Request.post("/app/wxPay/balanceBuyVip").then(res => {
						if (res.code == 0) {
							this.$queue.showToast('支付成功');
							setTimeout(function() {
								uni.navigateBack()
							}, 1000)
						}else {
							this.$queue.showToast(res.msg);
						}
					});
				} else if (this.openWay == 2) { //微信支付
					// #ifdef MP-WEIXIN
					let data = {
						type: 3
					}
					this.$Request.post('/app/wxPay/wxPayJsApiBuyVip', data).then(res => {
						console.log(res)
						if (res.code == 0) {
							uni.requestPayment({
								provider: 'wxpay',
								timeStamp: res.data.timestamp,
								nonceStr: res.data.noncestr,
								package: res.data.package,
								signType: res.data.signType,
								paySign: res.data.sign,
								success: function(res) {
									console.log(res)
									uni.showLoading({
										title: '支付成功',
										icon: 'none'
									});
									setTimeout(function() {
										uni.navigateBack()
									}, 1000)
								},
								fail: function(err) {
									uni.showLoading({
										title: '支付失败',
										icon: 'nones'
									});
								}
							});
						}
					});
					// #endif


					// #ifdef H5
					let data = {
						userId: userId,
						type: 2
					}
					this.$Request.post('/app/wxPay/wxPayJsApiBuyVip', data).then(res => {
						this.showpay = false
						this.callPay(res);
					});
					// #endif

					// #ifdef APP
					let data = {
						type: 1
					}
					this.$Request.post('/app/wxPay/wxPayJsApiBuyVip', data).then(res => {
						console.log(res)
						this.showpay = false
						if (res.code == 0) {
							this.isCheckPay(res.code, 'wxpay', JSON.stringify(res.data));
						}
					});
					// #endif

				} else if (this.openWay == 3) { //支付宝支付
					// #ifdef H5
					let data = {
						type: 5
					}
					this.$Request.post('/app/wxPay/wxPayJsApiBuyVip', data).then(
						res => {
							this.showpay = false
							const div = document.createElement('div')
							div.innerHTML = res.data //此处form就是后台返回接收到的数据
							document.body.appendChild(div)
							document.forms[0].submit()
						});
					// #endif

					// #ifdef APP-PLUS
					let data = {
						type: 4
					}
					this.$Request.post('/app/wxPay/wxPayJsApiBuyVip', data).then(res => {
						this.showpay = false
						this.setPayment('alipay', res.data);
					});
					// #endif

				}
			},
			callPay: function(response) {
				if (typeof WeixinJSBridge === "undefined") {
					if (document.addEventListener) {
						document.addEventListener('WeixinJSBridgeReady', this.onBridgeReady(response), false);
					} else if (document.attachEvent) {
						document.attachEvent('WeixinJSBridgeReady', this.onBridgeReady(response));
						document.attachEvent('onWeixinJSBridgeReady', this.onBridgeReady(response));
					}
				} else {
					this.onBridgeReady(response);
				}
			},
			onBridgeReady: function(response) {
				let that = this;
				console.log(response)
				if (!response.package) {
					return;
				}
				WeixinJSBridge.invoke(
					'getBrandWCPayRequest', {
						"appId": response.appid, //公众号名称，由商户传入
						"timeStamp": response.timestamp, //时间戳，自1970年以来的秒数
						"nonceStr": response.noncestr, //随机串
						"package": response.package,
						"signType": response.signType, //微信签名方式：
						"paySign": response.sign //微信签名
					},
					function(res) {
						if (res.err_msg === "get_brand_wcpay_request:ok") {
							// 使用以上方式判断前端返回,微信团队郑重提示：
							//res.err_msg将在用户支付成功后返回ok，但并不保证它绝对可靠。
							uni.showLoading({
								title: '支付成功'
							});
							uni.hideLoading();

							uni.navigateTo({
								url: '/pages/my/index'
							})
						} else {
							uni.hideLoading();
						}
						WeixinJSBridge.log(response.err_msg);
					}
				);
			},
			isCheckPay(code, name, order) {
				if (code == 0) {
					console.log('999999999999')
					this.setPayment(name, order);
				} else {
					uni.hideLoading();
					uni.showToast({
						title: '支付信息有误'
					});
				}
			},
			setPayment(name, order) {
				console.log(777777777, name, order)
				uni.requestPayment({
					provider: name,
					orderInfo: order, //微信、支付宝订单数据
					success: function(res) {
						uni.hideLoading();
						uni.showLoading({
							title: '支付成功',
							icon: 'none'
						});
						setTimeout(function() {
							uni.navigateBack()
						}, 1000)
					},
					fail: function(err) {
						uni.hideLoading();
					},
					complete() {
						uni.hideLoading();
					}
				});
			}
		},
		computed: {

		}
	}
</script>

<style>
	.bg {
		background-color: #1E1F31;
	}

	.btn {
		width: 100%;
		height: 88upx;
		background: linear-gradient(0deg, #af8262 0%, #cab49c 100%);
		border-radius: 44upx;
		text-align: center;
		line-height: 88upx;
		margin-top: 40upx;
		font-size: 34upx;
		font-weight: 600;
		color: #402321;
	}

	.active {
		border: 1px solid #cab49c !important;
		border-radius: ;
	}

	.popup_pay {
		width: 100%;
		position: relative;
		padding-bottom: 45rpx;
	}

	.pay_btn {
		width: 90%;
		margin: 0 auto;
		text-align: center;
		background: #FCD202;
		height: 80rpx;
		border-radius: 16rpx;
		color: #ffffff;
		line-height: 80rpx;

	}
</style>
