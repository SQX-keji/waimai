<template>
	<view class="padding">
		<view style="padding-bottom: 20upx;color: red;" v-if="auditReason && shop.status == 2">{{auditReason}}</view>
		<view class="text-white padding bg radius">
			<u-form :model="shop" label-position="top">
				<u-form-item label="商铺主营类型">
					<u-input v-model="shop.shopTypeName" placeholder="请输入商铺主营类型" disabled @click="show = true" />
				</u-form-item>
				<u-form-item label="商铺名称">
					<u-input v-model="shop.name" placeholder="请填写 (必填)" />
				</u-form-item>
				<u-form-item label="省市区">
					<u-input v-model="shop.address" disabled placeholder="请填写" @tap="bindOpen" />
				</u-form-item>

				<u-form-item label="商铺详细地址">
					<!-- <u-input v-model="shop.detailedAddress" placeholder="请填写"  /> -->
					<textarea v-model="shop.detailedAddress" placeholder-style="color: #aaaaaa;font-size: 28rpx;" placeholder="请填写商铺详细地址"
						style="height: 120rpx;width: 100%;font-size: 28rpx;" />
				</u-form-item>
			</u-form>
		</view>

		<view class="text-white padding bg radius margin-top">
			<u-form :model="shop" label-position="top">
				<u-form-item label="联系人姓名">
					<u-input v-model="shop.userName" placeholder="请输入真实姓名" />
				</u-form-item>
				<u-form-item label="身份证号">
					<u-input v-model="shop.isNumber" maxlength="20" placeholder="请填写 (必填)" />
				</u-form-item>
				<u-form-item label="联系电话">
					<u-input v-model="shop.phone" type="number" placeholder="请填写 (必填)" />
				</u-form-item>
				<u-form-item label="验证码" v-if="shop.status == null||shop.status == 2">
					<u-input v-model="shop.verCode" type="number" placeholder="请填写 (必填)"
						style="width: 50%;display: inline-block;" />
					<button class="send-msg" @click="sendMsg">{{sendTime}}</button>
				</u-form-item>
			</u-form>
		</view>

		<view class="text-white padding bg radius margin-tb">
			<view>
				<view class="text-lg margin-top-sm text-black">店铺logo</view>
				<view class="flex" style="overflow: hidden;flex-wrap: wrap;">
					<view v-if="detailsImg.length">
						<view class="margin-top flex margin-right-sm flex-wrap">
							<view class="flex"
								style="width: 200rpx;height: 200rpx;margin-right: 2rpx;position: relative;">
								<image :src="detailsImg" style="width: 100%;height: 100%;"></image>
								<view style="z-index: 9;position: absolute;top: -15rpx;right: -15rpx;"
									@click="removeImg(1)">
									<u-icon name="close-circle-fill" color="#2979ff" size="50rpx"></u-icon>
								</view>
							</view>
						</view>
					</view>
					<view class="margin-top" @click="addImages(2)" v-if="detailsImg.length<=0">
						<view style="width: 200rpx;height: 200rpx;background: #f4f5f6;"
							class="flex justify-center align-center">
							<view>
								<view class="text-center">
									<image src="../static/apply/addimg.png" style="width: 65rpx;height: 55rpx;">
									</image>
								</view>
								<view class="text-center text-black">添加图片</view>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view>
				<view class="text-lg margin-top-sm text-black">营业执照</view>
				<view class="flex" style="overflow: hidden;flex-wrap: wrap;">
					<view v-if="shop.business.length">
						<view class="margin-top flex margin-right-sm flex-wrap">
							<view class="flex"
								style="width: 200rpx;height: 200rpx;margin-right: 2rpx;position: relative;">
								<image :src="shop.business" style="width: 100%;height: 100%;"></image>
								<view style="z-index: 9;position: absolute;top: -15rpx;right: -15rpx;"
									@click="removeImgs(1)">
									<u-icon name="close-circle-fill" color="#2979ff" size="50rpx"></u-icon>
								</view>
							</view>
						</view>
					</view>
					<view class="margin-top" @click="addImage(1)" v-if="shop.business.length<=0">
						<view style="width: 200rpx;height: 200rpx;background: #f4f5f6;"
							class="flex justify-center align-center">
							<view>
								<view class="text-center">
									<image src="../static/apply/addimg.png" style="width: 65rpx;height: 55rpx;">
									</image>
								</view>
								<view class="text-center text-black">添加图片</view>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view>
				<view class="text-lg margin-top-sm text-black">身份证正面</view>
				<view class="flex" style="overflow: hidden;flex-wrap: wrap;">
					<view v-if="shop.front.length">
						<view class="margin-top flex margin-right-sm flex-wrap">
							<view class="flex"
								style="width: 200rpx;height: 200rpx;margin-right: 2rpx;position: relative;">
								<image :src="shop.front" style="width: 100%;height: 100%;"></image>
								<view style="z-index: 9;position: absolute;top: -15rpx;right: -15rpx;"
									@click="removeImgs(2)">
									<u-icon name="close-circle-fill" color="#2979ff" size="50rpx"></u-icon>
								</view>

							</view>
						</view>
					</view>
					<view class="margin-top" @click="addImage(2)" v-if="shop.front.length<=0">
						<view style="width: 200rpx;height: 200rpx;background: #f4f5f6;"
							class="flex justify-center align-center">
							<view>
								<view class="text-center">
									<image src="../static/apply/addimg.png" style="width: 65rpx;height: 55rpx;">
									</image>
								</view>
								<view class="text-center text-black">添加图片</view>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view>
				<view class="text-lg margin-top-sm text-black">身份证反面片</view>
				<view class="flex" style="overflow: hidden;flex-wrap: wrap;">
					<view v-if="shop.back.length">
						<view class="margin-top flex margin-right-sm flex-wrap">
							<view class="flex"
								style="width: 200rpx;height: 200rpx;margin-right: 2rpx;position: relative;">
								<image :src="shop.back" style="width: 100%;height: 100%;"></image>
								<view style="z-index: 9;position: absolute;top: -15rpx;right: -15rpx;"
									@click="removeImgs(3)">
									<u-icon name="close-circle-fill" color="#2979ff" size="50rpx"></u-icon>
								</view>

							</view>
						</view>
					</view>
					<view class="margin-top" @click="addImage(3)" v-if="shop.back.length<=0">
						<view style="width: 200rpx;height: 200rpx;background: #f4f5f6;"
							class="flex justify-center align-center">
							<view>
								<view class="text-center">
									<image src="../static/apply/addimg.png" style="width: 65rpx;height: 55rpx;">
									</image>
								</view>
								<view class="text-center text-black">添加图片</view>
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>

		<u-button v-if="shop.status == null || shop.status == 2" @click="submit" class="margin-top"
			:custom-style="customStyle" shape="square" :hair-line="false">提交审核
		</u-button>
		<u-button v-if="shop.status == 0" class="margin-top" disabled :custom-style="customStyle" shape="square"
			:hair-line="false">审核中
		</u-button>
		<!-- 分类列表 -->
		<u-select v-model="show" :list="shopList" valueName='id' labelName="shopTypeName" @confirm="confirm"></u-select>
	</view>
