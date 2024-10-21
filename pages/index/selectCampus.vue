<template>
	<view class="container">
		<view class="flex align-center" style="background:#fff;">
			<view class="content padding-left bg-white"
				style="color: #333;font-size: 28upx;font-weight: bold;" v-if="city" >

				<text @click="goCity()">{{city}}<text class="cuIcon-right"></text></text>
			</view>
			<view class="padding-tb-sm padding-lr-xs margin-right-xs flex-sub">
				<view class="search-bar-form">
					<view class="search-bar-box">
						<icon class="icon-search-in-box" type="search" size="16"></icon>
						<input confirm-type="search" class="search-bar-input" placeholder="输入地址查询"
							placeholder-class="phcolor" :value="inputVal" :focus="inputShowed" @input="inputTyping" />
						<view class="icon-clear" v-if="inputVal" @tap="clearInput">
							<!-- #ifdef APP-PLUS || MP -->
							<icon type="clear" :size="15"></icon>
							<!-- #endif -->
						</view>
					</view>
				</view>
			</view>
		</view>

		<view class="tui-list search-result " v-if="inputShowed">
			<view class="padding margin-lr radius flex justify-between align-center" v-for="(item,index) in searchResult"
				:key="index" @click="update(item)">
				<view>
					<view class="text-lg text-bold text-black">{{item.addressDetail}}</view>
					<view class="text-df text-gray margin-top-xs">{{item.province}}{{item.city}}{{item.district}}</view>
				</view>
			</view>
		</view>
		<view v-else>
			<view class="tui-list city-list " v-if="list&&list.length>0">
				<view class="title text-lg text-bold padding-sm padding-top-sm">我的地址</view>
				<view class="padding margin-lr radius flex justify-between align-center" v-for="(item,index) in list"
					:key="index" v-if="index == 0" @click="update(item)" style="border: 2rpx solid #FCD202;">
					<view>
						<view class="text-lg text-bold text-black">{{item.addressDetail}}</view>
						<view class="text-df text-gray margin-top-xs">{{item.province}}{{item.city}}{{item.district}}
						</view>
					</view>
					<u-icon name="checkbox-mark" color="#FCD202" size="42"></u-icon>
				</view>
				<view class="padding margin-lr radius flex justify-between align-center" v-for="(item,index) in list"
					:key="index" v-if="index > 0" @click="update(item)">
					<view>
						<view class="text-lg text-bold text-black">{{item.addressDetail}}</view>
						<view class="text-df text-gray margin-top-xs">{{item.province}}{{item.city}}{{item.district}}
						</view>
					</view>
				</view>

			</view>
		</view>
		<!-- 添加收货地址 -->
		<view class="btn">
			<view class="address_push" @click="addAddress">添加收货地址</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				region: [],
				inputVal: '', // 搜索框输入的内容
				inputShowed: false, // 输入框是否显示
				list: [],
				searchResult: [], // 搜索城市的结果
				localCampus: '',
				campusDetails: '',
				txt: '选择城市',
				province: '', //省
				city: '', //市
				district: '', //区

				page: 1,
				limit: 10,
				totalCount: 0,
				longitude: '',
				latitude: ''
			}
		},
		onLoad: function(options) {
			console.log(options)
			if (uni.getStorageSync('city')) {
				this.city = uni.getStorageSync('city')
				this.longitude = uni.getStorageSync('lng')
				this.latitude = uni.getStorageSync('lat')
			} else {
				this.getlocation()
			}
			
		},
		onShow() {
			
			if(uni.getStorageSync('userId')) {
				this.initCampusList()
			}
			
		},
		methods: {
			update(e) {
				let data = {
					addressId: e.addressId,
					addressDefault: 1,
				}
				this.$Request.postJson("/app/address/updateAddress", data).then(res => {
					if (res.code == 0) {
						uni.removeStorageSync('city')
						uni.removeStorageSync('lng')
						uni.removeStorageSync('lat')
						setTimeout(function() {
							uni.navigateBack()
						}, 10)
					}
				});
			},
			goCity() {
				let that = this
				uni.chooseLocation({
					success: function(res) {
						console.log(res ,'选择');
						
						that.longitude = res.longitude
						that.latitude = res.latitude
						that.city = res.name
						uni.setStorageSync('lng',that.longitude)
						uni.setStorageSync('lat',that.latitude)
						uni.setStorageSync('city',res.name)
						setTimeout(function() {
							uni.navigateBack()
						},10)
						
					}
				});
			},
			
			getlocation() {
				let that = this
				// that.list=[]
				uni.getLocation({
					type: 'gcj02',
					success: function(res) {
						console.log('当前位置的经度：' + res.longitude);
						console.log('当前位置的纬度：' + res.latitude);
						that.longitude = res.longitude
						that.latitude = res.latitude
						
						let data = {
							lat: res.latitude,
							lng: res.longitude
						}
						that.$Request.get("/app/address/selectCity", data).then(res => {
							if (res.code == 0) {
								that.city = res.data.city
							}
						});
						// that.initCampusList();
					}
				});
			},
			//获取社区列表
			initCampusList() {
				let that = this;
				let data = {
					page: that.page,
					limit: that.limit,
				}
				that.$Request.getT('/app/address/selectAddressList', data).then(res => {
					console.log(res)
					if (res.code === 0) {
						if (that.page == 1) {
							that.list = res.data.list
						} else {
							that.list = [...that.list, ...res.data.list]
						}

					}
				})
			},
			showInput() {
				this.inputShowed = true
			},
			clearInput() {
				this.inputVal = "";
				this.inputShowed = false;
				this.searchResult = [];
				uni.hideKeyboard() //强行隐藏键盘
			},
			inputTyping(e) {
				this.inputVal = e.detail.value;
				if (e.detail.value.length === 0) {
					this.searchResult = [];
					this.inputShowed = false
				} else {
					this.inputShowed = true
				}
				this.searchCity()
			},
			// 搜索城市
			searchCity() {
				let data = {
					impotr: this.inputVal,
					page: 1,
					limit: 1000
				}
				this.$Request.get("/app/address/searchAddress", data).then(res => {
					if (res.code == 0) {
						this.searchResult = res.data.list
					}
				});
			},
			// 添加地址
			addAddress() {
				let userId = this.$queue.getData('userId');
				if (!userId) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
					return
				}
				uni.navigateTo({
					url: '/my/address/add'
				})
			},
		},
		onReachBottom: function() {
			this.page = this.page + 1;
			this.initCampusList();
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.initCampusList();
		},
	}
