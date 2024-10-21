<template>
	<view class="">

		<view v-if="chatList.length" class="content ">
			<view class="radius padding-lr-sm bg" style="margin-top: 4rpx;" @click="goIM(item)"
				v-for="(item,index) in chatList" :key='index' v-if="item.content">
				<view class="flex padding-tb " v-if="userId == item.userId">
					<view>
						<u-image shape="circle" width='80rpx' height="80rpx" :src="item.shopCover"></u-image>
					</view>
					<view class="flex-sub margin-left-sm">
						<view class="flex justify-between">
							<view style="width: 50%;">{{item.shopName}}</view>
							<view class="text-grey">{{item.createTime}}</view>
						</view>
						<view class="flex justify-between">
							<view class="text-grey">{{ item.messageType == 1? item.content : '[图片]'}}</view>
							<view v-if="item.userUnread"
								style="height: 32rpx;width: 32rpx;border-radius: 100rpx;background-color: red;color: #FFF;text-align: center;">
								{{item.userUnread}}
							</view>
						</view>
					</view>
				</view>
				<view class="flex padding-tb" v-else>
					<view>
						<u-image shape="circle" width='80rpx' height="80rpx" :src="item.shopCover"></u-image>
					</view>
					<view class="flex-sub margin-left-sm">
						<view class="flex justify-between">
							<view style="width: 50%;">{{item.shopName}}</view>
							<view class="text-grey">{{item.createTime}}</view>
						</view>
						<view class="flex justify-between">
							<view class="text-grey">{{ item.messageType == 1? item.content : '[图片]'}}</view>
							<view v-if="item.userUnread"
								style="height: 32rpx;width: 32rpx;border-radius: 100rpx;background-color: red;color: #FFF;text-align: center;">
								{{item.userUnread}}
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>

		<empty v-if="!chatList.length && !msgList.length" content='暂无消息'></empty>
	</view>
</template>

<script>
	import empty from '../../components/empty.vue'
	export default {
		components: {
			empty
		},
		data() {
			return {
				page: 1,
				limit: 20,
				chatList: [],
				userId: '',
				msgList: [],
				time: '',
				count: 0,
				messageCount: 0
			}
		},
		onLoad() {
			this.getChatList()
			let that = this
			if (that.userId) {
				that.time = setInterval(function() {
					that.getChatList()
					// that.getMsgList()
					that.$nextTick(function() {
						that.messageCount = uni.getStorageSync('messageCount')
					})
				}, 3000)
			} else {
				that.chatList = []
				that.msgList = []
			}
		},
		onShow() {
			let that = this
			that.userId = uni.getStorageSync('userId')
			if (that.userId) {
				that.page = 1;
				// that.chatList = []
				that.msgList = []
				that.getChatList();
			} else {
				that.chatList = []
				that.msgList = []
			}
		},
		onHide() {
			clearInterval(this.time)
		},
		methods: {
			getChatList() {
				this.$Request.get("/app/ordersChat/selectOrdersChatPageUser", {
					page: this.page,
					limit: this.limit,
				}).then(res => {
					if (res.code == 0) {
						if (this.page == 1) {
							this.chatList = [];
						}
						res.data.list.forEach(d => {
							this.chatList.push(d);
						});
						this.count = res.data.totalCount;
						// this.chatList = res.data.list
					}
				});
			},
			// getMsgList() {
			// 	this.$Request.get("/app/message/selectMessageByUserIdLimit1").then(res => {
			// 		if (res.code == 0) {
			// 			this.msgList = res.data.list
			// 		}
			// 	});
			// },
			goIM(e) {
				let userId = this.$queue.getData('userId');
				if (e.userId == userId) {
					userId = e.byUserId
				} else {
					userId = e.userId
				}
				uni.navigateTo({
					url: '/pages/index/shop/im?ordersId=' + e.ordersId
				})
				// uni.navigateTo({
				// 	url: '/pages/msg/im?chatConversationId=' + e.chatConversationId + '&byUserId=' + userId
				// })
			},
			goMsg() {
				uni.navigateTo({
					url: '/pages/msg/message'
				})
			}
		},
		onReachBottom: function() {
			if (this.chatList.length == this.count) {
				uni.showToast({
					title: '已经到底了',
					icon: 'none'
				})
			} else {
				this.page = this.page + 1;
				this.getChatList();
			}
		},
	}
</script>

<style>
	page {
		background-color: #F7F7F7;
	}

	.bg {
		background: #FFFFFF;
	}
</style>
