<template>
	<view class="">
		<view class="margin-tb-sm bg-white padding">
			<view class="flex justify-between align-center">
				<view class="flex align-center">
					<image src="../../../static/tabbar/index.png" style="width: 40rpx;height: 37rpx;" mode=""></image>
					<view class="text-xl text-bold text-black margin-lr-xs">{{goodsShop.shopName}}</view>
					<!-- <image src="../../../static/images/index/right.png" style="width: 14rpx;height: 24rpx;" mode=""></image> -->
				</view>
				<view class="flex Switch ">
					<view @click="tabSwidth(1)" :class="orderType==1?'selSwitch':''">自取</view>
					<view @click="tabSwidth(2)" :class="orderType==2?'selSwitch':''">外卖</view>
				</view>
			</view>
			<view class="text-gray">距离您16km</view>
			<view class="flex justify-between margin-top">
				<view class="btn1" @click="cancel()">取消订单</view>
				<!-- <view class="btn2">邀请好友</view> -->
				<button class="btn" open-type="share">邀请好友</button>
			</view>
		</view>
		<view v-if="myGoodList.length" class="margin-tb-sm bg-white padding">
			<view class="flex justify-between align-center padding-bottom">
				<image :src="myGoodList[0].avatar" mode="" style="width: 48rpx;height: 48rpx;border-radius: 48rpx;">
				</image>
				<view class="flex align-center flex-sub margin-left-sm">
					<view class="text-black text-lg text-bold">{{myGoodList[0].nickName}}</view>
					<!-- <view class="margin-left-sm">我自己</view> -->
				</view>
				<view class="flex">
					<view class="sBtn1" @click="clear()">清空</view>
					<view class="sBtn2" @click="goGoodsList">修改订单</view>
				</view>
			</view>
			<u-line color="#E6E6E6" />
			<view class="padding-top" v-for="(item,index) in myGoodList" :key='index'>
				<view class="flex justify-between">
					<view class="text-black text-lg text-bold">{{item.goodsName}}</view>
					<view class="text-lg text-gray"><text class="text-sm">x</text>{{item.goodsNum}}</view>
				</view>
				<view v-if="index==(myGoodList.length-1)" class="flex justify-between margin-top-sm ">
					<view class="text-gray">总计</view>
					<view class="text-lg text-black text-bold"><text class="text-sm">¥</text>{{item.goodsPrice}}</view>
				</view>
			</view>
		</view>
		<view v-if="otherGoodList.length>0" v-for="(orders,index) in otherGoodList" :key='index'>
			<view v-if="(index!=0 && orders.userId!=otherGoodList[index-1].userId) || index==0"
				class="margin-tb-sm bg-white padding">
				<view class="flex justify-between align-center padding-bottom">
					<image :src="orders.avatar" mode=""
						style="width: 48rpx;height: 48rpx;border-radius: 48rpx;"></image>
					<view class="flex align-center flex-sub margin-left-sm">
						<view class="text-black text-lg text-bold">{{orders.nickName}}</view>
						<!-- <view class="margin-left-sm">我自己</view> -->
					</view>
					<view class="flex">
						<!-- <view class="sBtn1">清空</view> -->
						<view class="sBtn3" @click="goGoodsList">和他点一样</view>
					</view>
				</view>
				<u-line color="#E6E6E6" />
				<view  v-for="(item,index) in otherGoodList" :key='index'>
					<view class="padding-top" v-if="item.userId==orders.userId">
						<view class="flex justify-between">
							<view class="text-black text-lg text-bold">{{item.goodsName}}</view>
							<view class="text-lg text-gray"><text class="text-sm">x</text>{{item.goodsNum}}</view>
						</view>
						<view v-if="item.userId!=otherGoodList[index+1].userId"
							class="flex justify-between margin-top-sm ">
							<view class="text-gray">总计</view>
							<view class="text-lg text-black text-bold"><text class="text-sm">¥</text>{{item.goodsPrice}}
							</view>
						</view>
					</view>

				</view>
			</view>
		</view>
		
		<empty v-if="!myGoodList.length && !otherGoodList.length" content='暂无商品' ></empty>
		
		<view style="height: 100rpx;"></view>
		<!-- 结算 -->
		<view class="settlement" @click="isPopupShow">
			<view class="settlement_img">
				<image src="../../../static/images/index/diancan.png" mode=""></image>
				<view class="settlement_hot">{{number}}</view>
			</view>
			<view class="settlement_le">
				<text>￥</text>{{totalPrice}}
			</view>
			<view class="settlement_ri" @click.stop="goConfirm()">去结算</view>
		</view>
	</view>