</script>

<style lang="scss">
	page {
		height: 100%;
		background:#FFFFFF;
	}

	.page {
		height: 100%;
		overflow: hidden;
	}

	.search-bar {
		display: flex;
		align-items: center;
		position: relative;
		padding: 27rpx 30rpx 35rpx;
		background-color: #fff;
	}

	.search-bar-form {
		flex: 1;
		position: relative;
		border-radius: 32rpx;
		background: #f2f5f7;
	}

	.search-bar-box {
		display: flex;
		align-items: center;
		position: relative;
		padding-left: 20rpx;
		padding-right: 20rpx;
		height: 64rpx;
		z-index: 1;
	}

	.search-bar-input {
		line-height: normal;
		width: 100%;
		padding-left: 20rpx;
		font-size: 30rpx;
		color: #333;
	}

	.phcolor {
		font-size: 30rpx;
	}

	.icon-clear {
		height: 38rpx;
	}

	.icon-clear .tui-icon-class {
		display: block
	}

	.search-bar-label {
		height: 64rpx;
		display: flex;
		justify-content: center;
		align-items: center;
		position: absolute;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		z-index: 2;
		border-radius: 32rpx;
		color: #ccc;
		background: #f2f5f7;
	}

	.icon-search {
		position: relative;
		height: 26rpx;
		margin-right: 20rpx;
		font-size: inherit;
	}

	.search-bar-text {
		font-size: 30rpx;
		line-height: 32rpx;
	}

	.cancel-btn {
		padding-left: 30rpx;
	}

	.search-result::before {
		display: none;
	}

	.search-result::after {
		display: none;
	}

	.tui-list-cell {
		// padding: 30upx;
		// display: flex;
		// flex-direction: row;
		// justify-content: space-between;
		// align-items: center;
		// width: 100%;
	}

	.tui-list-cell-hover {
		// background-color: #eee !important;
	}

	.tui-list-cell-navigate {
		// width: 100%;
		// position: relative;
		// padding: 30rpx 0 30rpx 30rpx;
		font-size: 30rpx;
		color: #333;
		font-weight: 800;
	}

	.tui-list-cell-navigate::after {
		content: '';
		position: absolute;
		border-bottom: 1rpx solid #eaeef1;
		-webkit-transform: scaleY(0.5);
		transform: scaleY(0.5);
		bottom: 0;
		right: 0;
		left: 30rpx;
	}

	.current-city {
		padding: 0 30rpx 30rpx;
		background: #fff;
	}

	.tui-icon-class {
		margin-right: 10rpx;
	}

	.current-city .title {
		font-size: 28rpx;
		line-height: 24rpx;
		color: #333333;
	}

	.box {
		background: #F5F5F5;
		border-radius: 10upx;

		width: 686upx;
		// height: 134upx;
	}

	.city-name {
		display: flex;
		align-items: center;
		// margin-top: 17rpx;
		font-size: 30rpx;
		font-weight: bold;
		line-height: 30rpx;
		color: #333;

	}

	.hot-city .title {
		height: 48rpx !important;
		padding-left: 30rpx;
		font-size: 24rpx !important;
		line-height: 48rpx !important;
		color: #999;
		background: #f2f5f7 !important;
	}

	.city-names {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-between;
		align-content: space-between;
		padding: 12rpx 90rpx 26rpx 30rpx;
		background: #fff;
	}

	.city-name-item {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 140rpx;
		height: 56rpx;
		margin-top: 16rpx;
		/* border: solid 1rpx #ccc; */
		border-radius: 28rpx;
		font-size: 28rpx;
		color: #333;
		position: relative;
	}

	.city-name-item::before {
		content: "";
		position: absolute;
		width: 200%;
		height: 200%;
		-webkit-transform-origin: 0 0;
		transform-origin: 0 0;
		-webkit-transform: scale(0.5, 0.5);
		transform: scale(0.5, 0.5);
		-webkit-box-sizing: border-box;
		box-sizing: border-box;
		left: 0;
		top: 0;
		border-radius: 56rpx;
		border: 1px solid #ccc;
	}

	.tap-city {
		color: #fff;
		background: #5677fc;
		/* border: solid 1rpx #5677fc; */
	}

	.tui-list {
		background-color: #fff;
		position: relative;
		width: 100%;
		display: flex;
		flex-direction: column;
		padding-bottom: env(safe-area-inset-bottom);
	}

	.tui-list-cell-divider {
		height: 48rpx;
		padding-left: 30rpx;
		font-size: 24rpx;
		color: #999;
		background: #f2f5f7;
		padding: 0 30rpx;
		display: flex;
		align-items: center;
	}

	.tui-indexed-list-bar {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: flex-start;
		z-index: 9999;
		position: absolute;
		top: 132rpx;
		right: 0;
		padding-right: 10rpx;
		width: 44rpx;
	}

	.tui-indexed-list-text {
		font-size: 22rpx;
		white-space: nowrap;
	}

	.tui-indexed-list-bar.active {
		background-color: rgb(200, 200, 200);
	}

	.tui-indexed-list-alert {
		position: absolute;
		z-index: 20;
		width: 160rpx;
		height: 160rpx;
		left: 50%;
		top: 50%;
		margin-left: -80rpx;
		margin-top: -80rpx;
		border-radius: 80rpx;
		text-align: center;
		line-height: 160rpx;
		font-size: 70rpx;
		color: #fff;
		background-color: rgba(0, 0, 0, 0.5);
	}

	/* 添加收货地址 */
	.btn {
		position: fixed;
		bottom: 0rpx;
		width: 100%;
		height: 100rpx;
		line-height: 100rpx;
		background-color: white;
		padding-top: 10rpx;
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
