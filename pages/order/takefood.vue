<template>
	<view class="pages">
		<!-- 余额 -->
		<view class="tosend" v-if="dataDet">
			<view class="tosend_text" v-if="dataDet.status == 3&&dataDet.orderType==1">待取餐</view>
			<view class="tosend_text" v-if="dataDet.status == 3&&dataDet.orderType==2">配送中</view>
			<view class="tosend_text" v-if="dataDet.status == 4">已完成</view>
			<view class="tosend_text" v-if="dataDet.status == 5">已取消</view>
			<view class="tosend_text" v-if="dataDet.status == 6">制作中</view>
			<view class="tosend_text" v-if="dataDet.status == 7">待接单</view>
			<view class="tosend_text" v-if="dataDet.status == 8">已取消</view>
			<view class="tosend_header">
				<!-- 排序 -->
				<view class="cont_two_top" v-if="dataDet.status == 3&&dataDet.orderType==1">
					<view class="cont_two_top_le">取餐号码</view>
					<!-- <view class="cont_two_top_ri">制作中</view> -->
				</view>
				<view v-if="dataDet.status == 3&&dataDet.orderType==1" class="cont_two_text">{{dataDet.orderCode}}
				</view>
				<view v-if="dataDet.status == 3&&dataDet.orderType==1" class="cont_two_text2">
					前面还有<text>{{dataDet.countOrder}}</text>个订单</view>

				<!-- 商品列表 -->
				<view v-if="dataDet" class="tosend_header_food" v-for="(item,index) in dataDet.orderGoodsList" :key='index'>
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
							<view>X{{item.goodsNum}}</view>
						</view>
						<view class="flex justify-between align-center text-gray" v-if="item.goodsPack">
							<view>打包费</view>
							<view>￥{{item.goodsPack}} / 份</view>
						</view>
					</view>
				</view>
				<view class="tosend_header_do " v-if="dataDet.couponMoney>0">
					<view>优惠券</view>
					<view class="tosend_header_do_ri">-￥{{dataDet.couponMoney}}</view>
				</view>
				<view class="tosend_header_do do_bot" v-if="dataDet.errandMoney>0&&dataDet.orderType==2">
					<view>跑腿费</view>
					<view class="tosend_header_do_ri">￥{{dataDet.errandMoney}}</view>
				</view>
				<view class="tosend_header_do">
					<view class="tosend_header_do_le2">实付</view>
					<view class="tosend_header_do_ri2"><text>￥</text>{{dataDet.payMoney}}</view>
				</view>
			</view>
			<view class="text-center" v-if="dataDet">
				<map v-if="dataDet.status == 3 && latitude && longitude" id="map" @tap="goMap"
					style="width: 95%; height: 300rpx;margin: 20rpx auto 0;" :markers="markers" :latitude="latitude"
					:longitude="longitude"></map>
			</view>
			
			<!-- 骑手信息 -->
			<view class="rider_box" v-if="dataDet&&(dataDet.status == 3||dataDet.status == 4)&&dataDet.riderUserId">
				<view class="flex justify-between align-center padding">
					<view style="font-size: 31rpx;color: black;">联系骑手...</view>
					<image @click="complaint" src="../../static/images/order/tousu.png"
						style="width: 43rpx;height: 39rpx;" mode=""></image>
				</view>
				<view>
					<u-line color="#F2F2F2" />
				</view>
				<view style="padding: 20rpx 25rpx;display: flex;justify-content: space-between;align-items: center;">
					<view style="display: flex;">
						<view class="rider_left">
							<image :src="dataDet.riderAvatar"></image>
						</view>
						<view style="margin-left: 10rpx;color: #333333;">
							<view>{{dataDet.riderUserName}}</view>
							<view>{{dataDet.riderPhone1}}</view>
						</view>
					</view>
					<view class="phone" @click="bindphone(dataDet.riderPhone)">联系TA</view>
				</view>
			</view>
			<!-- 其他信息 -->
			<view class="tosend_cont" v-if="dataDet">
				<view class="tosend_header_text">
					<text>订单信息</text>
				</view>
				<view class="tosend_cont_infor" v-if="dataDet.orderType==1">
					<view class="tosend_cont_infor_le">取餐号码</view>
					<view class="tosend_cont_infor_ri">{{dataDet.orderCode}}</view>
				</view>
				<view class="tosend_cont_infor">
					<view class="tosend_cont_infor_le">订单编号</view>
					<view class="tosend_cont_infor_ri">{{dataDet.orderNumber}}
						<u-icon @click="copy(dataDet.orderNumber)" name="order" style="margin-left: 6rpx;" size="32">
						</u-icon>
					</view>
				</view>
				<view class="tosend_cont_infor">
					<view class="tosend_cont_infor_le">下单时间</view>
					<view class="tosend_cont_infor_ri">{{dataDet.payTime}}</view>
				</view>
				<view class="tosend_cont_infor">
					<view class="tosend_cont_infor_le">支付方式</view>
					<view class="tosend_cont_infor_ri" v-if="dataDet.payType==1">微信支付</view>
					<view class="tosend_cont_infor_ri" v-if="dataDet.payType==2">余额支付</view>
				</view>
				<view class="tosend_cont_infor" v-if="dataDet.orderType==2">
					<view class="tosend_cont_infor_le">联系人</view>
					<view class="tosend_cont_infor_ri">{{dataDet.address.userName}}</view>
				</view>
				<view class="tosend_cont_infor" v-if="dataDet.orderType==2">
					<view class="tosend_cont_infor_le">联系方式</view>
					<view class="tosend_cont_infor_ri">{{dataDet.address.userPhone}}</view>
				</view>
				<view class="tosend_cont_infor" v-if="dataDet.orderType==2">
					<view class="tosend_cont_infor_le">详细地址</view>
					<view class="tosend_cont_infor_ri">
						{{dataDet.address.province}}{{dataDet.address.city}}{{dataDet.address.district}}{{dataDet.address.addressDetail}}
					</view>
				</view>
			</view>
		</view>
		<view style="width: 100rpx;height: 100rpx;position: fixed;bottom: 100rpx;right: 70rpx;" v-if="dataDet">
			<image src="../../static/images/order/kefu.png" style="width: 100%;height: 100%;" @click="goChat()" mode="">
			</image>
			<view class="shopxiaoix" v-if="RiderUnreadCount>0">{{RiderUnreadCount}}</view>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				orderId: '',
				dataDet: '',
				markers: [], //标记点
				latitude: '',
				longitude: '',
				RiderUnreadCount: 0,
			}
		},
		onLoad(option) {
			let that = this
			uni.showLoading({
				title: '加载中...'
			})
			that.orderId = option.orderId
			uni.getLocation({
				type: 'gcj02', //返回可以用于uni.openLocation的经纬度
				success: function(res) {
					that.lat = res.latitude;
					that.lng = res.longitude;
					that.getDataDet()
				}
			});
			this.getRiderUnreadCount()
		},
		created() {
			this.RiderUnreadCount = setInterval(() => {
				this.getRiderUnreadCount()
			}, 5000)
		},
		methods: {
			getDataDet() {
				let data = {
					orderId: this.orderId
				}
				this.$Request.get("/app/order/selectOrderById", data).then(res => {
					uni.hideLoading()
					if (res.code == 0) {
						this.dataDet = res.data
						// this.dataDet.goodsMessage = JSON.parse(this.dataDet.goodsMessage)

						this.dataDet.orderCode = this.dataDet.orderCode.substring(this.dataDet.orderCode.length -
							3, this.dataDet.orderCode.length)
						this.dataDet.orderGoodsList.forEach(res => {
							res.goodsPicture = res.goodsPicture.split(',')
						})
						if (this.dataDet.riderPhone) {
							this.dataDet.riderPhone1 = this.dataDet.riderPhone.substring(0, 3) + "****" + this
								.dataDet.riderPhone.substring(7, 11);
						}

						this.dataDet.address = this.dataDet.address ? JSON.parse(this.dataDet.address) : ''
						console.log(this.dataDet.address)
						if (this.dataDet.riderUserId) {
							this.getLocation(this.dataDet.riderUserId)
						}
					}
				});
			},
			goChat() {
				uni.navigateTo({
					url: '/pages/index/shop/im?ordersId=' + this.orderId
				})
			},
			getLocation(e) {
				let data = {
					riderUserId: e,
					lat: this.lat,
					lng: this.lng
				}
				this.$Request.getT('/timedtask/selectRiderLocation', data).then(res => {
					if (res.code === 0) {

						console.log(res.data, '经纬度')
						this.latitude = res.data.riderLocation.lat;
						this.longitude = res.data.riderLocation.lng;

						this.markers = [{
							id: 1,
							latitude: res.data.riderLocation.lat,
							longitude: res.data.riderLocation.lng,
							iconPath: '../../static/images/order/rider.png',
							width: '40',
							height: '40'
						}]
					}
				});
			},
			goMap() {
				uni.navigateTo({
					url: '/pages/order/waimaiMap?orderId=' + this.orderId
				})
			},
			// 拨打电话
			bindphone(e) {
				console.log(e)
				uni.makePhoneCall({
					phoneNumber: e
				});
			},
			complaint() {
				uni.navigateTo({
					url: '/pages/order/complaint/complaint?indentNumber=' + this.dataDet.indentNumber +'&indentType=5'
				})
			},
			copy(e) {
				uni.setClipboardData({
					data: e,
					success: () => {
						uni.showToast({
							title: '复制成功'
						})
					}
				})
			},
			getRiderUnreadCount() {
				let that = this
				let data = {
					ordersId: that.orderId
				}
				that.$Request.getT("/app/ordersChat/selectUserUnreadCount", data).then(res => {
					if (res.code == 0) {
						if (res.data > 0) {
							if (that.RiderUnreadCount != res.data) {
								that.aplayAudio()
								that.RiderUnreadCount = res.data
							}
						} else {
							that.RiderUnreadCount = 0
						}
					}
				});
			},
			// 语音播报
			aplayAudio() {
				// const audio = document.getElementById('audio')

				// audio.play()
				// console.log('语音提示')
				const innerAudioContext = uni.createInnerAudioContext();
				innerAudioContext.autoplay = true;
				innerAudioContext.src =
					'https://pw.xianmxkj.com/file/uploadPath/2022/01/19/0753211f78d718d44ee6372e33eae9ee.mp3';
				innerAudioContext.onPlay(() => {
					console.log('开始播放');
				});
				innerAudioContext.onError((res) => {
					console.log(res.errMsg);
					console.log(res.errCode);
				});
			},
		}
	}