</template>

<script>
	import empty from '@/components/empty.vue'
	export default {
		components: {
			empty
		},
		data() {
			return {
				orderType: 2,
				shopId: '',
				goodsNum: 0,
				totalPrice: 0,
				orderId: '',
				otherGoodList: [],
				myGoodList: [],
				userId: '',
				goodsShop: {},
				number: 0,
			}
		},
		onLoad(e) {
			this.shopId = e.shopId
			this.orderId = e.orderId
			this.userId = uni.getStorageSync('userId')
			this.getGoodList()
			
		},
		onShareAppMessage(res) { //发送给朋友
			return {
				title: this.tuiguang,
				path: '/pages/index/index?shopId='+this.shopId+'&orderId='+this.orderId,
				imageUrl: this.tuiguangImg,
			}
		},
		methods: {
			clear() {
				let that = this
				uni.showModal({
					title: '提示',
				 content: '确定清空我的订单吗',
				 success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定');
							let data = {
								shopId: that.shopId,
							}
							that.$Request.post("/app/order/deleteShareTheBill", data).then(res => {
								if (res.code == 0) {
									this.getGoodList()
								}
							});
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			// 取消订单
			cancel(e) {
				let that = this
				console.log(e)
				uni.showModal({
					title: '提示',
					content: '确认取消订单吗?',
					success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定');
							let data = {
								orderId: that.orderId
							}
							that.$Request.post("/app/order/deleteOrder", data).then(res => {
								if (res.code == 0) {
									uni.showToast({
										title: '订单取消成功',
										icon: 'none'
									})
									uni.removeStorageSync('orderId')
									setTimeout(function() {
										uni.navigateBack()
									},1000)
								} else {
									uni.showToast({
										title: res.msg,
										icon: 'none'
									})
								}
							});
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			// 切换外卖/自提
			tabSwidth(e) {
				this.orderType = e
				this.getOrderList()
			},
			goGoodsList() {
				uni.navigateTo({
					url: '/pages/index/shop/goodsList?shopId=' + this.shopId
				})
			},
			getGoodList() {
				let data = {
					orderId: this.orderId,
				}

				this.$Request.get("/app/order/selectShareTheBill", data).then(res => {
					if (res.code == 0) {
						this.goodsShop = res.data.goodsShop
						
						this.myGoodList = res.data.parentShareTheBill
						let mySum = 0
						for (var i = 0; i < this.myGoodList.length; i++) {
							if (i == 0) {
								mySum = this.myGoodList[i].goodsPrice * this.myGoodList[i].goodsNum
								this.number += this.myGoodList[i].goodsNum
							} else {
								mySum = mySum + this.myGoodList[i].goodsPrice * this.myGoodList[i].goodsNum
								this.number += this.myGoodList[i].goodsNum
							}
							if (i == (this.myGoodList.length - 1)) {
								this.myGoodList[i].goodsPrice = mySum
								this.totalPrice = mySum
							}
						}


						this.otherGoodList = res.data.orderGoodsList
						var sum = 0
						for (var i = 0; i < this.otherGoodList.length; i++) {
							if (i == 0) {
								sum = this.otherGoodList[i].goodsPrice * this.otherGoodList[i].goodsNum
								this.number += this.otherGoodList[i].goodsNum
							} else {
								this.number += this.otherGoodList[i].goodsNum
								if (this.otherGoodList[i].userId == this.otherGoodList[i - 1].userId) {
									sum = sum + this.otherGoodList[i].goodsPrice * this.otherGoodList[i].goodsNum
								} else {
									this.otherGoodList[i - 1].goodsPrice = sum
									sum = this.otherGoodList[i].goodsPrice * this.otherGoodList[i].goodsNum
								}
							}
							if (i == (this.otherGoodList.length - 1)) {
								this.otherGoodList[i].goodsPrice = sum
								this.totalPrice += sum
							}
						}
						
					} else {
						uni.removeStorageSync('orderId')
						this.$queue.showToast(res.msg);
						setTimeout(function() {
							uni.navigateBack()
						},1000)
					}
				});
			},
			// 去结算
			goConfirm() {
				if (!this.userId) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
					return
				}
				console.log(this.goodsList)
				if (this.myGoodList.length > 0 || this.otherGoodList.length > 0) {
					uni.navigateTo({
						url: '/pages/index/shop/confirmOrder?shopId=' + this.shopId + '&orderType=2'
					})
				} else {
					uni.showToast({
						title: '请先添加商品',
						icon: "none"
					})
				}
			
			},
		}
	}
</script>

<style>
	.Switch {
		width: 164rpx;
		height: 62rpx;
		color: #FFFFFF;
		background: #FCD202;
		border-radius: 30rpx;
		display: flex;
		align-items: center;
		padding: 2rpx;
	}

	.Switch>view {
		/* flex: 1; */
		width: 80rpx;
		text-align: center;
		line-height: 62rpx;
		height: 58rpx;
	}

	.selSwitch {
		color: #333333;
		background: #FFFFFF;
		border-radius: 30rpx;
	}
	.btn {
		width: 320rpx;
		height: 88rpx;
		color: #333333;
		font-weight: 500;
		text-align: center;
		line-height: 88rpx;
		border-radius: 44rpx;
		background: #FCD202;
		border: 2rpx solid #FCD202;
		margin: 0;
	}
	button::after {
		border: 2rpx solid #FCD202;
	}
	
	.btn1 {
		width: 320rpx;
		height: 88rpx;
		color: #999999;
		font-weight: 500;
		font-size: 42rpx;
		text-align: center;
		line-height: 88rpx;
		border-radius: 44rpx;
		border: 2rpx solid #CCCCCC;
	}

	.btn2 {
		width: 320rpx;
		height: 88rpx;
		color: #333333;
		font-weight: 500;
		text-align: center;
		line-height: 88rpx;
		border-radius: 44rpx;
		background: #FCD202;
	}

	/* 结算 */
	.settlement {
		width: 94%;
		background-color: #000000;
		line-height: 3.4;
		border-radius: 49rpx;
		position: fixed;
		bottom: 10rpx;
		left: 3%;
		display: flex;
		justify-content: space-between;
	}

	.settlement_le {
		width: 45%;
		padding-left: 20%;
		color: #FFFFFF;
		font-size: 30rpx;
	}

	.settlement_le text {
		font-size: 22rpx;
	}

	.settlement_ri {
		width: 35%;
		background-color: #FCD202;
		font-family: PingFang SC;
		font-weight: 800;
		color: #333333;
		text-align: center;
		border-top-right-radius: 49rpx;
		border-bottom-right-radius: 49rpx;
	}

	.settlement_img {
		width: 91rpx;
		height: 96rpx;
		position: absolute;
		// bottom: 30rpx;
		left: 5%;
	}

	.settlement_img image {
		width: 74rpx;
		height: 81rpx;
	}

	.settlement_hot {
		width: 35rpx;
		height: 35rpx;
		line-height: 35rpx;
		text-align: center;
		border-radius: 50%;
		position: absolute;
		top: -10rpx;
		right: 0;
		background-color: #FF130A;
		color: #FFFFFF;
		font-size: 20rpx;
		font-weight: bold;
		color: #FFFFFF;
	}

	.sBtn1 {
		width: 128rpx;
		height: 48rpx;
		color: #999999;
		text-align: center;
		line-height: 48rpx;
		border: 2rpx solid #CCCCCC;
		border-radius: 24rpx;
	}

	.sBtn2 {
		width: 128rpx;
		height: 48rpx;
		color: #FF130A;
		text-align: center;
		line-height: 48rpx;
		border: 2rpx solid #FF130A;
		border-radius: 24rpx;
		margin-left: 20rpx;
	}

	.sBtn3 {
		width: 170rpx;
		height: 48rpx;
		color: #999999;
		text-align: center;
		line-height: 48rpx;
		border: 2rpx solid #CCCCCC;
		border-radius: 24rpx;
	}
</style>
