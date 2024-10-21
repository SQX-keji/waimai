<template>
	<view class="content">
		<!-- <view class="complain_cont">
			<view class="complain_tabs" v-show="!isShow">
				<u-tabs :list="list" :is-scroll="true" name="illegal" :current="current" active-color="#FF7F00"
					@change="change"></u-tabs>
			</view>
		</view> -->
		<!-- <u-tabs :list="listTab" :is-scroll="false" inactive-color="#333333" active-color="#FF7F00" :current="currentIndex" @change="changeTab">
		</u-tabs> -->
		<view class="tabs_box dis">
			<!-- 全部 -->
			<view class="complain_box" v-for="(item,index) in orderlist" :key="index" @click="bindonline(item)">
				<view class="complain_part1">
					<view class="part1_left">{{item.illegal}}</view>
					<!-- <view class="part1_left" v-if="item.complaintType=='2'">拒绝系统推单</view> -->
					<!-- <view class="part1_left" v-if="item.complaintType=='3'">残损违规</view> -->
					<view class="part1_right">扣款{{item.deductMoney}}元</view>
				</view>
				<view class="complain_part2" v-if="item.shopAddressDetail">
					<image src="../static/tousu/black.png"></image>
					<text>{{item.shopAddressDetail}}</text>
				</view>
				<view class="complain_part2" v-if="item.userAddressDetail">
					<image src="../static/tousu/orange.png"></image>
					<text>{{item.userAddressDetail}}</text>
				</view>
				<u-line color="#E6E6E6" />
				<view class="complain_title">
					<span v-if="item.complaintState=='1'">投诉成功</span>
					<!-- <span v-if="item.complaintState=='2'">申诉中</span>
					<span v-if="item.complaintState=='3'">申诉未通过</span>
					<span v-if="item.complaintState=='4'">申诉通过</span> -->
					<span v-if="item.complaintState=='5'">投诉审核中</span>
					<span v-if="item.complaintState=='6'">投诉未通过</span>
				</view>
			</view>

			<!-- <view class="empty" v-if="orderlist.length == 0">
				<view
					style="display: block; width: 90%; margin: 0 auto; position: fixed;top: 35%;left: 0rpx;right: 0rpx;text-align: center;">
					<image src="../../../static/image/empty.png" style="width: 300rpx;height: 300rpx;"></image>
					<view style="color: #CCCCCC;">暂无内容</view>
				</view>
			</view> -->
			<empty v-if="!orderlist.length" style="z-index:0;position: relative;top: -20px;"></empty>
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
				isShow: false,
				page: 1,
				limit: 10,
				complaintType: null,
				complaintState: '',
				listTab: [{
					name: '全部'
				}, {
					name: '可申诉'
				}, {
					name: '申诉中'
				}, {
					name: '申诉未通过'
				}, {
					name: '申诉通过'
				}],
				currentIndex: 0,
				list: [{
					id: '',
					illegal: '全部'
				}],
				current: 0,
				orderlist: [],
				totalCount: 0,
				illegalId: ''
			}
		},
		mounted() {

		},
		onLoad() {
			// this.getTypeList()
			this.bindorder()
		},
		methods: {
			getTypeList() {
				this.$Request.getT('/app/illegalType/selectIllegalTypeList').then(res => {
					if (res.code == 0) {
						this.list = [...this.list, ...res.data]
					}
				});
			},
			bindlist(index) {
				console.log(index)

				this.current = index;
				this.isShow = !this.isShow
			},
			// 获取全部数据
			bindorder() {
				this.$Request.getT('/app/tbindent/selectComplaint', {
					page: this.page,
					limit: this.limit,
					// shopId:uni.getStorageSync('shopId')
					// complaintState: this.complaintState,
					// illegalId: this.illegalId
				}).then(res => {
					if (res.code == 0) {
						this.totalCount = res.data.totalCount
						if (this.page == 1) {
							this.orderlist = res.data.list
						} else {
							this.orderlist = this.list_box.concat(res.data.list)
						}
					} else {
						console.log('失败：', res.data)
					}
				});
			},
			change(index) {
				console.log(index)
				this.illegalId = this.list[index].id
				this.orderlist = []
				this.current = index;
				this.currentIndex = 0
				this.page = 1
				
				this.complaintState = ''
				this.bindorder()
			},
			changeTab(index) {
				this.orderlist = []
				this.currentIndex = index
				this.page = 1
				if (index == 0) {
					this.complaintState = ''
				} else {
					this.complaintState = index
				}
				this.bindorder()
			},
			bindonline(item) {
				// if(item.complaintState == 1 || item.complaintState == 4) {
				uni.navigateTo({
					url: '/pages/riderMy/myComplain/online_complain/online_complain?indentNumber=' + item
						.indentNumber + '&complaintId=' + item.complaintId
				})
				// }
			},
			bindshow() {
				this.isShow = !this.isShow
			},
		},
		// 上拉加载
		onReachBottom: function() {
			if (this.page < this.totalCount) {
				this.page = this.page + 1;
			} else {
				uni.showToast({
					title: '已经最后一页啦',
					icon: 'none'
				})
			}
			this.bindorder();
		}
	}
