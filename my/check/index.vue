<template>
	<view class="">
		<!-- <view class="text-center text-white bg-red">18岁以上方可认证使用</view> -->
		<view class="margin padding-lr bg-white" style="border-radius: 16rpx;">
			<view class="flex justify-between align-center" style="border-bottom: 2rpx solid #e6e6e6;color: #1a1a1a;">
				<view class="padding-tb text-lg text-bold ">申请信息</view>
				<view class="text-df" style="color: #666666;">注：18岁以上方可认证使用</view>
			</view>
			<view class="flex align-center padding-tb">
				<view class="flex-sub text-df text-bold" style="color: #1a1a1a;">你的生日</view>
				<view class="flex align-center">
					<!-- <view class="margin-right-xs text-gray">2020年4月2日</view> -->
					<input @click="isShow" v-model="birthday" class="margin-right-xs" disabled placeholder="请填写你的生日"
						style="text-align: right;" type="text">
					<!-- <image src="../../static/images/orderReceiving/right.png" style="width: 16rpx;height: 26rpx;"></image> -->
				</view>
			</view>
			<view class="flex align-center padding-tb">
				<view class="flex-sub text-df text-bold" style="color: #1a1a1a;">你的性别</view>
				<view class="flex align-center">
					<u-radio-group v-model="sex">
						<u-radio v-for="(item,index) in gender" :key='index' :name="item.name">{{item.name}}</u-radio>
					</u-radio-group>
				</view>
			</view>
			<view class="flex align-center padding-tb">
				<view class="flex-sub text-df text-bold" style="color: #1a1a1a;">你的身高(CM)</view>
				<view class="flex align-center">
					<!-- <view class="margin-right-xs text-gray">170cm</view> -->
					<input class="margin-right-xs" v-model="height" placeholder="请填写你的身高" style="text-align: right;"
						type="text">
					<!-- <image src="../../static/images/orderReceiving/right.png" style="width: 16rpx;height: 26rpx;"></image> -->
				</view>
			</view>
			<view class="flex align-center padding-tb">
				<view class="flex-sub text-df text-bold" style="color: #1a1a1a;">你的体重(KG)</view>
				<view class="flex align-center">
					<!-- <view class="margin-right-xs text-gray">50kg</view> -->
					<input class="margin-right-xs" v-model="weight" placeholder="请填写你的体重" style="text-align: right;"
						type="text">
					<!-- <image src="../../static/images/orderReceiving/right.png" style="width: 16rpx;height: 26rpx;"></image> -->
				</view>
			</view>
			<view class="flex align-center padding-tb">
				<view class="flex-sub text-df text-bold" style="color: #1a1a1a;">你的职业</view>
				<view class="flex align-center">
					<!-- <view class="margin-right-xs text-gray">设计</view> -->
					<input class="margin-right-xs" v-model="occupation" placeholder="请填写你的职业" style="text-align: right;"
						type="text">
					<!-- <image src="../../static/images/orderReceiving/right.png" style="width: 16rpx;height: 26rpx;"></image> -->
				</view>
			</view>
		</view>

		<view class="margin padding bg-white" style="border-radius: 16rpx;">
			<view class="flex align-center padding-tb">
				<view class="flex-sub text-df text-bold" style="color: #1a1a1a;">真实姓名</view>
				<view class="flex align-center">
					<!-- <view class="margin-right-xs text-gray">设计</view> -->
					<input class="margin-right-xs" v-model="realName" placeholder="请填写你的真实姓名" style="text-align: right;"
						type="text">
					<!-- <image src="../../static/images/orderReceiving/right.png" style="width: 16rpx;height: 26rpx;"></image> -->
				</view>
			</view>
			<view class="flex align-center padding-tb">
				<view class="flex-sub text-df text-bold" style="color: #1a1a1a;">身份证号 <text class="text-red"> *</text>
				</view>
				<view class="flex align-center">
					<!-- <view class="margin-right-xs text-gray">设计</view> -->
					<input class="margin-right-xs" v-model="identityCardNum" placeholder="请填写你的身份证号"
						style="text-align: right;" type="text">
					<!-- <image src="../../static/images/orderReceiving/right.png" style="width: 16rpx;height: 26rpx;"></image> -->
				</view>
			</view>
			<view class="text-lg text-bold text-black margin-bottom-sm">上传身份证正面</view>
			<view class="margin-top"
				style="border: 2rpx dashed #484B74; width: 100%;height: 320rpx;position: relative;">
				<view style="text-align: center;margin: 80rpx auto 0;" @click="addIDCard(1)" v-if="!identityCardFront">
					<image src="../../static/images/index/add.png" mode="widthFix" style="width: 73rpx;"></image>
					<view class="text-sm text-gray margin-top-sm">添加身份证正面</view>
				</view>
				<image @click="addIDCard(1)" v-else :src="identityCardFront" style="width: 100%;height: 320rpx;">
				</image>
			</view>
			<view class="text-lg text-bold text-black margin-tb-sm">上传身份证反面</view>
			<view class="margin-top"
				style="border: 2rpx dashed #484B74; width: 100%;height: 320rpx;position: relative;">
				<view style="text-align: center;margin: 80rpx auto 0;" @click="addIDCard(2)" v-if="!identityCardRear">
					<image src="../../static/images/index/add.png" mode="widthFix" style="width: 73rpx;"></image>
					<view class="text-sm text-gray margin-top-sm">添加身份证反面</view>
				</view>
				<image @click="addIDCard(2)" v-else :src="identityCardRear" style="width: 100%;height: 320rpx;"></image>
			</view>
		</view>

		<view class="margin padding bg-white" style="border-radius: 16rpx;">
			<view class="text-lg text-bold text-black margin-bottom-sm">个人简介</view>
			<textarea class="radius" v-model="individualResume"
				style=" width: 100%; height: 150rpx;background: #f5f5f5;padding: 15rpx;"
				placeholder="请输入个人简介"></textarea>
			<view class="text-lg text-bold text-black margin-tb-sm">图片上传</view>

			<view class="flex flex-wrap">
				<view class="flex " v-if="infantImgs.length"
					style="width: 200rpx;height: 200rpx;margin-right: 10rpx;position: relative;margin-bottom: 10rpx;"
					v-for="(image,index) in infantImgs" :key="index">
					<image :src="image" class="radius" style="width: 100%;height: 100%;" @click="previewImg(index)">
					</image>
					<view style="z-index: 9;position: absolute;top: -15rpx;right: -15rpx;"
						@click="infantImgremove(index)">
						<u-icon name="close-circle-fill" color="red" size="50rpx"></u-icon>
					</view>
				</view>
				<view v-if="infantImgs.length<9" style="width: 200rpx;height: 200rpx;background: #f4f5f6;"
					class="flex justify-center align-center radius" @click="addImages()">
					<view>
						<view class="text-center">
							<image src="../../static/images/index/add.png" style="width: 65rpx;height: 55rpx;">
							</image>
						</view>
						<view class="text-center">添加图片</view>
					</view>
				</view>
			</view>
			<view class="text-lg text-bold text-black margin-tb-sm">视频上传</view>
			<view>
				<view v-if="!video" style="width: 200rpx;height: 200rpx;background: #f4f5f6;"
					class="flex justify-center align-center radius" @click="addVideo()">
					<view>
						<view class="text-center">
							<image src="../../static/images/index/add.png" style="width: 65rpx;height: 55rpx;">
							</image>
						</view>
						<view class="text-center">添加视频</view>
					</view>
				</view>

				<view class="flex " v-if="video" style="width: 100%;position: relative;">
					<video :src="video" controls></video>
					<view style="z-index: 9;position: absolute;top: -15rpx;right: -15rpx;" @click="removeVidoe()">
						<u-icon name="close-circle-fill" color="red" size="50rpx"></u-icon>
					</view>
				</view>
			</view>
			<!-- </view> -->

		</view>
		<view class="margin">
			<view class="text-center text-lg radius text-bold"
				style="width: 100%;height: 78rpx;line-height: 78rpx;background: #7E59FF;color: #FFF;"
				@click="fabuBtn()">
				{{btnName}}
			</view>
		</view>

		<u-picker @confirm="timeAction" v-model="show" :params="params" mode="time"></u-picker>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				action: 'https://newxxpw.xianmxkj.com/sqx_fast/alioss/upload',
				fileList: [],
				show: false,
				params: {
					year: true,
					month: true,
					day: true,
				},
				birthday: '', //生日
				height: '', //身高
				weight: '', //体重
				occupation: '', //职业
				individualResume: '', //简介
				pictureList: [],
				realName: '', //姓名
				identityCardNum: '', //身份证号
				identityCardFront: '', //身份证正面
				identityCardRear: '', //身份证反面
				gender: [{
						name: '男',
						checked: true
					},
					{
						name: '女',
						checked: false
					},
				],
				sex: '男',
				userId: '',
				btnName: '提交申请',
				video: "",
				isFinish: false,
				infantImgs: [],
			}
		},
		onLoad(option) {
			this.userId = option.userId ? option.userId : ''
			if (this.userId) {

				// this.getUserInfo()
			}
			uni.setNavigationBarTitle({
				title: "修改个人信息"
			})
			this.btnName = '提交修改'
			this.getPWInfo()
		},
		methods: {
			// 上传视频
			addVideo() {
				uni.chooseVideo({
					count: 1,
					sourceType: ['album', 'camera'],
					success: res => {
						this.$queue.showLoading("上传中...");

						console.log(res.tempFilePath)
						uni.uploadFile({ // 上传接口
							// url: this.config("APIHOST1") + '/alioss/upload', //真实的接口地址
							url: 'https://newxxpw.xianmxkj.com/sqx_fast/alioss/upload',
							filePath: res.tempFilePath,
							name: 'file',
							timeout: '30000',
							success: (uploadFileRes) => {
								// console.log(JSON.parse(uploadFileRes.data))
								this.video = JSON.parse(uploadFileRes.data).data
								console.log(this.video)
								uni.hideLoading();
							}
						});
					}
				})
			},
			// 删除视频
			removeVidoe() {
				this.video = ''
			},
			addIDCard(e) {
				uni.chooseImage({
					count: 1,
					sourceType: ['album', 'camera'],
					success: res => {
						for (let i = 0; i < 1; i++) {
							this.$queue.showLoading("上传中...");
							uni.uploadFile({ // 上传接口
								// url: this.config("APIHOST1") + '/alioss/upload', //真实的接口地址
								url: 'https://newxxpw.xianmxkj.com/sqx_fast/alioss/upload',
								filePath: res.tempFilePaths[i],
								name: 'file',
								success: (uploadFileRes) => {
									if (e == 1) {
										this.identityCardFront = JSON.parse(uploadFileRes.data)
											.data
									} else {
										this.identityCardRear = JSON.parse(uploadFileRes.data).data
									}
									uni.hideLoading();
								}
							});
						}
					}
				})
			},
			// 上传照片
			addImages(e) {
				uni.chooseImage({
					count: 9,
					sourceType: ['album', 'camera'],
					success: res => {
						console.log(res.tempFilePaths)
						for (let i = 0; i < res.tempFilePaths.length; i++) {
							this.$queue.showLoading("上传中...");
							uni.uploadFile({ // 上传接口
								// url: this.config("APIHOST1") + '/alioss/upload', //真实的接口地址
								url: 'https://newxxpw.xianmxkj.com/sqx_fast/alioss/upload',
								filePath: res.tempFilePaths[i],
								name: 'file',
								success: (uploadFileRes) => {
									if (this.infantImgs.length < 9) {
										this.infantImgs.push(JSON.parse(uploadFileRes.data).data)
									}
									uni.hideLoading();
								}
							});
						}
					}
				})
			},
			// 删除照片
			infantImgremove(index) {
				this.infantImgs.splice(index, 1)
			},
			// 预览照片
			previewImg(index) {
				let _this = this;
				uni.previewImage({
					current: index,
					urls: _this.infantImgs
				});
			},
			getPWInfo() {
				this.$Request.getT('/app/usermessage/findPwUserMessage').then(res => {
					if (res.code == 0) {
						// if (res.data.auditStatus == 0) {
						this.birthday = res.data.birthday //生日
						this.height = res.data.height //身高
						this.weight = res.data.weight //体重
						this.sex = res.data.sex == 1 ? '男' : '女'
						this.occupation = res.data.occupation //职业
						this.individualResume = res.data.individualResume //简介
						this.realName = res.data.realName //姓名
						this.identityCardNum = res.data.identityCardNum //身份证号
						this.identityCardFront = res.data.identityCardFront //身份证正面
						this.identityCardRear = res.data.identityCardRear //身份证反面
						this.pictureList = res.data.pictureList.split(',')
						this.video = res.data.video
						this.infantImgs = [];
						this.pictureList.forEach(res => {
							this.infantImgs.push(res)
						})
						// }
					}
				})
			},
			getUserInfo() {
				this.$Request.getT('/app/payorder/selectUserMessageById?userId=' + this.userId).then(res => {
					if (res.code == 0) {
						console.log(res.data)
						this.birthday = res.data.birthday //生日
						this.height = res.data.height //身高
						this.weight = res.data.weight //体重
						this.occupation = res.data.occupation //职业
						this.individualResume = res.data.individualResume //简介
						this.realName = res.data.realName //姓名
						this.identityCardNum = res.data.identityCardNum //身份证号
						this.identityCardFront = res.data.identityCardFront //身份证正面
						this.identityCardRear = res.data.identityCardRear //身份证反面
						this.sex = res.data.sex == 1 ? '男' : '女'
						this.pictureList = res.data.pictureList.split(',')
						this.video = res.data.video
						this.infantImgs = [];
						this.pictureList.forEach(res => {
							this.infantImgs.push(res)
						})
					}
				})
			},
			isShow() {
				this.show = true
			},
			timeAction(e) {
				console.log(e)
				this.birthday = e.year + '-' + e.month + '-' + e.day
			},
			// 发布
			fabuBtn() {
				let that = this
				if (!that.birthday) {
					uni.showToast({
						title: '请填写你的生日',
						icon: 'none'
					})
					return
				}
				if (!that.height) {
					uni.showToast({
						title: '请填写你的身高',
						icon: 'none'
					})
					return
				}
				if (!that.weight) {
					uni.showToast({
						title: '请填写你的体重',
						icon: 'none'
					})
					return
				}
				if (!that.occupation) {
					uni.showToast({
						title: '请填写你的职业',
						icon: 'none'
					})
					return
				}
				if (!that.realName) {
					uni.showToast({
						title: '请填写你的真实姓名',
						icon: 'none'
					})
					return
				}

				if (!that.identityCardNum) {
					uni.showToast({
						title: '请填写你的身份证号',
						icon: 'none'
					})
					return
				}
				if (!that.identityCardFront) {
					uni.showToast({
						title: '请上传你的身份证正面',
						icon: 'none'
					})
					return
				}
				if (!that.identityCardRear) {
					uni.showToast({
						title: '请上传你的身份证反面',
						icon: 'none'
					})
					return
				}
				console.log(that.infantImgs, 'lenght', that.infantImgs.length)
				if (that.infantImgs.length < 9) {
					uni.showToast({
						title: '请上传9张你的照片',
						icon: 'none'
					})
					return
				}
				if (!that.video) {
					uni.showToast({
						title: '请上传你的视频',
						icon: 'none'
					})
					return
				}

				console.log(that.isFinish)
				console.log(that.infantImgs)
				// return
				let data = {
					birthday: that.birthday, //生日
					sex: that.sex == '男' ? 1 : 2, //性别
					height: that.height, //身高
					weight: that.weight, //体重
					occupation: that.occupation, //职业
					individualResume: that.individualResume, //简介
					pictureList: that.infantImgs.toString(),
					realName: that.realName,
					identityCardNum: that.identityCardNum,
					identityCardFront: that.identityCardFront,
					identityCardRear: that.identityCardRear,
					video: that.video
				}

				if (that.userId) {
					uni.showModal({
						title: '提示',
						content: '修改资料预计24小时后通过审核,审核期间将不能接单,是否继续？',
						confirmText: '继续',
						success: function(res) {
							if (res.confirm) {
								console.log('用户点击确定');
								that.$Request.postJson('/app/usermessage/updateUserMessage', data).then(
									res => {
										// console.log(that.res)
										if (res.code == 0) {
											uni.showToast({
												title: '提交成功'
											})
											setTimeout(function() {
												uni.switchTab({
													url: '/pages/my/index'
												})
											}, 1000)
										} else {
											uni.showToast({
												title: res.msg,
												icon: 'none'
											})
										}
									});
							} else if (res.cancel) {
								console.log('用户点击取消');
							}
						}
					})
				} else {
					that.$Request.postJson('/app/usermessage/applyPw', data).then(res => {
						console.log(that.res)
						if (res.code == 0) {
							uni.showToast({
								title: '申请成功'
							})
							setTimeout(function() {
								uni.switchTab({
									url: '/pages/my/index'
								})
							}, 1000)
						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					});
				}
			},
		}

	}
</script>

<style>
	page {
		/* background-color: #FFF; */
	}
</style>
