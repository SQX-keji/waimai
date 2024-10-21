<template>
	<view>
		
		<view style="position: fixed;top: 0;width: 100%;z-index: 999;">
			<view class="flex justify-between align-center bg-white padding-tb padding-lr-sm">
				<view class="flex-sub text-center" :class="current==1?'select':''" @click="confirm(1)">综合排序</view>
				<view class="flex-sub text-center" :class="current==3?'select':''" @click="confirm(3)">距离优先</view>
				<view class="flex-sub text-center" :class="current==4?'select':''" @click="confirm(4)">销量优先</view>
				<view class="flex-sub text-center flex" @click="isShow = !isShow" >
					<view class="flex align-center" style="margin: 0 auto;">
						<view :class="isShow?'select':''" >筛选</view>
						<u-icon v-if="!isShow" name="arrow-down" size="28"></u-icon>
						<u-icon v-if="isShow" name="arrow-up" color="#FCD202" size="28"></u-icon>
					</view>
				</view>
			</view>
			<view v-if="isShow" style="position: absolute;top: 90rpx;width: 100%;z-index: 1000;background: rgba(0,0,0,.5);height: 100vh;" @click="isShow =false">
				<view class="padding-lr bg-white">
					<view class="flex justify-between align-center padding-tb-sm u-border-bottom" v-for="(item,index) in options4" :key='index' @click.stop="getSelect(item)" >
						<view class="text-df" :class="item.select?'select':''">{{item.shopTypeName}}</view>
						<u-icon v-if="item.select" name="checkmark" color="#FCD202" size="28"></u-icon>
					</view>
				</view>
			</view>
		</view>

		<!-- #ifdef H5 -->
		<view class="padding-lr-sm">
		<!-- #endif -->
		<!-- #ifndef H5 -->
		<view class="padding-lr-sm" style="margin-top: 100rpx;">
		<!-- #endif -->
			<view class="" v-for="(item,index) in shopList" :key='index'>
				<view class="margin-tb-sm flex justify-between bg-white padding"
					@click="goNav('/pages/index/shop/index?shopId='+item.shopId)">
					<image :src="item.shopCover" class="radius" style="width: 160rpx;height: 160rpx;"></image>
					<view class=" margin-left-sm" style="width: 450rpx;">
						<view class=" flex flex-direction justify-between">
							<view class="text-lg text-bold text-black">{{item.shopName}}</view>
							<view class="flex align-center margin-top-xs" style="width: 100%;">
								<u-icon name="star-fill" color="#FD6416" size="28"></u-icon>
								<text class="text-lg" style=""> {{item.shopScore?item.shopScore:0}}</text>
								<text class="text-gray flex-sub margin-left-xs">销量{{item.shopSales?item.shopSales:0}}</text>
								<text class="text-gray margin-left-xs">{{item.errandTime}}分钟</text>
								<text class="text-gray margin-left-xs">{{item.distance}}</text>
							</view>
							<view class="text-gray margin-top-xs flex justify-between">
								<view>起送 ¥{{item.minimumDelivery}} 配送 ¥{{item.errandMoney?item.errandMoney:0}}</view>
								<view style="color: #FCD202;">{{item.autoSendOrder==1?'商家配送':'平台配送'}}</view>
							</view>
							<view class="text-gray margin-top-xs" v-if="item.businessHours&&item.lockHours">
								营业时间：{{item.businessHours}}-{{item.lockHours}}</view>
							<view class="flex margin-top-xs">
								<view class="lable" v-if="item.exemptMinMoney">满{{item.exemptMinMoney}}免配送费</view>
								<view class="lable" v-for="(ite,ind) in item.shopLable" :key='ind'
									v-if="item.shopLable">
									{{ite}}
								</view>
							</view>
						</view>
						<view class="flex margin-top-xs">
							<view v-for="(ite,ind) in item.goodsList" :key='ind'
								@click.stop="goDet(ite.goodsId,item.shopId)" style="width: 33%;">
								<image :src="ite.goodsCover" style="width: 120rpx;height: 120rpx;" class="radius"
									mode=""></image>
								<view class="u-line-1 text-df text-bold margin-top-xs">{{ite.goodsName}}</view>
								<view class="text-bold margin-top-xs" style="color: #FD6416;"> 
								<text class="text-sm">¥</text> {{ite.goodsMoney}}</view>
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>
		<empty v-if="!shopList.length"></empty>
	
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
				hintShow: false,
				shop: {},
				page: 1,
				limit: 10,

				shopList: [],

				current: 1,
				shopTypeId: '',
				userId: '',
				lng: '',
				lat: '',
				value1: 0,
				value2: 0,
				value3: 0,
				value4: 0,
				options1: [{
					label: '综合排序',
					value: 1,
				}],
				options2: [{
					value: '3',
					label: "距离优先"
				}],
				options3: [{
					value: '4',
					label: "销量优先"
				}],
				options4: [{
					id: '',
					select: true,
					shopTypeName: "全部"
				}],
				title: '综合排序',
				title1: '距离优先',
				title2: '销量优先',
				title3: '筛选',
				totalCount: 0
			}
		},
		onLoad(e) {
			console.log(e)
			let that = this
			uni.showLoading({
				title: '加载中'
			})
			that.shopTypeId = e.value ? e.value : ''
			that.value2 = e.value ? e.value : 0
			that.userId = uni.getStorageSync('userId')
			that.getShopType()
			uni.getLocation({
				type: 'gcj02', //返回可以用于uni.openLocation的经纬度
				success: function(res) {
					that.lat = res.latitude;
					that.lng = res.longitude;
					that.getShopList()
				}
			});

		},
		methods: {
			goBack(){
			  uni.navigateBack({
			  	
			  })	
			},
			getSelect(e) {
				console.log(e)
				this.options4.forEach(res=>{
					if(res.id == e.id) {
						res.select = true
						this.page = 1
						this.shopTypeId = e.id
						this.getShopList();
						this.isShow = false
					} else {
						res.select = false
					}
				})
			},
			confirm(e) {
				console.log(e)
				this.page = 1
				this.current = e;

				this.getShopList();
			},
			confirm2(e) {
				console.log(e)
				this.page = 1
				this.current = e;

				this.getShopList();
			},
			confirm3(e) {
				console.log(e)
				this.page = 1
				this.current = e;

				this.getShopList();
			},
			confirm4(e) {
				console.log(e)
				this.page = 1
				this.shopTypeId = e

				this.getShopList();
			},

			// 商户列表
			getShopList() {
				let data = {
					page: this.page,
					limit: this.limit,
					screen: this.current,
					shopTypeId: this.shopTypeId,
					lng: this.lng,
					lat: this.lat
				}
				this.$Request.getT('/app/goods/selectShop', data).then(res => {
					uni.hideLoading()
					if (res.code == 0) {
						console.log(res.data.list)
						this.totalCount = res.data.totalCount
						res.data.list.forEach(ret => {
							if (ret.distance > 1000) {
								ret.distance = Number((ret.distance / 1000)).toFixed(2) + "km"
							} else {
								if (ret.distance == 0) {
									ret.distance = "0m";
								} else {
									ret.distance = Number(ret.distance).toFixed(1) + "m";
								}
							}
							ret.shopLable = ret.shopLable ? ret.shopLable.split(',') : ''
							ret.shopCover = ret.shopCover ? ret.shopCover.split(',') :
								'../../static/logo.png'
							ret.errandTime = Math.round(ret.errandTime)
							ret.shopScore = ret.shopScore.toFixed(1)
						})


						if (this.page == 1) {
							this.shopList = res.data.list
						} else {
							this.shopList = [...this.shopList, ...res.data.list]
						}
						console.log(this.shopList)
					}
				})
			},
			// 商户类型
			getShopType() {
				this.$Request.getT('/app/shoptype/selectShopTypeList').then(res => {
					if (res.code == 0) {
						res.data.forEach(res => {
							res.select = false
						})
						this.options4 = [...this.options4, ...res.data]
					}
				})
			},
			goNav(url) {
				console.log(url)
				if (this.userId) {
					uni.navigateTo({
						url
					})
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},
			goShopDet(url, e) {
				this.shop = e
				// console.log(e, '当前店铺')

				// let myDate = new Date();
				// let hours = myDate.getHours();
				// let minute = myDate.getMinutes();
				// let openTime = this.shop.businessHours.split(':')[0] //开始小时
				// let closeTime = this.shop.lockHours.split(':')[0] //结束小时
				// let openTime1 = this.shop.businessHours.split(':')[1] //开始分钟
				// let closeTime1 = this.shop.lockHours.split(':')[1] //结束分钟.
				// console.log(hours)
				// console.log(minute)
				// console.log(openTime)
				// console.log(openTime1)

				// console.log(closeTime)
				// console.log(closeTime1)
				// console.log(minute >= closeTime1)
				// if (hours < openTime) {
				// 	this.hintShow = true
				// 	return
				// } else if (hours == openTime && minute < openTime1) {
				// 	this.hintShow = true
				// 	return
				// } else if (hours >= closeTime) {
				// 	this.hintShow = true
				// 	return
				// } else if (hours == closeTime && minute >= closeTime1) {
				// 	this.hintShow = true
				// 	return
				// }

				console.log(url)
				if (this.userId) {
					uni.navigateTo({
						url
					})
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},
		},
		onReachBottom: function() {
			if(this.shopList.length<this.totalCount) {
				this.page = this.page + 1;
				this.getShopList()
			} else {
				uni.showToast({
					title: '已经到底了',
					icon: 'none'
				})
			}
			
		},
	}
</script>

<style>
	.select{
		color: #FCD202;
	}
	.tabs {
		margin: 20rpx 0;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.tabs_name {

		flex: 1;
		/* display: inline-block; */
		text-align: center;
		margin-bottom: 30rpx 0;
		position: relative;
		display: flex;
		/* justify-content: space-between; */
		align-items: center;
	}

	.tabs_name view {
		margin: 0 auto;
		display: flex;
		align-items: center;
	}

	.tabs_2 {
		/* width: 30%; */
	}

	.actives {
		color: #007AFF;
	}

	.tabsicon {
		position: absolute;
		top: 0;
		right: 22rpx;
		width: 20rpx;
		height: 40rpx;
		z-index: 1;
	}

	.tabs_icon {
		width: 12rpx;
		height: 8rpx;
		z-index: 1;
		margin-left: 10rpx;
	}

	.icon1 {
		/* position: absolute; */
		/* top: 6rpx; */
	}

	.icon2 {
		position: absolute;
		bottom: 6rpx;
	}


	.hintPopul {
		width: 100%;
		height: 100vh;
		position: fixed;
		top: 0;
		background: rgba(0, 0, 0, .4);
		z-index: 999;
	}

	.content_ {
		position: absolute;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		margin: auto;
		text-align: center;
		width: 500rpx;
		height: 400rpx;
		border-radius: 20rpx;
		background-color: #fff;
		padding-top: 120rpx;
	}

	.content_ image {
		position: absolute;
		top: -50rpx;
		left: 0;
		right: 0;
		margin: auto;
	}

	.hintText {
		font-size: 30rpx;
	}

	.skuBtn {
		width: 460rpx;
		height: 60rpx;
		line-height: 60rpx;
		text-align: center;
		background: #FCD202;
		font-size: 28rpx;
		border: 2rpx solid #FCD202;
		color: #333333;
		border-radius: 50rpx;
		font-weight: 700;
		margin: auto;
	}

	.lable {
		padding: 8rpx 14rpx;
		background: #FFE6D9;
		border-radius: 4rpx;
		font-weight: 500;
		color: #FD6416;
		font-size: 20rpx;
		margin-right: 10rpx;
	}
</style>
