<template>
	<view>
		<view class="hehuo_view">
			<image src="../../static/images/my/hezuobg.png"></image>
			<view class="text_view">
				<view class="item_view">
					<view class="item_title">意向代理城市</view>
					<input @click="goCity" type="text" disabled v-model="city" placeholder="请输入代理城市" />
					<view class="xian"></view>
				</view>

				<view class="item_view">
					<view class="item_title">姓名</view>
					<input type="text" v-model="userName" placeholder="请输入姓名" />
					<view class="xian"></view>
				</view>
				<view class="item_view">
					<view class="item_title">联系电话</view>
					<input type="number" v-model="phone" maxlength="11" placeholder="请输入联系电话" />
					<view class="xian"></view>
				</view>
				<view class="item_view">
					<view class="item_title">年龄</view>
					<input type="number" v-model="age" maxlength="11" placeholder="请输入年龄" />
					<view class="xian"></view>
				</view>
				<view class="item_view">
					<view class="item_title">头像上传</view>
					<view class="flex" style="overflow: hidden;flex-direction: initial;">
						<view v-if="headImg.length">
							<view class="margin-top flex margin-right-sm">
								<view class="flex"
									style="width: 150upx;height: 150upx;margin-right: 10rpx;position: relative;">
									<image :src="headImg" style="width: 100%;height: 100%;"></image>
									<view style="z-index: 9;position: absolute;top: -15rpx;right: -15rpx;"
										@click="headImgremove(index)">
										<u-icon name="close-circle-fill" color="#2979ff" size="50rpx"></u-icon>
									</view>
								</view>
							</view>
						</view>
						<view class="margin-top" @click="addImage()" v-if="headImg.length<=0">
							<view style="width: 150upx;height: 150upx;background: #F5F5F5;"
								class="flex justify-center align-center">
								<view>
									<view class="text-center">
										<image src="../../static/images/my/add.png"
											style="width: 54upx;height: 47upx;position: relative;">
										</image>
									</view>
									<view class="text-center text-xs margin-top-xs">上传图片</view>
								</view>
							</view>
						</view>
					</view>
				</view>

				<!-- <view class="audit_message" v-if="auditContent != '' && bb == 3">拒绝原因：{{auditContent}}</view> -->
				<view class="save_btn" @tap="save" v-if="bb !=0">提交申请</view>
				<!-- <view class="save_btn" v-if="status == 0">审核中</view> -->
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				hotCitys: ['杭州', '天津', '北京', '上海', '深圳', '广州', '成都', '重庆', '厦门'],
				locationValue: '正在定位...',
				// auditContent: '',
				city: '',
				money: '',
				teamNumber: '',
				userName: '',
				phone: '',
				age: '',
				headImg: [],
				bb:true,
			}
		},
		onLoad() {
			this.getChannel();
		},
		methods: {
			// 头像删除
			headImgremove(index) {
				this.headImg = ''
			},
			getChannel() {
				let userId = this.$queue.getData('userId');
				this.$Request.getT('/app/artificer/selectAgencyById?userId=' + userId).then(res => {
					if (res.code == 0) {
						if (res.data == null) {
							this.bb = 1;
						} else {
							this.bb = res.data.status;
							this.city = res.data.city;
							this.age = res.data.age;
							this.headImg = res.data.img;
							this.userName = res.data.name;
							this.phone = res.data.phone;
						}
						console.log(this.bb)
						// this.auditContent = res.data.auditContent;
					}
				});
			},
			//获取省市区
			Getcity(latitude, longitude) {
				this.$Request.get("/app/Login/selectCity", {
					lat: latitude,
					lng: longitude
				}).then(res => {
					console.log(res)
					this.city = res.data.city
					console.log(this.address)
				});
			},
			goCity() {
				let that = this
				uni.chooseLocation({
					success: function(res) {
						console.log('位置名称：' + res.name);
						console.log('详细地址：' + res.address);
						console.log('纬度：' + res.latitude);
						console.log('经度：' + res.longitude);
						// that.city = res.address || '郑州'
						that.Getcity(res.latitude, res.longitude)
					}
				});
				// uni.getLocation({
				// 	type: 'gcj02',
				// 	geocode: true,
				// 	success: function(res) {
				// 		console.log('当前位置：' + res.address.city);
				// 		that.city = res.address.city || '郑州'
				// 	}
				// });
			},
			save() {
				// let isStudent = this.$queue.getData("isStudent");
				// if (isStudent != 2) {
				// 	uni.showModal({
				// 		title: '温馨提示',
				// 		content: '您还没有进行实名认证，请认证完成之后再来操作吧！',
				// 		showCancel: true,
				// 		cancelText: '取消',
				// 		confirmText: '确认',
				// 		success: res => {
				// 			if (res.confirm) {
				// 				uni.navigateTo({
				// 					url: '/offlinetask/pages/public/authentication'
				// 				});
				// 			}
				// 		}
				// 	});
				// 	return;
				// }
				// this.form.headImg = this.headImg
				// this.headImg = this.headImg.toString();
				if (this.city === '') {
					this.$queue.showToast('请输入代理城市')
					return;
				}
				if (this.userName === '') {
					this.$queue.showToast('请输入姓名')
					return;
				}
				if (this.phone === '' || this.phone.length != 11) {
					this.$queue.showToast('请输入正确的手机号！')
					return;
				}
				if (this.age === '') {
					this.$queue.showToast('请输入年龄')
					return;
				}
				if (this.headImg == '') {
					this.$queue.showToast('请上传头像')
					return;
				}
				let userId = this.$queue.getData('userId');
				let data = {
					userId: userId,
					name: this.userName,
					phone: this.phone,
					age: this.age,
					city: this.city,
					img: this.headImg,
				}
				this.$Request.postJson('/app/artificer/insertAgency', data).then(res => {
					if (res.code == 0) {
						uni.hideLoading();
						this.$queue.showToast('提交成功！');
						setTimeout(d => {
							uni.navigateBack();
						}, 1000);
					} else {
						uni.hideLoading();
						this.$queue.showToast(res.msg);
					}
				});
			},
			addImage() {
				let that = this
				uni.chooseImage({
					count: 1,
					sourceType: ['album', 'camera'],
					success: res => {
						for (let i = 0; i < 1; i++) {
							that.$queue.showLoading("上传中...");
							uni.uploadFile({ // 上传接口
								url: that.config("APIHOST1") + '/alioss/upload', //真实的接口地址
								// url: 'https://anmo.xianmxkj.com/sqx_fast/alioss/upload',
								filePath: res.tempFilePaths[i],
								name: 'file',
								success: (uploadFileRes) => {
									console.log(uploadFileRes.data)
									that.headImg = JSON.parse(uploadFileRes.data).data
									console.log(that.headImg)
									uni.hideLoading();
								}
							});
						}
					}
				})
			},
		}
	}
