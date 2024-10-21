<template>
	<view>
		<view style="text-align: left;padding-bottom: 10rpx;">
			<view v-for="(item, index) in list" :key="index" class="item">
				<view>
					<!-- <view style="margin-bottom: 8upx;text-align: right;">
						<text v-if="item.type == 1" style="margin-bottom: 8upx;color: #ecd4b4">充值</text>
						<text v-if="item.type == 2" style="margin-bottom: 8upx;color: #ecd4b4">提现</text>
					</view> -->
					<view style="color: #999999;font-size: 28upx;">
						<view style="margin-bottom: 8upx">{{item.title}}</view>
						<!-- <view v-if="item.classify === 2" style="margin-bottom: 8upx"> 返佣类型：直属返佣</view> -->
						<!-- <view v-if="item.classify === 3" style="margin-bottom: 8upx"> 返佣类型：非直属支付</view> -->
						<view style="margin-bottom: 8upx">{{item.content}}</view>
						<view style="margin-bottom: 8upx"> 创建时间：{{item.createTime}}</view>
						<view style="margin-bottom: 8upx;text-align: right;">
							<text v-if="item.type == 1" style="color: #ecd4b4;font-size: 32upx;font-weight: 600"><text
									class="text-olive">+</text>￥{{item.money}}</text>
							<text v-if="item.type == 2" style="color: #ecd4b4;font-size: 32upx;font-weight: 600"><text
									class="text-red">-</text>￥{{item.money}}</text>
						</view>
					</view>
				</view>
			</view>
			<!-- 加载更多提示 -->
			<!-- <view class="s-col is-col-24" v-if="list.length > 0">
				<load-more :loadingType="loadingType" :contentText="contentText"></load-more>
			</view> -->
			<!-- 加载更多提示 -->
			<!-- <empty v-if="list.length === 0" des="暂无明细数据" show="false"></empty> -->
			<empty v-if="list.length == 0" content="暂无明细"></empty>
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
				page: 1,
				limit: 10,
				tabIndex: 1,
				checkReZhiShu: '否',
				checkReTuanZhang: '否',
				checkReFeiZhiShu: '否',
				scrollTop: false,
				contentText: {
					contentdown: '上拉显示更多',
					contentrefresh: '正在加载...',
					contentnomore: '没有更多数据了'
				},
				totalCount: 0
			}
		},
		onLoad() {
			this.$queue.showLoading("加载中...");
			this.getList();
		},
		onPageScroll: function(e) {
			this.scrollTop = e.scrollTop > 200;
		},
		methods: {
			getList() {
				let data = {
					page: this.page,
					limit: this.limit,
					classify: 3
				}
				this.$Request.get('/app/userMoney/balanceDetailed', data).then(res => {
					if (res.code === 0) {
						this.totalCount = res.data.totalCount
						if (this.page === 1) {
							this.list = res.data.list;
						} else {
							this.list = [...this.list, ...res.data.list];
						}
					}
					uni.stopPullDownRefresh();
					uni.hideLoading();
				});
			}
		},
		onReachBottom: function() {
			if(this.list.length<this.totalCount) {
				this.page = this.page + 1;
				this.getList()
			} else {
				uni.showToast({
					title: '已经到底了',
					icon: 'none'
				})
			}
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.getList();
		}
	}
</script>

<style lang="less">
	page {
		// background: #1c1b20;
	}

	.tui-tab-item-title {
		// color: #ffffff;
		font-size: 30rpx;
		height: 80rpx;
		line-height: 80rpx;
		flex-wrap: nowrap;
		white-space: nowrap;
	}

	.tui-tab-item-title-active {
		border-bottom: 1px solid #5E81F9;
		color: #5E81F9;
		font-size: 32upx;
		font-weight: bold;
		border-bottom-width: 6upx;
		text-align: center;
	}

	.item {
		background: #FFFFFF;
		padding: 32rpx;
		margin: 32rpx;
		font-size: 28rpx;
		box-shadow: 7px 9px 34px rgba(0, 0, 0, 0.1);
		border-radius: 16upx;
	}
</style>
