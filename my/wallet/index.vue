<template>
	<view class="pages">
		<!-- 余额 -->
		<view class="wallet">
			<view class="wallet_header">
				<view class="wallet_header_text">当前余额</view>
				<view class="wallet_header_xin">
					<view class="wallet_header_le">{{myMoney}} <text> 元</text></view>
				</view>
			</view>
		</view>
		<!-- 充值 -->
		<view class="wallet_topup">
			<view class="wallet_header_text">充值金额</view>
			<view class="wallet_header_xin2">
				<view class="wallet_header_xin2_sty" v-for="(item,index) in wallet" :key='index' :class="{topup:item.isSelect}" @tap="active(item)">
					
					<view class="topup_img" v-if="item.isSelect">
						<image src="../static/wallet/face.png" mode=""></image>
					</view> 
					充值<text>{{item.money}}</text>元
				</view>
			</view>
		</view>
		<!-- 消费 -->
		<view class="wallet_header_consum" @click="record()">
			<view class="wallet_header_consum_le">消费记录</view>
			<view class="wallet_header_consum_ri">
				<image src="../static/wallet/right1.png" mode="scaleToFill"></image>
			</view>
		</view>
		<!-- 立即充值 -->
		<view class="wallet_bottom" @click="showpay = true" >立即充值</view>
		
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
				<view class="pay_btn" @click="payment()">确认支付</view>
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
				openWay: 2,
				
				myMoney: 0,
				wallet: [],
				thisSelect: {}
			}
		},
		onLoad() {
			// #ifdef APP
			this.openLists = [ {
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
				image: '../../static/images/my/weixin.png',
				text: '微信',
				id: 2
			}]
			// #endif
			
			// #ifdef H5
			this.openLists = [ {
				image: '../../static/images/my/weixin.png',
				text: '微信',
				id: 2
			}, {
				image: '../../static/images/my/zhifubao.png',
				text: '支付宝',
				id: 3
			}]
			// #endif
			
		},
		onShow() {
			this.deploy()
			this.getMoneyNum()
			
		},
		methods: {
			selectWay: function(item) {
				this.openWay = item.id;
			},
			deploy() {
				this.$Request.get("/app/topupmoney/selectTopUpMoney").then(res => {
					if (res.code == 0) {
						res.data.forEach(res=> {
							res.isSelect = false
						})
						this.wallet = res.data
					}
				});
			},
			active(e) {
				this.wallet.forEach(res => {
					if (res.id == e.id) {
						res.isSelect = true
						this.thisSelect = e
					} else {
						res.isSelect = false
					}
				})
				console.log(this.wallet)
			},
			record() {
				uni.navigateTo({
					url: '/my/wallet/walletDet'
				})
			},
			getMoneyNum() {
				this.$Request.get("/app/userMoney/selectMyMoney").then(res => {
					if (res.code == 0) {
						this.myMoney = res.data.money
					}
				});
			},
			payment() {
				let that = this
				that.showpay = false
				if(!that.thisSelect.money) {
					uni.showToast({
						title: '请选择充值金额！',
						icon: 'none'
					})
					return
				}
				if (that.openWay == 2) { //微信支付
					// #ifdef MP-WEIXIN
					that.$Request.post('/app/wxPay/wxPayJsApiMoney', {
						money: that.thisSelect.money,
						type: 3,
					}).then(ret => {
						uni.hideLoading()
						uni.requestPayment({
							provider: 'wxpay',
							timeStamp: ret.data.timestamp,
							nonceStr: ret.data.noncestr,
							package: ret.data.package,
							signType: ret.data.signType,
							paySign: ret.data.sign,
							success: function(suc) {
								uni.showToast({
									title: '支付成功',
									icon: 'success'
								})
								
								that.getMoneyNum()
							},
							fail: function(err) {
								console.log('fail:' + JSON.stringify(err));
								uni.showToast({
									title: '支付失败',
									icon: 'none'
								})
							}
						});
					});
					// #endif
					// #ifdef APP
					let data = {
						type: 1,
						money: that.thisSelect.money
					}
					that.$Request.post('/app/wxPay/wxPayJsApiMoney', data).then(res => {
						console.log(res)
						that.showpay = false
						if (res.code == 0) {
							that.isCheckPay(res.code, 'wxpay', JSON.stringify(res.data));
						}
					});
					// #endif
					// #ifdef H5
					let data = {
						money: that.thisSelect.money,
						type: 2
					}
					this.$Request.post('/app/wxPay/wxPayJsApiMoney', data).then(res => {
						this.showpay = false
						that.callPay(res);
					});
					// #endif
				} else if(that.openWay == 3) {
					// #ifdef APP-PLUS
					let data = {
						type: 4,
						money: that.thisSelect.money
					}
					that.$Request.post('/app/wxPay/wxPayJsApiMoney', data).then(res => {
						that.showpay = false
						that.setPayment('alipay', res.data);
					});
					// #endif
					
					// #ifdef H5
					let data = {
						type: 5,
						money: that.thisSelect.money
					}
					this.$Request.post('/app/wxPay/wxPayJsApiMoney', data).then(res => {
						this.showpay = false
						const div = document.createElement('div')
						div.innerHTML = res.data //此处form就是后台返回接收到的数据
						document.body.appendChild(div)
						document.forms[0].submit()
					});
					// #endif
				}
			},
			callPay: function(response) {
				
				if (typeof WeixinJSBridge === "undefined") {
					console.log(document.addEventListener,'999')
					if (document.addEventListener) {
						document.addEventListener('WeixinJSBridgeReady', this.onBridgeReady(response), false);
					} else if (document.attachEvent) {
						document.attachEvent('WeixinJSBridgeReady', this.onBridgeReady(response));
						document.attachEvent('onWeixinJSBridgeReady', this.onBridgeReady(response));
					}
				} else {
					console.log(888)
					this.onBridgeReady(response);
				}
			},
			onBridgeReady: function(response) {
				console.log(response)
				let that = this;
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
							that.getMoneyNum()
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
						that.getMoneyNum()
						// setTimeout(function() {
						// 	uni.navigateBack()
						// }, 1000)
					},
					fail: function(err) {
						uni.hideLoading();
					},
					complete() {
						uni.hideLoading();
					}
				});
			}
		}
	}