</script>

<style lang="less">
	// @import '../../static/less/index.less';
	// @import '../../static/css/index.css';

	.hehuo_view {
		width: 750rpx;
		height: 1830upx;

		image {
			width: 750rpx;
			height: 1830upx;
			background-size: 100%;
			position: absolute;
		}

		.text_view {
			position: absolute;
			z-index: 1;
			width: 84%;
			margin: 660rpx 50rpx 30rpx;

			.audit_message {
				color: red;
				width: 650rpx;
				height: 50rpx;
				margin-top: 50rpx;
			}

			.save_btn {
				width: 650rpx;
				height: 88rpx;
				background: #FFFFFF;
				border-radius: 10rpx;

				text-align: center;
				line-height: 88rpx;
				/* #ifdef MP-WEIXIN */
				margin-top: 150rpx;
				/* #endif */
				/* #ifdef H5 */
				margin-top: 100rpx;
				/* #endif */
				/* #ifdef APP-PLUS */
				margin-top: 180rpx;
				/* #endif */
			}

			.save_btn1 {
				width: 650rpx;
				height: 88rpx;
				background: #FFFFFF;
				border-radius: 10rpx;
				margin-top: 100rpx;
				text-align: center;
				line-height: 88rpx;
			}

			.item_view {
				margin-top: 30rpx;

				.item_title {
					font-size: 28rpx;
					font-family: PingFang SC Heavy, PingFang SC Heavy-Heavy;
					font-weight: 800;
					color: #333333;
				}

				input {
					margin-top: 20rpx;
					height: 40rpx;
					font-size: 24rpx;
					font-family: PingFang SC Regular, PingFang SC Regular-Regular;
					font-weight: 400;
					color: #333333;
				}

				.xian {
					width: 630rpx;
					height: 1rpx;
					border: 1rpx solid #77D7B0;
					margin-top: 10rpx;
				}
			}
		}
	}
</style>
