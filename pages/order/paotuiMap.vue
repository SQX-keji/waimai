<template>
	<view>
		<map id="map" style="width: 100%; height: 700px;" :latitude="latitude" :longitude="longitude" :markers="markers"
			:show-location="true">
		</map>

		<cover-view class="controls-title">
			<cover-view class="tabs_box">
				<cover-image class="pay_img" src="../../static/images/order/avatar.png"></cover-image>
				<cover-view class="flex flex-sub margin-left-sm flex-direction justify-between">
					<cover-view class="pay_name">骑手预计{{rider.mDateTime[1]}}送达</cover-view>
					<cover-view class="text-gray margin-top" style="margin-top: 5rpx;">
						距离您{{rider.aDouble}}
					</cover-view>
				</cover-view>
				<cover-view class="flex">
					<cover-image class="pay_img1 margin-right" @click="goNav" src="../../static/images/order/im.png"></cover-image>
					<cover-image class="pay_img1" @click="call" src="../../static/images/order/phone.png"></cover-image>
				</cover-view>
			</cover-view>
		</cover-view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				latitude: '',
				longitude: '',
				markers: [], //标记点
				indentNumber: '',
				riderUserId: '',
				orderDetails: {},
				rider: {},
				timer: ''
			}
		},
		onLoad(option) {
			let that = this
			that.indentNumber = option.indentNumber

			that.getData()

			
		},
		onShow() {
			let that = this
			this.timer = setInterval(function() {
				that.getLocation()
			}, 10000)
		},
		onHide() {
			console.log(this.timer,'定时器')
			clearInterval(this.timer)
		},
		methods: {
			getData() {
				this.$Request.postT('/app/tbindent/userIndentMessage?indentNumber=' + this.indentNumber).then(res => {
					console.log(res)
					if (res.code == 0) {
						this.orderDetails = res.data
						console.log(this.orderDetails.avatar)
						let marker = {
							id: 1,
							latitude: res.data.userLat,
							longitude: res.data.userLng,
							iconPath: '../../static/images/order/01.png',
							width: '40',
							height: '40'
						}
						this.markers.push(marker)
						this.riderUserId = this.orderDetails.riderUserId
						this.getLocation()
						console.log(this.markers)
					}
				});
			},
			getLocation() {
				let data = {
					riderUserId: this.riderUserId,
					lat: this.orderDetails.userLat,
					lng: this.orderDetails.userLng
				}
				this.$Request.getT('/timedtask/selectRiderLocation', data).then(res => {
					if (res.code === 0) {
						this.latitude = res.data.riderLocation.lat;
						this.longitude = res.data.riderLocation.lng;
						this.rider = res.data
						this.rider.mDateTime = res.data.mDateTime.split(' ')
						if (this.rider.aDouble > 1000) {
							this.rider.aDouble = Number((this.rider.aDouble / 1000)).toFixed(2)+"km"
						}else{
							if(this.rider.aDouble==0){
								this.rider.aDouble = "0m";
							}else{
								this.rider.aDouble = Number(this.rider.aDouble).toFixed(1) +"m";
							}
						}
						
						let marker = {
							id: 2,
							latitude: res.data.riderLocation.lat,
							longitude: res.data.riderLocation.lng,
							iconPath: '../../static/images/order/rider.png',
							width: '40',
							height: '40',
						}
						this.markers.push(marker)
					}
				});
			},
			call() {
				uni.makePhoneCall({
					phoneNumber: this.orderDetails.phone
				});
			},
			goNav() {
				uni.navigateTo({
					url: '/pageA/kefu/kefu'
				})
			}
		},
	}
</script>

<style>
	.controls-title {
		width: 90%;
		/* height: 220upx; */
		/* line-height: 220upx; */
		background: #FFFFFF;
		position: fixed;
		bottom: 0rpx;
		margin: 40upx;
		border-radius: 26upx;
		box-shadow: 0upx 30upx 40upx 0upx rgba(187, 170, 163, 0.20);
		/* margin: 0 40rpx; */
		margin-bottom: 50rpx;
	}

	.tabs_box {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 10rpx;
		margin: 20rpx;
	}

	.pay_tit {
		flex: 1;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}

	.pay_img {
		width: 100rpx;
		height: 100rpx;
		border-radius: 10rpx;
	}
	.pay_img1 {
		width: 60rpx;
		height: 60rpx;
		border-radius: 10rpx;
	}

	.tabs_bottom {
		margin: 0 20rpx 20rpx;
		display: flex;
	}

	.pay_btn {
		padding: 5rpx 16rpx;
		border: solid 2rpx #999999;
		margin-right: 20rpx;
		display: flex;
		align-items: center;
		border-radius: 10rpx;
	}

	.pay_name {
		font-size: 32rpx;
		font-weight: bold;
	}

	.pay_line {
		height: 2rpx;
		background-color: #afafaf;
		margin: 10rpx 0;
	}
</style>
