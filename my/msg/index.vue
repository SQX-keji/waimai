<template>
	<view class="content">
		<!-- <view class="navbar">
			<view v-for="(item, index) in tabList" :key="index" class="nav-item"
				:class="{ current: tabFromIndex === item.state }" @click="tabClicks(item.state)">
				{{ item.text }}
			</view>
		</view> -->
		<view v-for="(item, index) in list" :key="index" class="item" @click="goDet(item.content)">
			<view class="flex justify-between"
				style="font-size: 30upx;width: 100%;overflow: hidden;text-overflow: ellipsis;white-space:nowrap">
				<view class="text-bold">{{ item.title }}</view>
				<!-- <view v-if="item.isSee == 0"
					style="height: 32rpx;width: 32rpx;border-radius: 100rpx;background-color: red;color: #FFF;text-align: center;">
				</view> -->
			</view>
			<view class="flex justify-between">
				<view style="color: #999999;font-size: 28upx;margin-top: 10upx;">{{ item.content }}</view>

				<view style="margin-top: 10upx;color: #999999;font-size: 28upx;text-align: right;">{{ item.createAt }}
				</view>
			</view>

		</view>
		<!-- <view v-if="list.length === 0" style="background: #1c1b20;text-align: center;padding-top: 140upx;color: #FFFFFF;">暂无消息</view> -->
		<empty v-if="list.length === 0" des="暂无消息" show="false"></empty>
	</view>
</template>

<script>
	import empty from '@/components/empty';

	export default {
		components: {
			empty
		},
		data() {
			return {
				tabFromIndex: 5,
				tabCurrentIndex: 0,
				fromInfo: 5,
				list: [],
				page: 1,
				limit: 20,
				scrollTop: false,
				tabList: [{
						state: 5,
						text: '用户消息',
						totalElements: 0
					},
					{
						state: 4,
						text: '订单消息',
						totalElements: 0
					}
				],
				totalCount: 0
			};
		},
		onPageScroll: function(e) {
			this.scrollTop = e.scrollTop > 200;
		},
		onLoad(options) {
			this.$queue.showLoading("加载中...")
			this.loadData();
		},
		methods: {
			goDet(e) {
				console.log(e.indexOf('下单'))
				if (e.indexOf('下单') != -1) {
					uni.navigateTo({
						url: '/my/order/index'
					})
				} else if (e.indexOf('接单') != -1) {
					uni.navigateTo({
						url: '/my/takeOrder/index'
					})
				} else if (e.indexOf('订单审核通过') != -1) {
					uni.navigateTo({
						url: '/my/publish/index'
					})
				}
			},
			//顶部渠道点击
			tabClicks(index) {
				this.list = [];
				this.page = 1;
				this.tabFromIndex = index;
				this.$queue.showLoading("加载中...")
				this.loadData();
			},
			//获取消息列表
			loadData() {
				let that = this;
				let number = 10;
				let token = this.$queue.getData('token');
				if (token) {
					let data = {
						page: this.page,
						limit: this.limit,
						// state: this.tabFromIndex
					}
					this.$Request.getT('/app/message/selectMessageByUserId', data).then(res => {
						if (res.code === 0) {
							this.totalCount = res.data.totalCount
							if (this.page == 1) {
								this.list = res.data.list
							} else {
								res.data.list.forEach(d => {
									this.list.push(d);
								});
							}
						}
						uni.hideLoading();
						uni.stopPullDownRefresh();
					});
				}
			}
		},
		onReachBottom: function() {
			if(this.list.length<this.totalCount) {
				this.page = this.page + 1;
				this.loadData()
			} else {
				uni.showToast({
					title: '已经到底了',
					icon: 'none'
				})
			}
		},
	};
</script>

<style lang="scss">
	page,
	page {
		// background: #111224;
	}

	.content {
		// background: #111224;
		height: 100%;
	}

	.navbar {
		display: flex;
		height: 40px;
		padding: 0 5px;
		// background: #1E1F31;
		box-shadow: 0 1px 5px rgba(0, 0, 0, 0.06);
		position: relative;
		z-index: 10;

		.nav-item {
			flex: 1;
			display: flex;
			justify-content: center;
			align-items: center;
			height: 100%;
			font-size: 15px;
			// color: #FFFFFF;
			position: relative;

			&.current {
				color: #5E81F9;

				&:after {
					content: '';
					position: absolute;
					left: 50%;
					bottom: 0;
					transform: translateX(-50%);
					width: 44px;
					height: 0;
					border-bottom: 2px solid #5E81F9;
				}
			}
		}
	}

	.item {
		// background: #1E1F31;
		padding: 16rpx 25rpx;
		margin: 16rpx;
		font-size: 28rpx;
		box-shadow: 7rpx 9rpx 34rpx rgba(0, 0, 0, 0.1);
		border-radius: 16upx;
		background: #fff;
	}
</style>