</script>

<style scoped>
	/* 余额 */
	.tosend {
		width: 100%;
		height: 280rpx;
		background: -webkit-linear-gradient(top, #FCD202, #F5F5F5);
	}

	.tosend_text {
		padding: 3% 3% 4%;
		font-size: 48rpx;
		font-weight: 800;
		color: #333333;
	}

	.tosend_header {
		width: 94%;
		background-color: #FFFFFF;
		margin: 0 auto;
		border-radius: 18rpx;
		padding: 3%;
	}

	/* 排序 */
	.cont_two_top {
		width: 100%;
		display: flex;
		justify-content: space-between;
	}

	.cont_two_top_le {
		font-size: 30rpx;
		font-weight: 500;
		color: #333333;
	}

	.cont_two_top_ri {
		padding: 6rpx 10rpx;
		text-align: center;
		background: rgba(255, 19, 10, 0.2);
		font-size: 24rpx;
		border: 2rpx solid #FF130A;
		color: #FF130A;
		opacity: 0.6;
		border-radius: 8rpx;
	}

	.cont_two_text {
		font-size: 56rpx;
		text-align: center;
		font-weight: bold;
		color: #333333;
		margin: 5% 0;
		line-height: 32rpx;
	}

	.cont_two_text2 {
		font-size: 30rpx;
		width: 100%;
		font-weight: 500;
		color: #333333;
		text-align: center;
		padding-bottom: 4%;
		line-height: 32rpx;
		border-bottom: 1rpx solid #E6E6E6;
	}

	.cont_two_text2 text {
		color: #FF130A;
	}

	/* 商品列表 */
	.tosend_header_text {
		font-weight: 800;
		color: #333333;
		line-height: 2;
		font-size: 30rpx;
		display: flex;
		justify-content: space-between;
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
		line-height: 1.5;
	}

	.tosend_header_do view {
		flex: 1;
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

	/* 其他信息 */
	.tosend_cont {
		width: 94%;
		background-color: #FFFFFF;
		margin: 3% auto;
		border-radius: 18rpx;
		padding: 3%;
	}

	.tosend_cont_infor {
		display: flex;
		margin-top: 3%;
	}

	.tosend_cont_infor_le {
		flex: 1;
		color: #999999;
	}

	.tosend_cont_infor_ri {
		flex: 2;
		text-align: right;
		color: #333333;
	}

	.tosend_cont_text {
		text-align: right;
	}

	/* 联系骑手 */
	.rider_box {
		width: 95%;
		margin: 0 auto;
		background: #ffffff;
		margin-top: 20rpx;
		border-radius: 10rpx;

	}

	.rider_left image {
		width: 80rpx;
		height: 85rpx;
		border-radius: 60%;
	}

	.phone {
		background: #FD6416;
		color: #ffffff;
		padding: 8rpx 15rpx;
		border-radius: 13rpx;
		font-size: 24rpx;
	}

	.shopxiaoix {
		background: red;
		color: #ffffff;
		width: auto;
		padding: 5rpx 12rpx;
		height: auto;
		text-align: center;
		border-radius: 30rpx;
		position: absolute;
		top: -4rpx;
		right: -4rpx;
	}
</style>
