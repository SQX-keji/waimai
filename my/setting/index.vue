<template>
	<view class="bg-white padding-lr">
		<!-- <view class="flex  padding-tb" @click="goNav('/pages/public/pwd')">
			<view class="flex-sub text-df" style="line-height: 50upx;">修改密码</view>
			<image src="../../static/images/my/right.png" style="line-height: 50upx;width: 20rpx;height: 30rpx;"></image>
		</view> -->
		<view class="flex align-center padding-tb" @click="goNav('/my/setting/feedback')" v-if="XCXIsSelect=='是'">
			<view class="flex-sub text-df" style="line-height: 50upx;">意见反馈</view>
			<image src="../../static/images/index/right2.png" style="line-height: 50upx;width: 15rpx;height: 30rpx;">
			</image>
		</view>
		<view class="flex align-center padding-tb" @click="goNav('/my/setting/xieyi')">
			<view class="flex-sub text-df" style="line-height: 50upx;">用户协议</view>
			<image src="../../static/images/index/right2.png" style="line-height: 50upx;width: 15rpx;height: 30rpx;">
			</image>
		</view>
		<view class="flex align-center padding-tb" @click="goNav('/my/setting/mimi')">
			<view class="flex-sub text-df" style="line-height: 50upx;">隐私政策</view>
			<image src="../../static/images/index/right2.png" style="line-height: 50upx;width: 15rpx;height: 30rpx;">
			</image>
		</view>
		<view class="flex align-center padding-tb" @click="goNav('/my/setting/about')">
			<view class="flex-sub text-df" style="line-height: 50upx;">关于我们</view>
			<image src="../../static/images/index/right2.png" style="line-height: 50upx;width: 15rpx;height: 30rpx;">
			</image>
		</view>
		<view class="btn" @click="goOut">退出登录</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				XCXIsSelect: '是',
				checked: true
			}
		},
		onLoad() {
			this.XCXIsSelect = this.$queue.getData('XCXIsSelect') ? this.$queue.getData('XCXIsSelect') : '是'
			this.getUserInfo()
		},
		methods: {
			change(val) {
				this.checked = val
				this.$Request.post('/app/user/updateSendMsg', {
					isSendMsg: this.checked == true ? 1 : 2
				}).then(res => {
					if (res.code === 0) {
						this.content = res.data.value;
					}
				});
			},
			getUserInfo() {
				this.$Request.get("/app/user/selectUserById").then(res => {
					if (res.code == 0) {
						this.checked = res.data.isSendMsg == null || res.data.isSendMsg == 1 ? true : false
					}
				});
			},
			goNav(e) {
				uni.navigateTo({
					url: e
				})
			},
			goOut() {
				uni.showModal({
					title: '提示',
					content: '确定退出登录吗？',
					success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定');
							uni.removeStorageSync('userName')
							uni.removeStorageSync('avatar')
							uni.removeStorageSync('userId')
							uni.removeStorageSync('token')
							uni.removeStorageSync('phone')
							uni.removeStorageSync('zhiFuBaoName')
							uni.removeStorageSync('zhiFuBao')
							uni.removeStorageSync('invitationCode')
							uni.removeStorageSync('unionId')
							uni.removeStorageSync('openId')
							uni.removeStorageSync('isVIP')
							uni.removeStorageSync('wxCode')
							uni.removeStorageSync('wxQrCode')
							uni.removeStorageSync('sex')
							uni.showToast({
								title: '退出成功！',
								icon: 'none'
							})
							setTimeout(function() {
								uni.navigateBack()
							}, 1000)
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				})
			}
		}

	}
</script>

<style>
	page {
		background-color: #FFF;
	}

	.btn {
		width: 100%;
		height: 80upx;
		background: #FCD202;
		border-radius: 6upx;
		text-align: center;
		line-height: 80upx;
		margin-top: 40upx;
		font-size: 34upx;
		color: #fff;
	}
</style>