</script>

<style>
	body {
		background-color: #F5F5F5;
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

	.u-tab-item {
		font-weight: 400 !important;
		color: #000000 !important;
		font-size: 24rpx !important;
	}

	.tabs_box {
		/* display: none; */
		/* position: absolute; */
		/* top: 144rpx; */
	}

	.dis {
		/* display: block; */
		/* width: 100%; */
		/* position: absolute; */
		/* top: 100rpx; */
	}

	.content {
		width: 100%;
		position: relative;
	}

	.complain_cont {
		width: 100%;
		position: relative;
		/* display: flex; */
	}

	.complain_tabs {
		width: 100%;
	}

	.complain_btn {
		width: 15%;
		background: #FFFFFF;
		box-shadow: -2rpx 1rpx 3rpx 0rpx rgba(39, 39, 39, 0.11);
		height: 88rpx;
		position: absolute;
		top: 0rpx;
		right: 0rpx;
		z-index: 10075;
	}

	.btn {
		color: #999999;
		font-size: 25rpx;
		letter-spacing: 2rpx;
		text-align: center;
		line-height: 88rpx;
	}

	.complain_none {
		width: 15%;
		background: #FFFFFF;
		box-shadow: -2rpx 1rpx 3rpx 0rpx rgba(39, 39, 39, 0.11);
		height: 88rpx;
		position: absolute;
		top: 88rpx;
		right: 0rpx;

	}

	.popup_list {
		width: 97%;
		margin: 0 auto;
		position: relative;
		top: 90rpx;
	}

	.list_tabs {
		width: 90%;
		height: auto;
		display: flex;
		justify-content: start;
		flex-wrap: wrap;
	}

	.tabs {
		border: 1rpx solid #cccccc;
		padding: 0rpx 25rpx;
		line-height: 50rpx;
		margin: 10rpx 10rpx;
	}

	/* 全部 */
	.complain_box {
		width: 90%;
		margin: 0 auto;
		/* height: 300rpx; */
		background: #ffffff;
		margin-top: 30rpx;
		border-radius: 17rpx;
	}

	.complain_part1 {
		width: 90%;
		margin: 0 auto;
		display: flex;
		/* padding-top: 20rpx; */
	}

	.part1_left {
		flex: 1;
		font-size: 26rpx;
		font-weight: bold;
		letter-spacing: 2rpx;
		height: 80rpx;
		justify-content: left;
		align-items: center;
		display: flex;
	}

	.part1_right {
		flex: 1;
		color: #FF1B1B;
		display: flex;
		justify-content: flex-end;
		align-items: center;
	}

	.complain_part2 {
		width: 90%;
		margin: 0 auto;
		height: 50rpx;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.complain_part2 image {
		width: 15rpx;
		height: 15rpx;
		margin-right: 20rpx;
	}

	.complain_part2 text {
		color: #999999;
		font-size: 24rpx;
	}

	.u-line {
		border-bottom-width: 3px !important;
		margin-top: 20rpx !important;
	}

	.complain_title {
		width: 90%;
		margin: 0 auto;
		height: 80rpx;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		color: #FF2727;
		font-size: 27rpx;
		font-weight: bold;
		letter-spacing: 2rpx;
	}
</style>
