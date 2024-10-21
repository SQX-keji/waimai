<template>
	<view>
		<view class="empty" v-if="moneylist == ''">
			<view
				style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
				<image src="../../static/images/empty.png" style="width: 300rpx;height: 300rpx;"></image>
				<view style="color: #CCCCCC;">暂无内容</view>
			</view>
		</view>
		<view class="popup_money" v-else>
			<view class="data_select">
				<view class="money_box" v-for="(item,index) in moneylist" :key="index">
					<view class="box_tit">
						<view class="money_name">{{item.redPacketTitle}}</view>
						<view class="money_price">￥{{item.redPacketAmount}}</view>
					</view>
					<view class="money_data">有效期至{{item.expirationTime}}</view>
					<view class="money_line">
						<u-line direction="row" color="#E6E6E6" border-style="dashed" />
					</view>
					<view class="box_bottom">
						<view class="money_use">满{{item.minimumAmount}}元可使用</view>
						<!-- <view class="money_btn">
							<view class="lj_use">
								立即使用
							</view>
						</view> -->
					</view>
				</view>
			</view>

		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				moneylist: ''
			}
		},
		onLoad() {
			this.hongbao()
		},
		methods: {
			//获取登录用户的所有红包
			hongbao() {
				this.$Request.getT('/app/tbindent/findAllRedPacket').then(res => {
					console.log(res)
					if (res.code === 0) {
						this.moneylist = res.data
						// console.log(this.hongbao)
					}
				});

			}
		},
	}
</script>

<style>
	body {
		background-color: #F5F5F5;
	}

	/* #ifndef MP-WEIXIN */
	page {
		background: #F2EDED;
	}

	/* #endif */

	.empty {
		width: 100%;
		background: #ffffff;
		/* #ifdef MP-WEIXIN */
		height: 93vh;
		/* #endif */
		/* #ifndef MP-WEIXIN */
		height: 100vh;
		/* #endif */
	}

	.popup_money {
		height: 800upx;
		width: 100%;
		position: relative;
	}

	.u-drawer-bottom {
		background-color: #FAF7F5 !important;
	}

	.money_box {
		width: 93%;
		margin: 0 auto;
		background: #ffffff;
		border-radius: 14upx;
		height: 220rpx;
		margin-bottom: 20upx;
		margin-top: 20rpx;
	}

	.box_tit {
		width: 90%;
		margin: 0 auto;
		height: 80upx;
		display: flex;
	}

	.money_name {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
		font-size: 27rpx;
		font-weight: bold;
		letter-spacing: 2upx;
	}

	.money_price {
		flex: 1;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		font-size: 39upx;
		/* font-weight: bold; */
		color: red;
	}

	.money_data {
		color: #999999;
		font-size: 24rpx;
		width: 90%;
		margin: 0 auto;
		margin-top: -8upx;
	}

	.u-line {
		width: 90% !important;
		border-bottom-width: 6upx !important;
		margin: 0 auto !important;
		margin-top: 22upx !important;
		margin-bottom: 22upx !important;
	}

	.box_bottom {
		width: 90%;
		margin: 0 auto;
		display: flex;
		height: 40upx;
	}

	.money_use {
		flex: 1;
		color: #999999;
		font-size: 24rpx;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.lj_use {
		width: 150rpx;
		border: 2rpx solid #FF7F00;
		color: #FF7F00;
		text-align: center;
		line-height: 48rpx;
		border-radius: 40rpx;
		font-size: 23rpx;
	}
</style>