</template>

<script>
	import configdata from '@/common/config.js';
	export default {

		data() {
			return {
				shop: {
					type: '',
					typeid: '',
					name: '',
					address: '',
					shopTypeName: '',
					detailedAddress: '',
					userName: '',
					isNumber: '',
					phone: '',
					verCode: '',
					latitude: '',
					longitude: '',
					//详情图
					detailsImg: [],
					business: '',
					front: '',
					back: ""
				},
				customStyle: {
					backgroundColor: '#FFCC00',
					color: '#000000',
					border: 0
				},
				status: 1,
				//详情图
				detailsImg: [],
				shopId: '',
				page: 1,
				limit: 100,
				show: false,
				shopList: [],
				city: '',
				province: '',
				district: '',
				auditReason: '',
				Settedlist: [],
				sending: false,
				sendTime: '获取验证码',
				count: 60,
				XCXIsSelect:'是'
			}
		},
		onLoad(option) {
			this.XCXIsSelect = this.$queue.getData('XCXIsSelect');
			if(this.XCXIsSelect =='否'){
				uni.setNavigationBarTitle({
				    title: '隐私政策'
				});
			}else{
				uni.setNavigationBarTitle({
				    title: '实名认证'
				});
			}
			// uni.getLocation({
			// 	type: 'gcj02',
			// 	success: (res) => {
			// 		console.log(res.latitude);
			// 		console.log(res.longitude);
			// 		console.log("res___:" + JSON.stringify(res));
			// 	},
			// 	fail(e) {
			// 		console.log(e);
			// 	}
			// });
			
			// this.Settedlist = uni.getStorageSync("Settedlist")
			// if(this.Settedlist){
			// this.auditReason = this.Settedlist.auditReason
			// this.shop.name = this.Settedlist.shopName
			// this.shop.typeid = this.Settedlist.shopTypeId
			// this.shop.shopTypeName = this.Settedlist.shopTypeName
			// this.shop.address = this.Settedlist.province + this.Settedlist.city + this.Settedlist.district
			// this.shop.detailedAddress = this.Settedlist.detailedAddress
			// this.shop.latitude = this.Settedlist.shopLat
			// this.shop.longitude = this.Settedlist.shopLng
			// this.province = this.Settedlist.province
			// this.city = this.Settedlist.city
			// this.district = this.Settedlist.district
			// this.shop.userName =this.Settedlist.realName
			// this.shop.isNumber = this.Settedlist.identitycardNumber
			// this.shop.phone = this.Settedlist.phone
			// this.shop.business = this.Settedlist.businessLicense
			// this.shop.front = this.Settedlist.identitycardPro
			// this.shop.back = this.Settedlist.identitycardCon
			// this.shop.detailsImg = this.Settedlist.shopCover
			// this.detailsImg = this.Settedlist.shopCover.split(',')
			// }else{
			this.getShopList()
			// }
			this.getData()
		},
		onShow() {

		},
		onHide() {

		},
		methods: {
			getData() {
				this.$Request.get("/app/shop/selectShopByUserId").then(res => {
					if (res.code == 0 && res.data) {
						this.auditReason = res.data.auditReason

						this.shop.shopTypeName = res.data.shopTypeName
						this.shop.name = res.data.shopName
						this.shop.address = res.data.province + res.data.city + res.data.district
						this.shop.detailedAddress = res.data.detailedAddress
						this.shop.userName = res.data.realName
						this.shop.isNumber = res.data.identitycardNumber
						this.shop.phone = res.data.phone

						this.shop.detailsImg = res.data.shopCover //商铺图片
						this.detailsImg = res.data.shopCover.split(',')
						this.shop.business = res.data.businessLicense //营业执照
						this.shop.front = res.data.identitycardPro //身份证正面
						this.shop.back = res.data.identitycardCon //身份证反面

						this.shop.latitude = res.data.shopLat
						this.shop.longitude = res.data.shopLng
						this.province = res.data.province
						this.city = res.data.city
						this.district = res.data.district
						this.shop.status = res.data.status
					}
				});
			},
			confirm(e) {
				console.log(e)
				this.shop.shopTypeName = e[0].label
				this.shop.typeid = e[0].value
			},
			// 店铺列表
			getShopList() {
				let data = {
					page: this.page,
					limit: this.limit,
				}
				this.$Request.get("/app/shoptype/selectShopTypeList", data).then(res => {
					if (res.code == 0) {
						this.shopList = res.data.list

					}
				});
			},


			// 详情图删除
			removeImg(index) {
				this.detailsImg = ''
			},
			// 身份证 资格证删除
			removeImgs(index) {
				if (index == 1) {
					this.shop.business = ''
				} else if (index == 2) {
					this.shop.front = ''
				} else if (index == 3) {
					this.shop.back = ''
				}
			},
			bindOpen() {
				const that = this;
				uni.chooseLocation({
					success: function(res) {
						console.log('位置名称：' + res.name);
						console.log('详细地址：' + res.address);
						console.log('纬度：' + res.latitude);
						console.log('经度：' + res.longitude);
						that.shop.detailedAddress = res.address
						that.shop.latitude = res.latitude
						that.shop.longitude = res.longitude
						that.getcity(res.latitude, res.longitude)
					}
				});
			},
			getcity(latitude, longitude) {
				let data = {
					lat: latitude,
					lng: longitude,
				}
				this.$Request.get("/app/address/selectCity", data).then(res => {
					if (res.code == 0) {
						this.city = res.data.city
						this.province = res.data.province
						this.district = res.data.district
						this.shop.address = res.data.province + res.data.city + res.data.district

					}
				});
			},
			// 图片上传
			addImages(e) {
				let that = this
				uni.chooseImage({
					count: 1,
					sourceType: ['album', 'camera'],
					success: res => {
						for (let i = 0; i < res.tempFilePaths.length; i++) {
							that.$queue.showLoading("上传中...");
							uni.uploadFile({ // 上传接口
								url: that.config("APIHOST1") + '/alioss/upload', //真实的接口地址
								// url: 'https://jiazheng.xianmxkj.com/sqx_fast/alioss/upload',
								filePath: res.tempFilePaths[i],
								name: 'file',
								success: (uploadFileRes) => {
									// if (that.detailsImg.length < 6) {
									that.detailsImg = JSON.parse(uploadFileRes.data).data
									// }

									console.log(that.detailsImg)
									uni.hideLoading();
								}
							});
						}
					}
				})
			},
			// 图片上传
			addImage(e) {
				let that = this
				uni.chooseImage({
					count: 1,
					sourceType: ['album', 'camera'],
					success: res => {
						for (let i = 0; i < res.tempFilePaths.length; i++) {
							that.$queue.showLoading("上传中...");
							uni.uploadFile({ // 上传接口
								url: that.config("APIHOST1") + '/alioss/upload', //真实的接口地址
								// url: 'https://jiazheng.xianmxkj.com/sqx_fast/alioss/upload',
								filePath: res.tempFilePaths[i],
								name: 'file',
								success: (uploadFileRes) => {
									if (e == 1) {
										that.shop.business = JSON.parse(uploadFileRes.data).data
									} else if (e == 2) {
										that.shop.front = JSON.parse(uploadFileRes.data).data
									} else if (e == 3) {
										that.shop.back = JSON.parse(uploadFileRes.data).data
									}

									console.log(that.detailsImg)
									uni.hideLoading();
								}
							});
						}
					}
				})
			},
			config: function(name) {
				var info = null;
				if (name) {
					var name2 = name.split("."); //字符分割
					if (name2.length > 1) {
						info = configdata[name2[0]][name2[1]] || null;
					} else {
						info = configdata[name] || null;
					}
					if (info == null) {
						let web_config = cache.get("web_config");
						if (web_config) {
							if (name2.length > 1) {
								info = web_config[name2[0]][name2[1]] || null;
							} else {
								info = web_config[name] || null;
							}
						}
					}
				}
				return info;
			},
			// 发布
			submit() {
				console.log(this.detailsImg)
				this.shop.detailsImg = this.detailsImg
				this.shop.detailsImg = this.shop.detailsImg.toString();

				if (!this.shop.shopTypeName) {
					uni.showToast({
						title: '请填写商铺主营类型',
						icon: 'none',
						duration: 1000
					})
					return
				}
				if (!this.shop.name) {
					uni.showToast({
						title: '请填写商铺名称',
						icon: 'none',
						duration: 1000
					})
					return
				}
				if (!this.shop.address) {
					uni.showToast({
						title: '请填写商铺地址',
						icon: 'none',
						duration: 1000
					})
					return
				}
				if (!this.shop.detailedAddress) {
					uni.showToast({
						title: '请填写店铺详细地址',
						icon: 'none',
						duration: 1000
					})
					return
				}

				if (!this.shop.userName) {
					uni.showToast({
						title: '请填写真实姓名',
						icon: 'none',
						duration: 1000
					})
					return
				}
				if (!this.shop.isNumber) {
					uni.showToast({
						title: '请填写身份证号',
						icon: 'none',
						duration: 1000
					})
					return
				}
				if (!this.shop.phone) {
					uni.showToast({
						title: '请填写联系电话',
						icon: 'none',
						duration: 1000
					})
					return
				}
				if (!this.shop.verCode) {
					uni.showToast({
						title: '请填写验证码',
						icon: 'none',
						duration: 1000
					})
					return
				}


				if (!this.shop.detailsImg) {
					uni.showToast({
						title: '请上传商铺图片',
						icon: 'none',
						duration: 1000
					})
					return
				}
				if (!this.shop.business) {
					uni.showToast({
						title: '请上传营业执照',
						icon: 'none',
						duration: 1000
					})
					return
				}
				if (!this.shop.front) {
					uni.showToast({
						title: '请上传身份证正面',
						icon: 'none',
						duration: 1000
					})
					return
				}
				if (!this.shop.back) {
					uni.showToast({
						title: '请上传身份证反面',
						icon: 'none',
						duration: 1000
					})
					return
				}

				let data = {

					shopName: this.shop.name,
					shopTypeId: this.shop.typeid,
					shopTypeName: this.shop.shopTypeName,
					detailedAddress: this.shop.address,
					detailedAddress: this.shop.detailedAddress,
					shopLat: this.shop.latitude,
					shopLng: this.shop.longitude,
					province: this.province,
					city: this.city,
					district: this.district,
					realName: this.shop.userName,
					identitycardNumber: this.shop.isNumber,
					phone: this.shop.phone,
					code: this.shop.verCode,
					businessLicense: this.shop.business,
					identitycardPro: this.shop.front,
					identitycardCon: this.shop.back,
					shopCover: this.shop.detailsImg

				}
				// uni.setStorageSync('updataShopId', this.shop.shopId)
				this.$Request.postJson("/app/shop/insertShopAuthentication", data).then(res => {
					if (res.code == 0) {
						uni.showToast({
							title: '提交成功，等待审核通过',
							icon: 'none'
						})
						setTimeout(function() {
							uni.navigateBack()
						}, 1000)
					} else {
						uni.showToast({
							title: res.msg,
							icon: 'none'
						})
					}
				});
			},
			sendMsg() {
				// const {
				// 	phone
				// } = this.shop;
				console.log('this.shop.phone', this.shop.phone)
				if (!this.shop.phone) {
					this.$queue.showToast("请输入手机号");
				} else if (this.shop.phone.length !== 11) {
					this.$queue.showToast("请输入正确的手机号");
				} else {
					this.$queue.showLoading("正在发送验证码...");
					this.$Request.getT("/app/shop/sendMsgs/" + this.shop.phone + "/enter").then(res => {
						if (res.code === 0) {
							this.sending = true;
							this.$queue.showToast('验证码发送成功请注意查收');
							this.countDown();
							uni.hideLoading();
						} else {
							uni.hideLoading();
							uni.showModal({
								showCancel: false,
								title: '短信发送失败',
								content: res.msg ? res.msg : '请一分钟后再获取验证码'
							});
						}
					});
				}
			},
			countDown() {
				const {
					count
				} = this;
				if (count === 1) {
					this.count = 60;
					this.sending = false;
					this.sendTime = '获取验证码'
				} else {
					this.count = count - 1;
					this.sending = true;
					this.sendTime = count - 1 + '秒后重新获取';
					setTimeout(this.countDown.bind(this), 1000);
				}
			},
		},
	}
</script>

<style>
	page {
		background-color: #F5F5F5;
	}

	.bg {
		background-color: #FFFFFF;
	}

	textarea::-webkit-input-placeholder {
		color: #303133;
		font-size: 20rpx;
	}

	textarea:-moz-placeholder {
		color: #303133;
		font-size: 20rpx;
	}

	textarea::-moz-placeholder {
		color: #303133;
		font-size: 20rpx;
	}

	textarea::-ms-input-placeholder {
		color: #303133;
		font-size: 20rpx;
	}


	.tabBox {
		border: 1rpx solid #999999;
		padding: 15rpx 20rpx;
		border-radius: 15rpx;
		font-size: 28rpx;
	}

	.btnnum {
		color: #005DFF;
		border: 1rpx solid #005DFF;
	}

	.send-msg {
		border-radius: 30px;
		color: #FFFFFF;
		/*#FCD202 */
		background: #1789FD;
		height: 60rpx;
		font-size: 28rpx;
		line-height: 60rpx;
		display: inline-block;
		width: 45%;
		margin-left: 4%;
		position: relative;
		top: 20rpx;
	}
</style>
