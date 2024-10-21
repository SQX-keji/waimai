<template>
	<view class="content">
		<view class="online_box">
			<view class="part1">
				<view class="online_left">投诉类型</view>
				<view class="online_right" @click="show = true">
					{{complaintName?complaintName:'请选择投诉类型'}}
					<image src="../../../static/images/index/right.png"></image>
				</view>
			</view>
			<u-line color="#E6E6E6" />
			<view class="online">
				<view class="tit">投诉理由</view>
				<view class="text_box" style="margin-top: 20rpx;">
					<u-input v-model="value" height="300" :type="type" :border="border" :clearable="false"
						placeholder="请描述问题发生的情况" />
				</view>
			</view>
		</view>
		<view class="btn" @click="bindorder">立即提交</view>

		<u-picker v-model="show" mode="selector" :range="typeList" range-key="illegal" @confirm='select'></u-picker>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				show: false,
				value: '',
				type: 'textarea',
				border: true,
				indentNumber: '',
				complaintName: '',
				typeList: [],
				IllegalId: '',
				indentType: ''
			}
		},
		onLoad(options) {
			console.log(options)
			this.indentNumber = options.indentNumber
			this.indentType = options.indentType
			this.getTypeList()
		},
		methods: {
			select(e) {
				console.log(e)
				this.complaintName = this.typeList[e].illegal
				this.IllegalId = this.typeList[e].id

			},
			getTypeList() {
				this.$Request.get('/shop/illegalType/selectIllegalTypeList').then(res => {
					if (res.code == 0) {
						this.typeList = res.data
					}
				});
			},
			bindorder() {
				if (this.IllegalId == '') {
					uni.showToast({
						title: '请选择投诉类型',
						icon: 'none'
					})
					return
				}
				if (this.value == '') {
					uni.showToast({
						title: '请填写投诉原因',
						icon: 'none'
					})
					return
				}
				this.$Request.postJson('/app/tbindent/insertComplaint', {
					indentNumber: this.indentNumber,
					wrongExplain: this.value,
					illegalId: this.IllegalId,
					indentType: '5',
					type: '1',
					userId: uni.getStorageSync('userId')
				}).then(res => {
					if (res.code == 0) {
						uni.showToast({
							title: '提交成功',
							icon: 'none'
						})
						setTimeout(function() {
							uni.navigateBack()
						}, 1000)
					} else {
						console.log('失败：', res.data)
					}

				});
			}
		}
	}
</script>

<style>
	body {
		background-color: #F5F5F5;
	}

	.content {
		width: 100%;
	}

	.online_box {
		width: 90%;
		margin: 0 auto;
		background: #FFFFFF;
		border-radius: 20rpx;
		margin-top: 30rpx;
		padding-bottom: 40rpx;
	}

	.part1 {
		width: 90%;
		margin: 0 auto;
		height: 80rpx;
		display: flex;
		align-items: center;
	}

	.online_left {
		flex: 1;
		font-size: 27rpx;
		font-weight: bold;
		letter-spacing: 2rpx;
	}

	.online_right {
		color: #999999;
		font-size: 22rpx;
		flex: 1;
		display: flex;
		justify-content: flex-end;
		align-items: center;
	}

	.online_right image {
		width: 12rpx;
		height: 21rpx;
		margin-left: 10rpx;
	}

	.online_title {
		font-size: 28rpx;
		font-weight: bold;
		letter-spacing: 2rpx;
		width: 90%;
		margin: 0 auto;
		line-height: 80rpx;
	}

	.online {
		width: 90%;
		margin: 0 auto;
		padding-bottom: 34rpx;
	}

	.tit {
		font-size: 27rpx;
		font-weight: bold;
		letter-spacing: 2rpx;
		margin-top: 19rpx;
	}

	.u-input--border {
		border: none !important;
		background: #F5F5F5 !important;
	}

	.btn {
		width: 90%;
		margin: 0 auto;
		background: #FF7F00;
		line-height: 90rpx;
		text-align: center;
		color: white;
		border-radius: 15rpx;
		margin-top: 20rpx;
		font-size: 28rpx;
	}
</style>