</script>

<style scoped>
	/* 余额 */
	.wallet {
		width: 100%;
		height: 260rpx;
		background: -webkit-linear-gradient(top, #FCD202, #F5F5F5);
	}

	.wallet_header {
		width: 94%;
		margin: 0 auto;
		background-color: #FFFFFF;
		border-radius: 18rpx;
		position: relative;
		top: 100rpx;
	}

	.wallet_header_text {
		padding: 3% 3% 1%;
		line-height: 32rpx;
		font-size: 30rpx;
		font-weight: 500;
		color: #333333;
		line-height: 32px;
	}

	.wallet_header_xin {
		padding: 2% 3% 5%;
		display: flex;
	}

	.wallet_header_le {
		flex: 2;
		font-size: 68rpx;
		font-weight: 500;
		color: #333333;
	}

	.wallet_header_le text {
		font-size: 34rpx;
	}

	.wallet_header_ri {
		margin-top: 20rpx;
	}

	.wallet_header_ri image {
		width: 40rpx;
		height: 30rpx;
	}

	.wallet_header_ri text {
		font-size: 34rpx;
		font-weight: 500;
		color: #333333;
	}

	/* 充值 */
	.wallet_topup {
		width: 94%;
		margin: 13% auto 0;
		background-color: #FFFFFF;
		border-radius: 18rpx;
		padding: 0 0 3%;
	}

	.wallet_header_xin2 {
		/* padding: 0 3% 2%; */
		display: flex;
		flex-wrap: wrap;
		justify-content: space-between;
		padding: 0 2%;
	}

	.wallet_header_xin2_sty {
		width: 48.5%;
		/* margin: 1% 0 0 3%; */
		border: 2rpx solid #E6E6E6;
		border-radius: 18rpx;
		text-align: center;
		line-height: 3;
		margin-bottom: 2%;
	}

	.wallet_header_xin2_sty text {
		font-size: 44rpx;
	}

	.wallet_header_xin2_sty:first-child {
		margin-left: 0;
	}

	.topup {
		color: #FF130A;
		border: 2rpx solid #FF130A;
		position: relative;
	}

	.topup_img {
		position: absolute;
		bottom: 61rpx;
	}

	.topup_img image {
		width: 48rpx;
		height: 40rpx;
	}

	/* 消费 */
	.wallet_header_consum {
		width: 94%;
		margin: 3% auto;
		background-color: #FFFFFF;
		border-radius: 18rpx;
		padding: 3%;
		display: flex;
	}

	.wallet_header_consum_le {
		width: 97%;
		font-family: PingFang SC;
		font-weight: 500;
		color: #333333;
	}

	.wallet_header_consum_ri {
		line-height: 1.5;
	}

	.wallet_header_consum_ri image {
		width: 16rpx;
		height: 28rpx;
	}

	/* 立即充值 */
	.wallet_bottom {
		width: 94%;
		position: fixed;
		bottom: 3%;
		left: 3%;
		line-height: 2.5;
		background: #FCD202;
		text-align: center;
		border-radius: 49rpx;
		font-size: 38rpx;
		font-weight: bold;
		color: #333333;
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
