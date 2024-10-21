<template>
	<view class="pages" style="position: relative;">
		<view style="position: fixed;top: 0;left: 0;right: 0;z-index: 99;width: 100%;" class="bg"  >
			<view class="flex bg justify-between padding-lr-xl">
				
				<view @click="switchTab(2)" :class="orderType==2? 'select':''" class="tabBtn" v-if="XCXIsSelect=='是'">
					<view class="title">外卖配送</view>
					<view :class="orderType==2? 'active':''"></view>
				</view>
				<view @click="switchTab(1)" :class="orderType==1? 'select':''" class="tabBtn" v-if="XCXIsSelect=='是'">
					<view class="title">到店取餐</view>
					<view :class="orderType==1? 'active':''"></view>
				</view>
				<!-- <view @click="switchTab(3)" :class="orderType==3? 'select':''" class="tabBtn" >
					<view class="title">跑腿订单</view>
					<view :class="orderType==3? 'active':''"></view>
				</view> -->
			</view>
			<view>
				<u-tabs v-if="orderType == 1" active-color="#FCD202" :list="qucanList" :is-scroll="true"
					:current="current" @change="change"></u-tabs>
				<u-tabs v-if="orderType == 2" active-color="#FCD202" :list="waimaiList" :is-scroll="true"
					:current="current1" @change="change1">
				</u-tabs>
				<u-tabs v-if="orderType == 3" :list="paotuiList" :is-scroll="true" active-color="#FCD202"
					:current="current2" @change="change2"></u-tabs>
			</view>
		</view>

		<!-- 全部订单 -->
		<view class="cont_one" v-if="orderType != 3">
			<view v-for="(item,index) in orderList" :key='index'
				@click="goNav('/pages/order/takefood?orderId='+item.orderId)">
				<view class="cont" v-if="item.orderType == 1">
					<view class="order_success">
						<view class="order_name" v-if="item.status == 6">制作中</view>
						<view class="order_name" v-if="item.status == 3">待取餐</view>
						<view class="order_name" v-if="item.status == 4">已完成</view>
						<view class="order_name" v-if="item.status == 5">已取消</view>
						<view class="order_name" v-if="item.status == 7">待接单</view>
						<view class="order_name" v-if="item.status == 8">已取消</view>
						<view class="order_data">{{item.payTime}}</view>
					</view>
					<u-line color="#E6E6E6" />
					<view class="cont_two_top">
						<view class="text-lg text-bold text-black flex align-center">
							<image :src="item.shopCover" style="width: 80rpx;height: 80rpx;border-radius: 80rpx;"
								mode=""></image>
							<view class="margin-left-xs">{{item.shopName}}</view>
						</view>

					</view>
					<view class="cont_two_text">{{item.orderCode}}</view>
					<view class="cont_two_text2" v-if="item.status == 6">前面还有<text>{{item.countOrder}}</text>个人排队</view>
					<!-- <view class="cont_two_bottom">
						<view class="cont_two_bottom_le">点餐详情</view>
						<view class="cont_two_bottom_ri">
							<image src="../../static/images/order/right1.png" mode=""></image>
						</view>
					</view> -->
					<u-line color="#E6E6E6" />
					<view class="flex justify-between align-center padding-lr-sm padding-tb-sm">
						<view class="text-sm text-gray">实收 <text
								class="text-lg text-bold text-black margin-left-xs">¥{{item.payMoney}}</text></view>
						<view class="flex padding-tb-sm">
							<view v-if="item.status == 7" class="btn" @click.stop="cancel(item)">取消订单</view>
						</view>
					</view>
				</view>
				<view class="cont" v-if="item.orderType == 2">
					<view class="order_success">
						<view class="order_name" v-if="item.status == 3">配送中</view>
						<view class="order_name" v-if="item.status == 4">已完成</view>
						<view class="order_name" v-if="item.status == 5">已取消</view>
						<view class="order_name" v-if="item.status == 6">制作中</view>
						<view class="order_name" v-if="item.status == 7">待接单</view>
						<view class="order_name" v-if="item.status == 8">已取消</view>
						<view class="order_data">{{item.payTime}}</view>
					</view>
					<u-line color="#E6E6E6" />
					<view class="flex justify-between padding align-center">
						<view class="text-lg text-bold text-black flex align-center">
							<image :src="item.shopCover" style="width: 80rpx;height: 80rpx;border-radius: 80rpx;"
								mode=""></image>
							<view class="margin-left-xs">{{item.shopName}}</view>
						</view>
					</view>
					<view class="">
						<view class=" padding-lr margin-tb-sm" v-for="(ite,ind) in item.orderGoodsList" :key='ind'>
							<view class="flex">
								<image :src="ite.goodsPicture[0]" mode=""
									style="width: 120rpx;height: 120rpx;border-radius: 10rpx;"></image>
								<view class="margin-left-sm flex flex-direction justify-between">
									<view class="text-black text-bold text-lg">{{ite.goodsName}}</view>
									<view class="text-gray text-sm">{{ite.skuMessage}}</view>
								</view>
							</view>
						</view>
					</view>
					<u-line color="#E6E6E6" />
					<view class="flex justify-between align-center padding-lr-sm">
						<view class="text-sm text-gray">实收 <text
								class="text-lg text-bold text-black margin-left-xs">¥{{item.payMoney}}</text></view>
						<view class="flex padding-tb-sm">
							<view v-if="item.status == 3" class="btn" @click.stop="finish(item)">确认收货</view>
							<view v-if="item.status == 6||item.status == 7" class="btn" @click.stop="cancel(item)">取消订单
							</view>
							<view v-if="item.status == 4 &&item.commentFlag!=1" class="btn" @click.stop="pingjia(item)">
								评价订单</view>
							<view class="btn_" @click.stop="goShop(item.shopId)">再来一单</view>
						</view>
					</view>
				</view>
			</view>
			<empty v-if="!orderList.length" style="z-index:0"></empty>
		</view>
		<view v-if="orderType == 3" style="margin-top: 160rpx;">
			<view>
				<view class="order_box" v-for="(item,index) in order" :key="index"
					@click="bindorderDetail(item.indentNumber)">
					<view class="order_success">
						<view class="order_name" v-if="item.indentState == 0">待付款</view>
						<view class="order_name"
							v-if="item.indentState == 1||item.indentState == 8||item.indentState == 9||item.indentState == 10">
							已取消</view>
						<view class="order_name" v-if="item.indentState == 2">待接单</view>
						<view class="order_name" v-if="item.indentState == 5">待确认</view>
						<view class="order_name" v-if="item.indentState == 3">已接单</view>
						<view class="order_name" v-if="item.indentState == 4">派送中</view>
						<view class="order_name" v-if="item.indentState ==6||item.indentState ==7">
							已完成</view>
						<view class="order_name" v-if="item.indentState == 10">
							已取消</view>
						<view class="order_data">{{item.createTime}}</view>
					</view>
					<u-line color="#E6E6E6" />
					<view class="order_city">
						<view class="city_type">
							<view class="type_name" v-if="item.indentType == 1">帮我送</view>
							<view class="type_name" v-if="item.indentType == 2">帮我取</view>
							<view class="type_name" v-if="item.indentType == 3">同城帮买</view>
							<view class="type_name" v-if="item.indentType == 4">同城服务</view>
							<view class="type_name" v-if="item.indentType == 5">同城外卖</view>
							<view class="city_text" v-if="item.indentType == 1||item.indentType == 2">
								{{item.itemType}}
							</view>
							<view class="city_text" v-if="item.indentType == 3&&item.buyType == 0">骑手购买</view>
							<view class="city_text" v-if="item.indentType == 3&&item.buyType == 1">指定购买</view>
							<view class="city_text" v-if="item.indentType == 4&&item.serviceType">{{item.serviceType}}
							</view>

						</view>
						<view class="city_address">
							<view class="fh_box"
								v-if="(item.indentType == 1||item.indentType == 2||item.indentType == 3)">
								<view class="fh_image">
									<image src="../../static/images/order/mai.png" v-if="item.indentType == 3"></image>
									<image src="../../static/images/order/icon_f.png" v-else></image>
								</view>
								<view class="box">
									<view class="fh_name">{{item.shopAddressDetail}}</view>
									<view class="fh_type" v-if="item.indentType != 3">{{item.shopName}}
										<text>{{item.shopPhone}}</text>
									</view>
								</view>
							</view>
							<view class="sh_box">
								<view class="sh_image">
									<image src="../../static/images/order/icon_s.png"></image>
								</view>
								<view class="box">
									<view class="sh_name">{{item.userAddressDetail}}</view>
									<view class="sh_type">{{item.userName}}
										<text>{{item.userPhone}}</text>
									</view>
								</view>
							</view>
						</view>
					</view>
					<u-line color="#E6E6E6" />
					<view class="order_btn">
						<!-- <view class="btn1" v-if="item.indentState == 7||item.indentState == 6 &&!item.evaluateMessage"
							@tap.stop="pingjia(item)">
							去评论
						</view> -->
						<view class="btn" v-if="item.indentState == 0||item.indentState == 2"
							@tap.stop="bindorderOff(item)">取消订单</view>
						<view class="btn" @tap.stop="bindconfirm(item)" v-if="item.indentState == 5">确认订单</view>
						<view class="btn_" @tap.stop="bindorder(item)" v-if="item.indentState == 1||item.indentState == 3||item.indentState ==8||
					item.indentState ==9||item.indentState ==10||item.indentState == 4||item.indentState == 6">再来一单</view>
						<view class="btn_" v-if="item.indentState == 0" @tap.stop="bindorderpay(item)">立即付款</view>
					</view>
				</view>
			</view>
			<empty v-if="!order.length" style="z-index:0"></empty>
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
				show: false,
				page: 1,
				limit: 10,
				orderType: 2,
				orderList: [],
				status: '',
				current: 0,
				current1: 0,
				current2: 0,
				qucanList: [{
						name: '全部'
					},
					{
						name: '待接单'
					},
					{
						name: '制作中'
					},
					{
						name: '待取餐'
					},
					{
						name: '已完成'
					},
					{
						name: '已取消'
					}
				],
				waimaiList: [{
						name: '全部'
					},
					{
						name: '待接单'
					},
					{
						name: '制作中'
					},
					{
						name: '配送中'
					},
					{
						name: '已完成'
					},
					{
						name: '已取消'
					}
				],
				paotuiList: [{
						name: '全部',
						id: 1
					},
					{
						name: '待付款',
						id: 2
					},
					{
						name: '待接单',
						id: 3
					},
					{
						name: '已接单',
						id: 4
					},
					{
						name: '派送中',
						id: 5
					},
					{
						name: '已完成',
						id: 6
					},
					{
						name: '已取消',
						id: 7
					}
				],
				userId: '',
				currentIndex: 1,
				order: [],
				waimaiCount: 0,
				paotuiCount: 0,
				XCXIsSelect: '是',

			};
		},
		onLoad(option) {
			this.XCXIsSelect = this.$queue.getData('XCXIsSelect') ? this.$queue.getData('XCXIsSelect') : '是'
		
			this.userId = uni.getStorageSync('userId')
			if (option.orderType) {
				this.orderType = option.orderType
			}
			uni.showLoading({
				title: '加载中...'
			})

		},
		onShow() {
			this.userId = uni.getStorageSync('userId')
			if (uni.getStorageSync('current') && this.userId) {
				this.current1 = uni.getStorageSync('current')
				this.status = uni.getStorageSync('current')
				this.page = 1
				this.orderType = 2

				uni.removeStorageSync('current')
				this.getOrderList()
			}
			if (this.userId) {
				if (this.orderType == 3) {
					this.orderList_()
				} else {
					this.getOrderList()
				}
			} else {
				this.order = []
				this.orderList = []
			}
			uni.hideLoading()
		},
		methods: {
			bindopen() {
				this.show = true
			},
			bindx() {
				this.show = false
			},
			// 取消跑腿订单
			bindorderOff(e) {
				console.log(e)
				let indentNumber = e.indentNumber
				console.log(indentNumber)
				uni.showModal({
					title: '温馨提示',
					// content: '取消订单平台将扣除您'+e.userFine+'元?',
					content: '确定取消订单？',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							this.$Request.postT('/app/tbindent/userCancleIndent?indentNumber=' + indentNumber)
								.then(res => {
									// console.log(res)
									if (res.code == 0) {
										uni.showToast({
											title: '订单取消成功'
										});
										this.page = 1;
										this.orderList_()
									} else {
										uni.hideLoading();
										uni.showModal({
											showCancel: false,
											title: '订单失败',
											content: res.msg
										});
									}
								});
						}
					}
				});
			},
			// 取消外卖订单
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
								orderId: e.orderId
							}
							that.$Request.get("/app/order/userCancelOrder", data).then(res => {
								if (res.code == 0) {
									uni.showToast({
										title: '订单取消成功',
										icon: 'none'
									})
									that.page = 1
									that.getOrderList()
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
			// 评价订单
			pingjia(e) {
				console.log(e)
				let ordersId = e.orderId
				let orderNumber = e.orderNumber
				let goodsId = []
				if (e.orderGoodsList) {
					e.orderGoodsList.forEach(res => {
						goodsId.push(res.goodsId)
					})
				}

				uni.navigateTo({
					url: '/pages/order/feedback?ordersId=' + ordersId + '&orderNumber=' + orderNumber +
						'&goodsId=' + goodsId.toString() + '&shopId=' + e.shopId
				})
			},
			goShop(e) {
				uni.navigateTo({
					url: '/pages/index/shop/index?shopId=' + e
				})
			},
			// 完成订单
			finish(e) {
				let that = this
				console.log(e)
				uni.showModal({
					title: '提示',
					content: '确认订单完成吗?',
					success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定');
							let data = {
								orderId: e.orderId
							}
							that.$Request.post("/app/order/accomplishOrder", data).then(res => {
								if (res.code == 0) {
									uni.showToast({
										title: '订单完成',
										icon: 'none'
									})
									that.getOrderList()
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
			switchTab(e) {
				this.page = 1
				this.status = ''
				this.orderType = e
				this.current = 0;
				this.current1 = 0;
				this.order = []
				this.orderList = []
				console.log(e)
				if (this.orderType == 3) {
					this.orderList_()
				} else {
					this.getOrderList()
				}
			},
			change(index) {
				this.page = 1
				this.current = index;
				switch (index) {
					case 0:
						this.status = '' //全部
						break;
					case 1:
						this.status = 7 //待接单
						break;
					case 2:
						this.status = 6 //制作中
						break;
					case 3:
						this.status = 3 //待取餐
						break;
					case 4:
						this.status = 4 //已完成
						break;
					case 5:
						this.status = 5 //已取消
						break;
				}
				this.getOrderList()
			},
			change1(index) {
				this.page = 1
				this.current1 = index;
				switch (index) {
					case 0:
						this.status = '' //全部
						break;
					case 1:
						this.status = 7 //待接单
						break;
					case 2:
						this.status = 6 //制作中
						break;
					case 3:
						this.status = 3 //配送中
						break;
					case 4:
						this.status = 4 //已完成
						break;
					case 5:
						this.status = 5 //已取消
						break;
				}
				this.getOrderList()
			},
			change2(index) {
				this.current2 = index;
				this.currentIndex = this.paotuiList[index].id
				this.page = 1;
				this.orderList_()
			},
			goNav(url) {
				uni.navigateTo({
					url
				})
			},
			// 跑腿订单获取
			orderList_() {
				let data = {
					page: this.page,
					limit: this.limit,
					indentState: this.currentIndex
				}
				this.$Request.getT('/app/tbindent/findUserIndent', data).then(res => {
					// console.log(res)
					var that = this
					if (res.code === 0) {
						this.paotuiCount = res.data.totalCount
						if (that.page == 1) {
							that.order = res.data.list
							that.totalPage = res.data.totalPage
						}
						if (that.page > 1) {
							if (res.data.list.length > 0) {
								that.order = that.order.concat(res.data.list)
							}
						}
					}
					uni.stopPullDownRefresh();
					uni.hideLoading()
				});
			},
			// 外卖数据列表
			getOrderList() {
				let data = {
					page: this.page,
					limit: this.limit,
					status: this.status,
					orderType: this.orderType
				}
				this.$Request.get("/app/order/waitTakeFood", data).then(res => {
					if (res.code == 0) {
						this.waimaiCount = res.data.totalCount
						res.data.list.forEach(res => {
							res.orderGoodsList.forEach(ret => {
								ret.goodsPicture = ret.goodsPicture.split(',')
							})
							res.orderCode = res.orderCode.substring(res.orderCode.length - 3, res.orderCode
								.length)
						})
						if (this.page == 1) {
							this.orderList = res.data.list
						} else {
							this.orderList = [...this.orderList, ...res.data.list]
						}
					}
					uni.stopPullDownRefresh();
					uni.hideLoading()
				});
			},
			// 再来一单
			bindorder(e) {
				console.log(e)
				let index = e.indentType
				let current = e.buyType
				if (e.indentType == 1 || e.indentType == 2) {
					uni.navigateTo({
						url: '/running/Helpsend/Helpsend?indentNumber=' + e.indentNumber + '&index=' + index
					})
				} else if (e.indentType == 3) {
					uni.navigateTo({
						url: '/running/Helppay/Helppay?indentNumber=' + e.indentNumber + '&index=' + index +
							'&current=' + current
					})
				} else {
					uni.navigateTo({
						url: '/running/Cityservice/Cityservice?indentNumber=' + e.indentNumber + '&index=' + index
					})
				}
			},
			// 订单详情
			bindorderDetail(indentNumber) {
				console.log(indentNumber)
				uni.navigateTo({
					url: '/pages/order/detail?indentNumber=' + indentNumber
				})
			},
			// 立即付款
			bindorderpay(e) {
				uni.navigateTo({
					url: '/running/order/pay/pay?indentNumber=' + e.indentNumber
				})
			}
		},
		onReachBottom: function() {

			if (this.orderType == 3) {
				if (this.order.length < this.paotuiCount) {
					this.page = this.page + 1;
					this.orderList_()
				} else {
					uni.showToast({
						title: '已经到底了',
						icon: 'none'
					})
				}
			} else {
				if (this.orderList.length < this.waimaiCount) {
					this.page = this.page + 1;
					this.getOrderList()
				} else {
					uni.showToast({
						title: '已经到底了',
						icon: 'none'
					})
				}
			}
		},
		onPullDownRefresh: function() {
			this.page = 1
			this.status = ''

			this.current = 0;
			this.current1 = 0;
			// console.log(e)
			if (this.orderType == 3) {
				this.orderList_()
			} else {
				this.getOrderList()
			}

		},
	}
</script>

<style scoped>
	.active {
		/* width: 82rpx; */
		height: 16rpx;
		background: #FCD202;
		position: relative;
		top: -20rpx;
		z-index: 9;
	}

	.bg {
		background-color: #FFFFFF;
	}

	/* 切换选项 */
	.nav {
		display: flex;
		align-items: center;
		justify-content: center;
		background-color: #FFFFFF;
		color: #999999;
	}

	.btn {
		width: 150rpx;
		height: 60rpx;
		line-height: 60rpx;
		text-align: center;
		background: #F5F5F5;
		font-size: 28rpx;
		border: 2rpx solid ##F5F5F5;
		color: #666666;
		border-radius: 50rpx;
		font-weight: 700;
		margin-left: 10rpx;
	}

	.btn_ {
		width: 150rpx;
		height: 60rpx;
		line-height: 60rpx;
		text-align: center;
		background: #FCD202;
		font-size: 28rpx;
		border: 2rpx solid #FCD202;
		color: #333333;
		border-radius: 50rpx;
		font-weight: 700;
		margin-left: 10rpx;
	}

	.tabBtn {
		/* background-color: #f6f6fa; */
		height: 60rpx;
		line-height: 60rpx;
		color: #999999;
		font-size: 38rpx;
	}

	.title {
		position: relative;
		z-index: 100;
	}

	.shaix {
		width: 30%;
		text-align: center;
		display: flex;
		align-items: center;
		justify-content: center;
		letter-spacing: 4rpx;
		font-size: 27rpx;
	}

	.select {
		color: #000000;
		font-weight: bold;
		background-color: #fff;
		z-index: 10;
	}

	.nav view {
		flex-grow: 1;
		margin: 3% 6.5% 2%;
		text-align: center;
	}

	.nav_btna {
		font-size: 42rpx;
		line-height: 34rpx;
		color: #000000;
		font-weight: bold;
		border-bottom: 14rpx solid #FCD202;
	}

	/* 内容 */
	/* 全部订单 */
	.cont_one {
		margin-top: 160rpx;
	}

	/* .cont_one image {
		width: 80%;
		height: 353rpx;
	} */

	/* 到店取餐 */
	.cont_two {
		display: none;
		width: 94%;
		margin: 3% auto;
		background-color: #FFFFFF;
		border-radius: 18rpx;
	}

	.cont {
		/* display: none; */
		width: 94%;
		margin: 3% auto;
		background-color: #FFFFFF;
		border-radius: 18rpx;
		/* padding: 20rpx 0; */
	}

	.cont_two_top {
		width: 94%;
		padding: 4% 3% 0;
		margin: 0 auto;
		display: flex;
		justify-content: space-between;
	}

	.cont_two_top_le {
		/* width: 85%; */
		font-size: 30rpx;
		font-weight: 500;
		color: #333333;
		/* line-height: 2; */
		margin: 5rpx 0;
	}

	.cont_two_top_ri {
		/* width: 15%; */
		padding: 6rpx 10rpx;
		text-align: center;
		background: rgba(255, 19, 10, 0.2);
		font-size: 24rpx;
		border: 2rpx solid #FF130A;
		color: #FF130A;
		opacity: 0.6;
		border-radius: 8rpx;
	}

	.cont_two_top_ri1 {
		/* width: 15%; */
		text-align: center;
		/* line-height: 2; */
		padding: 6rpx 10rpx;
		background: #62ba8b;
		font-size: 24rpx;
		border: 2rpx solid #62ba8b;
		color: #fff;
		/* opacity: 0.6; */
		border-radius: 8rpx;
	}

	.cont_two_top_ri2 {
		/* width: 15%; */
		text-align: center;
		/* line-height: 2; */
		padding: 6rpx 10rpx;
		background: #FCD202;
		font-size: 24rpx;
		border: 2rpx solid #FCD202;
		color: #fff;
		/* opacity: 0.6; */
		border-radius: 8rpx;
	}

	.cont_two_text {
		font-size: 58rpx;
		text-align: center;
		font-weight: bold;
		color: red;
		margin: 3% 0;
		line-height: 32rpx;
	}

	.cont_two_text2 {
		font-size: 30rpx;
		width: 100%;
		font-weight: 500;
		color: #333333;
		text-align: center;
		padding-bottom: 3%;
		/* line-height: 32rpx; */

	}

	.cont_two_text2 text {
		color: #FF130A;
	}

	.cont_two_bottom {
		width: 94%;
		padding: 3%;
		margin: 0 auto;
		display: flex;
		border-top: 1rpx solid #E6E6E6;
	}

	.cont_two_bottom_le {
		flex: 1;
		font-size: 24rpx;
		font-weight: 500;
		color: #999999;
		line-height: 32rpx;
	}

	.cont_two_bottom_ri {
		flex: 1;
		text-align: right;
	}

	.cont_two_bottom_ri image {
		width: 14rpx;
		height: 24rpx;
	}

	/* 外卖订单 */
	.cont_three {
		display: none;
		width: 94%;
		margin: 3% auto;
		background-color: #FFFFFF;
		border-radius: 18rpx;
	}

	.cont_three_top {
		width: 94%;
		padding: 4% 3% 0;
		margin: 0 auto;
		display: flex;
	}

	.cont_three_top_le {
		flex: 2;
		font-size: 30rpx;
		font-weight: 500;
		color: #D80204;
		line-height: 32rpx;
	}

	.cont_three_top_ri {
		flex: 1;
		text-align: right;
		font-size: 24rpx;
		font-weight: 500;
		color: #999999;
		line-height: 32rpx;
	}

	.cont_three_text {
		padding: 2% 0 2% 3%;
		font-size: 24rpx;
		font-weight: 500;
		color: #999999;
		line-height: 32rpx;
	}

	.cont_three_cen {
		width: 94%;
		padding: 0.5% 3%;
		margin: 0 auto;
		display: flex;
	}

	.cont_three_cen_le {
		flex: 2;
		font-size: 30rpx;
		font-weight: 500;
		color: #333333;
		line-height: 32rpx;
	}

	.cont_three_cen_ri {
		flex: 1;
		text-align: right;
		font-size: 24rpx;
		font-weight: 500;
		color: #999999;
		line-height: 32rpx;
	}

	.cont_three_text2 {
		font-size: 24rpx;
		font-weight: 500;
		color: #D80204;
		padding: 0 3%;
		line-height: 32rpx;
	}

	.cont_three_bottom {
		width: 94%;
		padding: 3%;
		margin: 3% auto 0;
		display: flex;
		border-top: 1rpx solid #E6E6E6;
	}

	.cont_three_bottom_le {
		flex: 1;
		font-size: 24rpx;
		font-weight: 500;
		color: #999999;
		line-height: 32rpx;
	}

	.cont_three_bottom_ri {
		flex: 1;
		text-align: right;
	}

	.cont_dis {
		display: block;
	}

	.box {
		/* width: 100%;
		height: 100vh;
		background-color: rgba(0, 0, 0, 0.2);

		position: absolute;
		top: 40px;
		z-index: 999 */
	}

	.popbox {
		width: 749upx;
		/* height: 764upx; */
		background: #FFFFFF;
		padding-bottom: 72rpx;
		z-inde: 999;
		position: fixed;
		top: 39px;
		left: 0;
		right: 0;
	}

	.impute {
		background: #F2F2F2;
		height: 80rpx;
		margin: 20rpx 0;
	}

	.btns {
		width: 690upx;
		height: 88upx;
		background: #FFCC00;
		box-shadow: 0upx 10upx 20upx 0upx #FFD9B3;
		border-radius: 16upx;
		text-align: center;
		line-height: 88rpx;
		font-size: 32rpx;
		font-weight: bold;
		margin: 0 auto;
	}

	.empty {
		width: 100%;
		background: #ffffff;
		/* #ifdef MP-WEIXIN */
		height: 93vh;
		/* #endif */
		/* #ifndef MP-WEIXIN */
		height: 80vh;
		/* #endif */
	}

	.tabs_box {
		display: none;
	}

	.dis {
		display: block;
		width: 100%;
	}

	.u-tab-item {
		font-size: 30rpx !important;
		/* color: #666666 !important; */
	}

	.success_box {
		width: 100%;
	}

	.order_box {
		width: 95%;
		margin: 0 auto;
		/* height: 420rpx; */
		background: #FFFFFF;
		margin-top: 20rpx;
		border-radius: 10px;
		/* padding: 20rpx 0rpx; */
	}

	.order_success {
		width: 90%;
		margin: 0 auto;
		display: flex;
		height: 80upx;
	}

	.order_name {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
		font-weight: bold;
		font-size: 31rpx;
		letter-spacing: 1upx;
	}

	.order_data {
		flex: 1;
		color: #999999;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		font-size: 27rpx;
	}

	.city_type {
		width: 90%;
		margin: 0 auto;
		height: 60upx;
		line-height: 60upx;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.type_name {
		font-size: 27rpx;
	}

	.city_text {
		line-height: 36rpx;
		color: #49A5FF;
		background: #c4e2ff;
		text-align: center;
		font-size: 25rpx;
		margin-left: 20rpx;
		padding: 2rpx 10rpx;
	}

	.city_address {
		width: 90%;
		margin: 0 auto;
		/* margin-top: -10rpx; */
	}

	/* 发货地址 */
	.fh_box {
		display: flex;
		/* height: 80upx; */
		margin-top: 25rpx;
	}

	.fh_image {
		/* flex: 1; */
		width: 10%;
	}

	.box {
		/* flex: 11; */
		width: 85%;
		overflow: hidden;
	}

	.fh_name {
		font-size: 31rpx;
		font-weight: 600;
		letter-spacing: 2upx;
	}

	.fh_type {
		color: #999999;
		font-size: 28rpx;
		margin-top: 10rpx;
	}

	.fh_type text {
		margin-left: 20upx;
	}

	/* 送货地址 */
	.sh_box {
		display: flex;
		margin-bottom: 14upx;
		margin-top: 25rpx;
	}

	.sh_image {
		/* flex: 1; */
		width: 10%;
	}

	.sh_name {
		font-size: 31rpx;
		font-weight: 600;
		letter-spacing: 2upx;
	}

	.sh_type {
		color: #999999;
		font-size: 28rpx;
		margin-top: 10rpx;
	}

	.sh_type text {
		margin-left: 20upx;
	}

	.fh_image image {
		width: 45upx;
		height: 45upx;
	}

	.sh_image image {
		width: 45upx;
		height: 45upx;
	}

	.order_btn {
		display: flex;
		justify-content: flex-end;
		align-items: center;
		height: 80upx;
		margin-top: 8upx;
		padding: 0 20rpx;
	}

	.btn1 {
		width: 170upx;
		font-size: 26rpx;
		line-height: 60upx;
		text-align: center;
		border: 3upx solid #9C9C9C;
		border-radius: 20upx;
		color: #9C9C9C;
		margin-right: 30upx;
	}

	.btn2 {
		width: 170upx;
		line-height: 60upx;
		color: white;
		background: #FF6A04;
		font-size: 27rpx;
		text-align: center;
		margin-right: 30upx;
		border-radius: 20upx;
	}

	.btn3 {
		width: 120upx;
		font-size: 26rpx;
		line-height: 60upx;
		text-align: center;
		border: 2upx solid #FF130A;
		border-radius: 10upx;
		color: #FF130A;
		margin-right: 30upx;
		background: rgba(255, 19, 10, 0.2);
		opacity: 0.6;
	}
</style>
