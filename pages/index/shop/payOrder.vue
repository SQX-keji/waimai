<template>
	<view class="pages">
		<!-- 地址 -->
		<view class="text-center text-red" v-if="isTrue && orderType==2">超出配送距离</view>
		<view class="food">
			<view class="flex bg justify-between padding-lr-xl">
				<view @click="switchTab(1)" :class="orderType==1? 'select':''" class="tabBtn">
					<view class="title">到店自取</view>
					<view :class="orderType==1? 'active':''"></view>
				</view>
				<view @click="switchTab(2)" :class="orderType==2? 'select':''" class="tabBtn" v-if="XCXIsSelect=='是'">
					<view class="title">外卖配送</view>
					<view :class="orderType==2? 'active':''"></view>
				</view>
			</view>
		
			<view v-if="orderType==1">
				<view class="flex margin-top">
					<u-icon name="map" size="50"></u-icon>
					<!-- <text>商家信息</text> -->
					<view class="flex-sub padding-tb-sm margin-left-sm">
						<view class="text-lg text-bold text-black">
							<text>{{dataList.shopName}}</text>
							
							<text class="margin-left-sm" @click="call"><u-icon name="phone" size="40"></u-icon></text>
						</view>
						<view class="text-df margin-top-xs" style="color: #999999;" @click="goMap">
							{{dataList.detailedAddress}}
						</view>
					</view>
				</view>
			</view>
			<view class="" v-if="orderType==2">
				<view class="goods_address" v-if="JSON.stringify(address) == '{}'" @click="goAddress">
					<view class="address_box">
						<view class="address_name">请选择收货地址</view>
						<view class="address_image margin-left">
							<image src="../../../static/images/index/right.png"></image>
						</view>
					</view>
				</view>
				<view class="flex margin-top" style="" v-else @click="goAddress">
					<u-icon name="map" size="50"></u-icon>
					<view class="flex-sub padding-tb-sm margin-left-sm">
						<view class="text-lg text-bold text-black">
							<text>{{address.userName}}</text>
							<text class="margin-left-sm">{{address.userPhone}}</text>
						</view>
						<view class="text-df margin-top-xs" style="color: #999999;">
							{{address.province}}{{address.city}}{{address.district}}{{address.addressDetail}}
						</view>
					</view>
					<view class="address_image margin-left">
						<image src="../../../static/images/index/right.png"></image>
					</view>
				</view>
			</view>
		</view>

		<!-- 商品 -->
		<view class="food">
			<view class="tosend_header_food" v-for="(item,index) in dataList.orderGoodsList" :key='index'>
				<view class="tosend_header_food_le">
					<image :src="item.goodsPicture[0]" style="border-radius: 10rpx;" mode=""></image>
				</view>
				<view class="flex-sub margin-left-sm">
					<view class="flex justify-between align-center">
						<view class="text-lg text-bold text-black">{{item.goodsName}}</view>
						<view class="text-lg text-bold text-black"><text class="text-sm">¥</text>{{item.goodsPrice}}
						</view>
					</view>
					<view class="flex justify-between align-center text-gray">
						<view>{{item.skuMessage}}</view>
						<view>x{{item.goodsNum}}</view>
					</view>
					<view class="flex justify-between align-center text-gray margin-tb-sm" v-if="item.goodsPack">
						<view>打包费</view>
						<view class="">￥{{item.goodsPack}} / 份</view>
					</view>
				</view>
			</view>
			<!-- <view class="tosend_header_do do_top" v-if="item.goodsPack>0" >
				<view>打包费</view>
				<view class="tosend_header_do_ri">￥{{item.goodsPack}}</view>
			</view> -->
			<view class="tosend_header_do justify-between" v-if="paotuiMoney>0&&orderType==2">
				<view>跑腿费</view>
				<view class="tosend_header_do_ri">￥{{paotuiMoney}}</view>
			</view>
			<view class="tosend_header_do justify-between do_bot" @click="getCouponList">
				<view>优惠券</view>
				<view class="tosend_header_do_ri" v-if="coupon">- ￥{{coupon.money}}</view>
				<view v-else>
					<image src="../../../static/images/order/right1.png" style="width: 14rpx;height: 24rpx;" mode="">
					</image>
				</view>
			</view>
			<view class="tosend_header_do justify-between">
				<view class="tosend_header_do_le2">合计</view>
				<view class="tosend_header_do_ri2">
					<text>￥</text>{{totalPrice1}}
				</view>
			</view>
		</view>
		<!-- 支付方式 -->
		<view class="margin-top padding-lr radius bg-white" style="width: 94%;margin: 0 auto;border-radius: 18rpx;">
			<view class="padding-tb-sm text-lg text-bold text-bold text-black">支付方式</view>

			<view class="flex align-center justify-between padding-tb" style="height: 100upx;"
				v-for="(item,index) in openLists" :key='index'>
				<image :src="item.image" style="width: 55rpx;height: 55rpx;border-radius: 50upx;"></image>
				<view class="flex-sub text-xl margin-left">{{item.text}}</view>
				<radio-group name="openWay" style="margin-left: 20upx;" @change='selectWay(item)'>
					<label class="tui-radio">
						<radio class="red" :checked="openWay === item.id ? true : false" />
					</label>
				</radio-group>
			</view>

		</view>
		<view style="height: 120rpx;"></view>
		<!-- 结算 -->
		<view class="goorder">
			<view class="goorder_but" :class="isTrue?'goorder_but_': ''" @click="toSettlement">立即结算</view>
		</view>

		<u-popup v-model="show" mode="center" :closeable="true" close-icon-pos="top-right" close-icon="close-circle"
			close-icon-size="50" border-radius="20" width="80%" @close="close">
			<view class="padding bg-gray">
				<view class="text-center text-lg text-bold margin-bottom-sm">可用优惠券</view>
				<view class="flex justify-between align-center radius margin-tb-sm padding-sm bg-white"
					v-for="(item,index) in couponList" :key='index' @click="selCoupon(item)">
					<view>
						<view>{{item.couponName}}</view>
						<view>有效期至{{item.expirationTime}}</view>
					</view>
					<view class="text-sm text-bold">¥<text class="text-lg">{{item.money}}</text></view>
				</view>
				<view v-if="couponList.length==0"
					style="height: 100rpx;line-height: 100rpx;text-align: center;font-weight: 700;">暂无可用优惠券</view>
			</view>
		</u-popup>
		<view class="hintPopul" v-if="shopDet&&shopDet.putawayFlag==1">
			<view class="content_">
				<image src="../../../static/images/index/shop.png" style="width: 200rpx;height: 180rpx;">
				</image>
				<view class="text-xl text-bold">店铺打烊啦</view>
				<view class="hintText margin-top-sm text-gray">现在店铺已经打烊了,营业时间</view>
				<view class="margin-top-xs text-gray margin-bottom">{{shopDet.businessHours}}-{{shopDet.lockHours}}</view>
				<view class="Btns" @click="goBack()">知道了</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				storeCommodityId: 0,
				openLists: [],
				openWay: 1,
				hintShow:true,
				page: 1,
				limit: 100,
				dataList: {},
				packMoney: 0, //打包费
				totalPrice: 0,
				totalPrice1: 0,
				address: {}, //地址
				addressId: '',
				parentId: '',
				orderId: '',
				show: false,
				coupon: '',
				shopDet:'',
				couponList: [],
				orderType: 1,//1表示到店自取 2表示外卖配送
				paotuiMoney: 0,
				dabaoMoney: 0,
				goodsMoney: 0,
				distance: 0,
				isTrue: false,
				XCXIsSelect: '是',
			}
		},
		onLoad(option) {
			// #ifdef APP
			this.openLists = [{
				image: '../../../static/images/my/jinbi.png',
				text: '零钱',
				id: 1
			}, {
				image: '../../../static/images/my/weixin.png',
				text: '微信',
				id: 2
			}, {
				image: '../../../static/images/my/zhifubao.png',
				text: '支付宝',
				id: 3
			}]
			// #endif

			// #ifdef MP-WEIXIN
			this.XCXIsSelect = this.$queue.getData('XCXIsSelect') ? this.$queue.getData('XCXIsSelect') : '是'
			this.openLists = [{
				image: '../../../static/images/my/jinbi.png',
				text: '零钱',
				id: 1
			}, {
				image: '../../../static/images/my/weixin.png',
				text: '微信',
				id: 2
			}]
			// #endif

			// #ifdef H5
			this.openLists = [{
				image: '../../../static/images/my/jinbi.png',
				text: '零钱',
				id: 1
			}, {
				image: '../../../static/images/my/zhifubao.png',
				text: '支付宝',
				id: 3
			}]
			// #endif
			this.orderId = option.orderId
			this.orderType = option.orderType

			uni.showLoading({
				title: '加载中...'
			})
			this.getOrderList()
			this.getAddressList()
			let that=this;
			//获取店铺信息
			uni.getLocation({
				type: 'gcj02', //返回可以用于uni.openLocation的经纬度
				success: function(res) {
					console.log(res,'经纬度')
				    that.lat = res.latitude;
				    that.lng = res.longitude;
					
				}
			});
		},
		onShow() {
			this.addressId = this.addressId ? this.addressId : uni.getStorageSync('addressId')
			if (this.addressId) {
				this.getAddressDet(this.addressId)
			}
		},
		methods: {
			//获取店铺详情
			getShopDet(shopId){
				let data = {
					shopId,
					lng: this.lng,
					lat: this.lat
				}
				this.$Request.get("/app/goods/selectGoodsList", data).then(res => {
					if (res.code == 0 && res.data) {
						
						this.shopDet = res.data.goodsShop
				
					}
					
				});
			},
			goBack(){
			  uni.navigateBack({
			  	
			  })	
			},
			getDistance() {
				let data = {
					ol: this.address.lng,
					od: this.address.lat,
					dl: this.dataList.shopLng,
					dd: this.dataList.shopLat
				}
				this.$Request.post("/app/tbindent/distance", data).then(res => {
					if (res.code == 0) {
						this.distance = res.data
						if (this.distance > Number(this.dataList.distributionDistance) && this.orderType == 2) {
							this.isTrue = true
						} else {
							this.isTrue = false
						}
					}
				});
			},
			close() {
				this.show = false
			},
			switchTab(e) {
				// this.page = 1
				// this.status = ''
				this.orderType = e
				// this.current = 0;
				console.log(e)
				
				if (JSON.stringify(this.address) != "{}") {
					this.getDistance()
				}
				// if (this.orderType == 2) {
				// 	this.totalPrice1 = this.totalPrice + this.paotuiMoney + this.dabaoMoney
				// } else {
				// 	this.totalPrice1 = this.totalPrice + this.dabaoMoney
				// }
				if (this.orderType == 2) {
					this.totalPrice1 = parseFloat(this.totalPrice + this.paotuiMoney + this.dabaoMoney).toFixed(2)
				} else {
					this.totalPrice1 = parseFloat(this.totalPrice + this.dabaoMoney).toFixed(2)
				}
				if(this.coupon){
					let totalMoney = parseFloat((this.totalPrice1 - (this.coupon.money ? this.coupon.money : 0))).toFixed(2)
					if (totalMoney <= 0) {
						this.totalPrice1 = 0.01
					} else {
						this.totalPrice1 = totalMoney
					}
				}
				
			},
			// 删除优惠券
			detCoupon() {
				let data = {
					orderId: this.dataList.orderId,
				}
				this.$Request.post("/app/order/deleteCouponByOrderId", data).then(res => {
					if (res.code == 0) {

					}
				});
			},
			// 选择优惠券
			selCoupon(item) {
				this.coupon = item
				if (this.orderType == 2) {
					this.totalPrice1 = parseFloat(this.totalPrice + this.paotuiMoney + this.dabaoMoney).toFixed(2)
				} else {
					this.totalPrice1 = parseFloat(this.totalPrice + this.dabaoMoney).toFixed(2)
				}
				let totalMoney = parseFloat((this.totalPrice1 - (this.coupon.money ? this.coupon.money : 0))).toFixed(2)
				if (totalMoney <= 0) {
					this.totalPrice1 = 0.01
				} else {
					this.totalPrice1 = totalMoney
				}

				let data = {
					parentId: this.dataList.parentId,
					couponId: this.coupon ? this.coupon.id : ''
				}
				this.$Request.post("/app/order/employCoupon", data).then(res => {
					if (res.code == 0) {
						this.show = false
					}else{
						this.$queue.showToast(res.msg)
					}
				});
			},
			// 获取优惠券列表
			getCouponList() {
				console.log(this.totalPrice)
				console.log(this.dataList.packMoney)
				let data = {
					page: 1,
					limit: 20,
					minMoney: this.orderType == 2 ? parseFloat(this.totalPrice * 1).toFixed(2) * 1 + this.paotuiMoney * 1 + this
						.dabaoMoney * 1 : parseFloat(this.totalPrice * 1).toFixed(2)
				}
				this.$Request.get("/app/coupon/selectUserCouponList", data).then(res => {
					if (res.code == 0) {
						this.couponList = res.data.list
					
					}
					this.show=true
				});
				
			},
			// 获取订单信息
			getOrderList() {
				let data = {
					orderId: this.orderId,
				}
				this.$Request.get("/app/order/selectBuyGoods", data).then(res => {
					uni.hideLoading()
					if (res.code == 0) {
						this.dataList = res.data
						this.getShopDet(res.data.shopId)
						// this.dataList.orderGoodsList[0].goodsPicture = this.dataList.orderGoodsList[0].goodsPicture.split(',')
						console.log(this.dataList.orderGoodsList)
						if (this.dataList) {
							this.dataList.orderGoodsList.forEach(res => {
								res.goodsPicture = res.goodsPicture.split(',')
								this.dabaoMoney += res.goodsPack * res.goodsNum
								this.goodsMoney += res.goodsPrice * res.goodsNum
							})
						}

						console.log(Number(this.dataList.orderGoodsList[0].goodsPrice))
						console.log(Number(this.dataList.exemptMinMoney))
						// 判断商品金额是否大于最低减免配送费金额
						if (Number(this.goodsMoney) >= Number(this.dataList.exemptMinMoney)) {
							console.log('跑腿费')
							this.paotuiMoney = 0
						} else {
							this.paotuiMoney = this.dataList.errandMoney
						}

						this.totalPrice = res.data.payMoney * 1
						console.log(this.dabaoMoney, '打包')
						console.log(this.paotuiMoney, '跑腿')
						if (this.orderType == 2) {
							this.totalPrice1 = this.totalPrice + this.paotuiMoney + this.dabaoMoney
						} else {
							this.totalPrice1 = this.totalPrice + this.dabaoMoney
						}

						console.log(this.dataList)
						
						this.detCoupon()
					}
				});
			},
			selectWay: function(item) {
				this.openWay = item.id;
			},
			// 立即结算
			toSettlement() {
				if (this.isTrue) {
					reutrn
				}
				if (this.orderType == 2 && !this.address.addressId) {
					uni.showToast({
						title: '请选择收货地址',
						icon: 'none'
					})
					return
				}
				uni.showLoading({
					title: '支付中...'
				})

				if (this.openWay == 1) {
					let data = {
						parentId: this.dataList.parentId,
						couponId: this.coupon ? this.coupon.id : '',
						addressId: this.address.addressId ? this.address.addressId : '',
						orderType: this.orderType
					}
					this.$Request.post("/app/wxPay/balanceOrder", data).then(res => {
						uni.hideLoading()
						if (res.code == 0) {
							uni.showToast({
								title: '支付成功',
								icon: 'success'
							})
							setTimeout(function() {
								uni.switchTab({
									url: '/pages/order/index'
								})
							}, 1000)
						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					});
				} else if (this.openWay == 2) {
					// #ifdef MP-WEIXIN
					let data = {
						parentId: this.dataList.parentId,
						couponId: this.coupon ? this.coupon.id : '',
						addressId: this.address.addressId ? this.address.addressId : '',
						orderType: this.orderType,
						type: 3
					}
					this.$Request.post("/app/wxPay/wxPayJsApiOrder", data).then(res => {
						uni.hideLoading()
						if (res.code == 0) {
							uni.requestPayment({
								provider: 'wxpay',
								timeStamp: res.data.timestamp,
								nonceStr: res.data.noncestr,
								package: res.data.package,
								signType: res.data.signType,
								paySign: res.data.sign,
								success: function(suc) {
									uni.showToast({
										title: '支付成功',
										icon: 'success'
									})
									setTimeout(function() {
										uni.switchTab({
											url: '/pages/order/index'
										})
									}, 1000)
								},
								fail: function(err) {
									console.log('fail:' + JSON.stringify(err));
									uni.showToast({
										title: '支付失败',
										icon: 'none'
									})
								}
							});
						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					});
					// #endif

					// #ifdef APP
					let data = {
						type: 1,
						parentId: this.dataList.parentId,
						couponId: this.coupon ? this.coupon.id : '',
						addressId: this.address.addressId ? this.address.addressId : '',
						orderType: this.orderType,
					}
					this.$Request.post('/app/wxPay/wxPayJsApiOrder', data).then(res => {
						console.log(res)
						this.showpay = false
						if (res.code == 0) {
							this.isCheckPay(res.code, 'wxpay', JSON.stringify(res.data));
						}
					});
					// #endif
				} else if (this.openWay == 3) {
					// #ifdef H5
					let data = {
						type: 5,
						parentId: this.dataList.parentId,
						couponId: this.coupon ? this.coupon.id : '',
						addressId: this.address.addressId ? this.address.addressId : '',
						orderType: this.orderType,
					}
					this.$Request.post('/app/wxPay/wxPayJsApiOrder', data).then(
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
						type: 4,
						parentId: this.dataList.parentId,
						couponId: this.coupon ? this.coupon.id : '',
						addressId: this.address.addressId ? this.address.addressId : '',
						orderType: this.orderType,
					}
					this.$Request.post('/app/wxPay/wxPayJsApiOrder', data).then(res => {
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
			},
			// 获取选择的地址
			getAddressDet(e) {
				this.$Request.get("/app/address/selectAddressById?addressId=" + e).then(res => {
					if (res.code == 0) {
						this.address = res.data

						if (JSON.stringify(this.address) != "{}") {
							this.getDistance()
						}
						uni.removeStorageSync('addressId')
					}
				});
			},
			// 获取地址列表
			getAddressList() {
				let that = this
				let data = {
					page: 1,
					limit: 100
				}
				that.$Request.get("/app/address/selectAddressList", data).then(res => {
					if (res.code == 0) {
						that.address = res.data.list[0] ? res.data.list[0] : {}
						res.data.list.forEach(ret => {
							if (ret.addressDefault == 1) {
								that.address = ret
							}
						})
						console.log(that.address, '我的地址列表460')
						if (JSON.stringify(this.address) != "{}") {
							this.getDistance()
						}
					}
				});
			},
			goAddress() {
				uni.navigateTo({
					url: '/my/address/index?add=1'
				})
			},
			// 点击调起地图查看位置
			goMap() {
				
				let that = this
				let lati = parseFloat(that.dataList.shopLat);
				let longi = parseFloat(that.dataList.shopLng);
				uni.authorize({
					scope: 'scope.userLocation',
					success(res) {
						uni.openLocation({
							name: that.dataList.shopName,
							latitude: lati,
							longitude: longi,
							success: function() {}
						});
					},
					fail(err) {

					}
				})
			},
			// 打电话
			call() {
				uni.makePhoneCall({
					phoneNumber: this.dataList.shopPhone
				});
			},
		}
	}
</script>

<style scoped>
	.hintPopul {
		width: 100%;
		height: 100vh;
		position: fixed;
		top: 0;
		background: rgba(0, 0, 0, .4);
		z-index: 999;
	}
	
	.content_ {
		position: absolute;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		margin: auto;
		text-align: center;
		width: 500rpx;
		height: 400rpx;
		border-radius: 20rpx;
		background-color: #fff;
		padding-top: 120rpx;
	}
	
	.content_ image {
		position: absolute;
		top: -50rpx;
		left: 0;
		right: 0;
		margin: auto;
	}
	
	.hintText {
		font-size: 30rpx;
	}
	
	.Btns {
		width: 460rpx;
		height: 60rpx;
		line-height: 60rpx;
		text-align: center;
		background: #FCD202;
		font-size: 28rpx;
		border: 2rpx solid #FCD202;
		color: #333333;
		border-radius: 50rpx;
		font-weight: 700;
		margin: auto;
	}
	
	
	.goods_address {
		width: 94%;
		background: #FFFFFF;
		height: 120rpx;
		margin: 20rpx auto 0;
		border-radius: 18rpx;
	}

	.address_box {
		width: 90%;
		margin: 0 auto;
		height: 120rpx;
		display: flex;
		line-height: 120rpx;
	}

	.address_name {
		flex: 1;
		font-size: 30rpx;
	}

	.address_image {
		/* flex: 1; */
		display: flex;
		justify-content: flex-end;
		align-items: center;
	}

	.address_image image {
		width: 25rpx;
		height: 40rpx;
	}


	/* 地址 */
	.address {
		width: 88%;
		margin: 3% auto;
		padding: 3%;
		display: flex;
		background-color: #FFFFFF;
		border-radius: 18rpx;
	}

	.adderss_le {
		width: 90%;
		line-height: 1.5;
	}

	.adderss_le_text {
		font-size: 30rpx;
		font-weight: bold;
		color: #333333;
	}

	.adderss_le_text2 {
		font-size: 24rpx;
		font-weight: 500;
		color: #999999;
		display: flex;
	}

	.adderss_le_text2 view {
		margin-left: 2%;
	}

	.adderss_le_text2 view:first-child {
		margin-left: 0;
	}

	.adderss_ri {
		width: 10%;
		text-align: right;
		line-height: 3;
	}

	.adderss_ri image {
		width: 18rpx;
		height: 30rpx;
	}

	/* 商品 */
	.food {
		width: 94%;
		margin: 3% auto;
		padding: 3%;
		background-color: #FFFFFF;
		border-radius: 18rpx;
	}

	.tosend_header_food {
		width: 100%;
		display: flex;
		margin-top: 3%;
	}

	.tosend_header_food_le {
		width: 15%;
	}

	.tosend_header_food_le image {
		width: 110rpx;
		height: 110rpx;
	}

	.tosend_header_food_ce {
		margin: 0 0 0 4%;
		width: 57%;
	}

	.tosend_header_food_ri {
		text-align: right;
		width: 25%;
	}

	.tosend_header_food_text {
		font-size: 34rpx;
		font-weight: 500;
		color: #333333;
		line-height: 1.8;
	}

	.tosend_header_food_text text {
		font-size: 25rpx;
	}

	.tosend_header_food_text2 {
		font-size: 30rpx;
		font-weight: 500;
		color: #999999;
	}

	.do_top {
		padding-top: 3%;
	}

	.do_bot {
		padding-bottom: 3%;
		border-bottom: 2rpx solid #E6E6E6;
	}

	.tosend_header_do {
		width: 100%;
		color: #333333;
		font-size: 30rpx;
		display: flex;
		margin-top: 20rpx;
		line-height: 1.5;
	}

	.tosend_header_do view {
		/* flex: 1; */
	}

	.tosend_header_do_ri {
		text-align: right;
	}

	.tosend_header_do_le2 {
		font-family: PingFang SC;
		font-weight: 500;
		color: #999999;
		padding-top: 1.5%;
	}

	.tosend_header_do_ri2 {
		text-align: right;
		color: #EA0000;
		font-size: 40rpx;
	}

	.tosend_header_do_ri2 text {
		font-size: 30rpx;
	}

	/* 支付 */
	/* .payment_all{
		width: 88%;margin: 3% auto;padding: 3%;background-color: #FFFFFF;
		border-radius: 18rpx;
	}
	.payment{
		width: 100%;display: flex;margin-top: 3%;
	}
	.payment_le{
		width: 13%;
	}
	.payment_le image{
		width: 60rpx;height: 60rpx;
	}
	.payment_ce{
		width: 52%;
		font-size: 30rpx;
		font-weight: bold;
		color: #333333;line-height: 60rpx;
	}
	.payment_ri{
		width: 35%;text-align: right;line-height: 60rpx;
	} */
	/* 结算 */
	.goorder {
		width: 100%;
		padding: 2% 3%;
		position: fixed;
		bottom: 0;
		background-color: #FFFFFF;
	}

	.goorder_but {
		width: 100%;
		line-height: 3.5;
		text-align: center;
		background: #FCD202;
		border-radius: 36rpx;
		font-weight: 700;
	}

	.goorder_but_ {
		opacity: 0.5;
	}

	.bg {
		background-color: #FFFFFF;
	}

	.tabBtn {
		/* background-color: #f6f6fa; */
		height: 60rpx;
		line-height: 60rpx;
		color: #999999;
		font-size: 38rpx;
	}

	.active {
		/* width: 82rpx; */
		height: 6rpx;
		background: #FCD202;
		/* position: relative;
		top: -20rpx; */
		z-index: 9;
	}
</style>
