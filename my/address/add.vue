<template>
	<view class="">
		<form>
			<view class="cu-form-group">
				<view class="title">联系人</view>
				<input placeholder="请输入联系人" name="input" v-model="form.userName"></input>
			</view>
			<view class="cu-form-group">
				<view class="title">联系电话</view>
				<input placeholder="请输入联系电话" name="input" v-model="form.userPhone"></input>
			</view>
			<view class="cu-form-group" @click="pickerShow">
				<view class="title">所在地区</view>
				<input placeholder="请选择地区" name="input" disabled v-model="form.address"></input>
				<text class='cuIcon-locationfill text-orange'></text>
			</view>
			<view class="cu-form-group">
				<view class="title">详细地址</view>
				<input placeholder="请输入详细地址" name="input" v-model="form.addressDetail"></input>
			</view>
			<view class="cu-form-group">
				<view class="title">设为默认</view>
				<switch @change="SwitchA" :class="form.addressDefault?'checked':''"
					:checked="form.addressDefault?true:false"></switch>
			</view>
		</form>

		<view class="btn" v-if="!id">
			<view class="address_push" @click="submit">保存</view>
		</view>
		<view class="btn" v-if="id">
			<view class="address_push" @click="updata">修改</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				form: {
					addressId: '',
					userName: '',
					userPhone: '',
					address: '',
					addressDetail: '',
					addressDefault: 0, //默认地址 0不默认 1默认
					lng: '',
					lat: ''
				},
				// switchA: false,
				region: '',
				
				id: '',
				latitude: '',
				longitude: ''
			}
		},
		onLoad(option) {
			this.id = option.id
			if (option.id) {
				this.getAddressDet(option.id)
				uni.setNavigationBarTitle({
					title: "修改地址"
				})
			}
		},
		methods: {
			pickerShow() {
				let that = this
				// An highlighted block
				uni.chooseLocation({
					success: function(res) {
						console.log(res)
						console.log('位置名称：' + res.name);
						console.log('详细地址：' + res.address);
						console.log('纬度：' + res.latitude);
						console.log('经度：' + res.longitude);
						let latitude = res.latitude; //纬度
						let longitude = res.longitude; //经度
						that.form.lng = longitude
						that.form.lat = latitude
						
						that.form.addressDetail = res.name
						
						let data = {
							lat: latitude,
							lng: longitude
						}
						that.$Request.get("/app/address/selectCity", data).then(res => {
							if (res.code == 0) {
								that.form.province = res.data.province
								that.form.city = res.data.city
								that.form.district = res.data.district
								that.form.address = res.data.province+res.data.city+res.data.district
							}
						});
					}
				});
			},
			SwitchA(e) {
				console.log(e.detail.value)
				this.form.addressDefault = e.detail.value
			},
			// 选择地区回调
			regionConfirm(e) {
				console.log(e)
				this.form.province = e.province.label
				this.form.city = e.city.label
				this.form.area = e.area.label
				this.region = e.province.label + '-' + e.city.label + '-' + e.area.label;
				console.log(this.region)
			},
			// 提交
			submit() {
				if (!this.form.userName) {
					uni.showToast({
						title: '请输入姓名',
						icon: 'none'
					})
					return
				}
				if (!this.form.userPhone) {
					uni.showToast({
						title: '请输入联系电话',
						icon: 'none'
					})
					return
				}
				if (!this.form.address) {
					uni.showToast({
						title: '请选择所在地区',
						icon: 'none'
					})
					return
				}
				if (!this.form.addressDetail) {
					uni.showToast({
						title: '请输入详细地址',
						icon: 'none'
					})
					return
				}
				this.form.addressDefault = this.form.addressDefault ? 1 : 0
				this.$Request.postJson("/app/address/insertAddress", this.form).then(res => {
					if (res.code == 0) {
						uni.showToast({
							title: '保存成功',
							icon: 'none'
						})
						setTimeout(function() {
							uni.navigateBack()
						}, 1000)
					}
				});
			},
			// 修改
			updata() {
				if (!this.form.userName) {
					uni.showToast({
						title: '请输入姓名',
						icon: 'none'
					})
					return
				}
				if (!this.form.userPhone) {
					uni.showToast({
						title: '请输入联系电话',
						icon: 'none'
					})
					return
				}
				if (!this.form.address) {
					uni.showToast({
						title: '请选择所在地区',
						icon: 'none'
					})
					return
				}
				if (!this.form.addressDetail) {
					uni.showToast({
						title: '请输入详细地址',
						icon: 'none'
					})
					return
				}

				let data = {
					addressId: this.form.addressId,
					userName: this.form.userName,
					userPhone: this.form.userPhone,
					address: this.form.address,
					addressDetail: this.form.addressDetail,
					addressDefault: this.form.addressDefault ? 1 : 0,
					lng: this.form.lng,
					lat: this.form.lat,
				}
				this.$Request.postJson("/app/address/updateAddress", data).then(res => {
					if (res.code == 0) {
						uni.showToast({
							title: '修改成功',
							icon: 'none'
						})
						setTimeout(function() {
							uni.navigateBack()
						}, 1500)
					}
				});
			},
			// 根据id获取地址详情
			getAddressDet(e) {
				this.$Request.get("/app/address/selectAddressById?addressId=" + e).then(res => {
					if (res.code == 0) {
						this.form.addressId = res.data.addressId
						this.form.userName = res.data.userName
						this.form.userPhone = res.data.userPhone
						this.form.address = res.data.province+res.data.city+res.data.district
						this.form.addressDetail = res.data.addressDetail
						this.form.lng = res.data.lng
						this.form.lat = res.data.lat
						this.form.addressDefault = res.data.addressDefault
						that.form.province = res.data.province
						that.form.city = res.data.city
						that.form.district = res.data.district
					}
				});
			}

		}

	}
</script>

<style>
	page {
		background-color: #fff !important;
	}

	/* 添加收货地址 */
	.btn {
		/* position: fixed;
		bottom: 0rpx; */
		width: 100%;
		height: 100rpx;
		line-height: 100rpx;
		background-color: white;
		margin-top: 30rpx;
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
