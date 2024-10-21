<template>
	<view class="address">
		<view class="bg-white radius margin-sm" v-for="(item, index) in list" :key="index">
			<view class="content_top" @click="goBack(item)">
				<view class="content_part1">
					<view class="content_name">{{item.userName}}</view>
					<view class="content_number">
						{{item.userPhone}}
					</view>
					<view class="content_btn" v-if="item.addressDefault">默认</view>
				</view>
				<view class="content_part2">
					{{item.province}}{{item.city}}{{item.district}}{{item.addressDetail}}
				</view>
			</view>
			<!-- 线条 -->
			<!-- <u-line color="#c1c1c1" border-style="solid" direction="row"></u-line> -->
			<view class="content_bottom">
				<view class="bottom_right">
					<view class="write" @click.stop="update(item)">
						<image src="../static/address/write.png"></image>
					</view>
					<view class="dete" @click.stop="del(item)">
						<image src="../static/address/dete.png"></image>
					</view>
				</view>
			</view>
		</view>
		<empty v-if="list.length == 0"></empty>

		<view style="height: 100rpx;"></view>
		<!-- 添加收货地址 -->
		<view class="btn">
			<view class="address_push" @click="addAddress">添加收货地址</view>
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
				list: [],
				checked: false,
				page: 1,
				limit: 10,
				add: 0,
				addressType: '',
				totalCount: 0
			}
		},
		onLoad(option) {
			this.add = option.add ? option.add : 0
			
			if (option.addressType) {
				this.addressType = option.addressType
			}
		},
		onShow() {
			this.page = 1
			this.getAddressList()
		},
		methods: {
			bindOrdersure() {
				uni.navigateTo({
					url: '/pages/order_sure/order_sure'
				})
			},
			// 添加地址
			addAddress() {
				uni.navigateTo({
					url: '/my/address/add?page=2'
				})
			},
			// 获取地址列表
			getAddressList() {
				let data = {
					page: this.page,
					limit: this.limit
				}
				this.$Request.get("/app/address/selectAddressList", data).then(res => {
					if (res.code == 0) {
						this.totalCount = res.data.totalCount
						if (this.page == 1) {
							this.list = res.data.list
						} else {
							this.list = [...this.list, ...res.data.list]
						}
					}
					uni.stopPullDownRefresh();
					uni.hideLoading();
				});
			},
			// 设为默认地址
			checkboxChange(e) {
				console.log(e)
				// this.list.forEach(res => {
				// if (res.addressId == e.addressId) {
				let data = {
					addressId: e.addressId,
					userName: e.userName,
					userPhone: e.userPhone,
					address: e.address,
					addressDetail: e.addressDetail,
					addressDefault: e.addressDefault ? 0 : 1
				}
				this.$Request.postJson("/app/address/updateAddress", data).then(res => {
					if (res.code == 0) {
						this.getAddressList()
					}
				});
				// } else {
				// res.addressDefault = 0
				// }
				// })
			},
			// 跳转修改
			update(e) {
				uni.navigateTo({
					url: '/my/address/add?id=' + e.addressId
				})
			},
			// 删除地址
			del(e) {
				let that = this
				uni.showModal({
					title: '提示',
					content: '确认删除吗',
					success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定');
							that.$Request.post("/app/address/deleteAddress", {
								addressId: e.addressId
							}).then(res => {
								if (res.code == 0) {
									uni.showToast({
										title: '删除成功',
										icon: 'none'
									})
									that.page = 1
									that.getAddressList()
								}
							});
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			// 点击地址返回上一页
			goBack(e) {
				console.log(e)
				if (this.add) {
					let pages = getCurrentPages();
					let prevPage = pages[pages.length - 2];
					// 修改上一页面的addressId
					// #ifdef MP-WEIXIN
					prevPage.$vm._data.addressId = e.addressId
					prevPage.$vm._data.addressType = this.addressType
					uni.navigateBack()
					// #endif
					// #ifdef H5
					prevPage._data.addressId = e.addressId
					prevPage._data.addressType = this.addressType
					uni.navigateBack()
					// #endif
					
					
					// #ifdef APP-PLUS
					uni.setStorageSync('addressId',e.addressId)
					uni.setStorageSync('addressType',this.addressType)
					setTimeout(function(){
						uni.navigateBack()
					},10)
					// #endif
				}
				
			}
		},
		onReachBottom: function() {
			if(this.list.length<this.totalCount) {
				this.page = this.page + 1;
				this.getAddressList()
			} else {
				uni.showToast({
					title: '已经到底了',
					icon: 'none'
				})
			}
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.getAddressList();
		},
	}
</script>

<style>
	body {
		background-color: #eeeeee;
		background-size: cover;
		box-sizing: border-box;
	}

	.address {
		width: 100%;
	}

	.address_content {
		width: 100%;
		background: #FFFFFF;
		margin-top: 20rpx;
	}

	.content_top {
		width: 90%;
		margin: 0 auto;
	}

	.content_part1 {
		font-size: 32rpx;
		color: #333333;
		display: flex;
		align-items: center;
		padding-top: 30rpx;
	}

	.content_name {
		font-size: 36rpx;
		font-weight: bold;
	}

	.content_number {
		margin-left: 20rpx;
	}

	.content_btn {
		padding: 4rpx 10rpx;
		border-radius: 10rpx;
		border: 1rpx solid #FCD202;
		color: #FCD202;
		margin-left: 20rpx;
		font-size: 22rpx;
		text-align: center;
	}

	.content_part2 {
		color: #999999;
		font-size: 28rpx;
		margin-top: 20rpx;
		margin-bottom: 30rpx;
	}

	.content_bottom {
		display: flex;
		width: 90%;
		margin: 0 auto;
		height: 60rpx;
	}

	.bottom_left {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.bottom_right {
		flex: 1;
		position: relative;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.write image {
		width: 35rpx;
		height: 35rpx;
	}

	.dete image {
		width: 35rpx;
		height: 35rpx;
	}

	.write {
		position: absolute;
		right: 75rpx;
	}

	.dete {
		position: absolute;
		right: 0rpx;
	}

	/* 添加收货地址 */
	.btn {
		position: fixed;
		bottom: 0rpx;
		width: 100%;
		height: 100rpx;
		line-height: 100rpx;
		background-color: white;
		padding-top: 10rpx;
	}

	.address_push {
		width: 90%;
		height: 80rpx;
		margin: 0 auto;
		background: #FCD202;
		border-radius: 20rpx;
		color: white;
		text-align: center;
		line-height: 80rpx;
		font-size: 35rpx;
	}
</style>
