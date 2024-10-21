<template>
	<view style="line-height: 26px;padding: 32upx;" class="home1">
		<!-- <view v-html="tit"> </view> -->
		<!-- <view style="font-size: 28upx;" v-html="content"></view> -->
		<u-parse :html="content"></u-parse>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				content: ''
			}
		},
		onLoad(e) {
			this.taskData(e.id)
		},
		methods: {
			// 获取任务数据
			taskData(e) {
				this.$Request.getT('/app/userinfo/TrainingCenter?id='+e).then(res => {
					if(res.code==0){
						this.content = res.data.message
					}
				});
			},
		}
	}
</script>

<style>
	page {
		background: #FFFFFF;
	}

	.content {
		width: 100%;
		background: #FFFFFF;
		margin-top: 20rpx;
		padding-bottom: 50rpx;
		padding-top: 20rpx;
	}

	.list_box {
		width: 90%;
		margin: 0 auto;
		display: flex;
		line-height: 45rpx;
		padding-top: 20rpx;
	}

	.list_left {
		flex: 2;
	}

	.name {
		font-size: 26rpx;
		color: #333333;
		font-weight: bold;
		letter-spacing: 2rpx;
	}

	.data {
		color: #999999;
		font-size: 24rpx;
	}

	/* .list_right {
		flex: 1;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		font-size: 31rpx;
	} */
</style>
