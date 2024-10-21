<template>
	<view class="content">
		<view @click="goAddress()" class="address text-bold">{{address?address:'选择地址'}}
			<u-icon name="arrow-down" color="#333333" size="28"></u-icon>
		</view>
		<!-- #ifdef H5 -->
		<view class="bg-white" >
		<!-- #endif -->
		<!-- #ifndef H5 -->
		<view class="bg-white" style="margin-top: 180rpx;">
		<!-- #endif -->
			<view class="padding-lr padding-top">
				<view class="flex align-center padding-left" @click="goNav('/pages/index/search/index')"
					style="border: 2rpx solid #FCD202;height: 80rpx;border-radius: 60rpx;">
					<u-icon name="search" size="46"></u-icon>
					<view class="flex-sub text-gray margin-left-sm">输入商家或商品名称</view>
					<view class="search-btn">搜索</view>
				</view>
			</view>

			<view class="padding-top padding-lr">
				<swiper class="swiper radius" :autoplay="true" interval="2000" duration="500" :circular="true"
					style="width: 100%;height: 280rpx;">
					<swiper-item v-for="(item,index) in bannerList" :key='index'>
						<image :src="item.imageUrl" class="radius" style="width: 100%;height: 280rpx;"></image>
					</swiper-item>
				</swiper>
			</view>

			<view class="padding-lr radius" v-if="XCXIsSelect=='是'">
				<u-grid :col="5" :border="false">
					<u-grid-item  v-for="(item,index) in gridData" :key='index' @tap="goDetail(item)">
						<image :src="item.imageUrl" style="width: 92rpx;height: 92rpx;border-radius: 92rpx;">
						</image>
						<view class="grid-text margin-top-sm">{{item.name}}</view>
					</u-grid-item>
				</u-grid>
			</view>
		</view>
		<!-- 公告 -->
		<view class="flex justify-between align-center bg-white padding-bottom padding-lr radius bg-white"
			style="width: 100%;height: 100rpx;">
			<image src="../../static/images/index/gonggao.png" style="width: 126rpx;height: 30rpx;" mode=""></image>
			<view class="flex-sub margin-left-sm">
				<swiper class="swiper" autoplay="1500" :vertical='true' style="height: 40rpx;overflow: hidden;">
					<swiper-item class="" v-for="(item,index) in noticeList" :key='index'>
						<text>{{item.title}}</text>
					</swiper-item>
				</swiper>
			</view>
		</view>
		<view class="flex justify-between padding-lr bg-white">
			<image src="../../static/images/index/jifen.png" @click="goNav('/my/integral/index')"
				style="width: 338rpx;height: 180rpx;" mode=""></image>
			<image src="../../static/images/index/renwu.png" @click="goNav('/my/task/index')"
				style="width: 338rpx;height: 180rpx;" mode=""></image>
		</view>

		<view class="padding-top padding-lr bg-white" v-if="!shopStatus == 1&&XCXIsSelect=='是'">
			<image src="../../static/images/index/banner.png" @click="goNav('/my/apply/index')"
				style="width: 100%;height: 170rpx;" mode=""></image>
		</view>
		<view class="" style="position: relative;">
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
			<view v-if="isShow" style="position: absolute;top: 90rpx;width: 100%;z-index: 10;background: rgba(0,0,0,.5);height: 100vh;" @click="isShow =false">
				<view class="padding-lr bg-white">
					<view class="flex justify-between align-center padding-tb-sm u-border-bottom" v-for="(item,index) in options4" :key='index' @click.stop="getSelect(item)" >
						<view class="text-df" :class="item.select?'select':''">{{item.shopTypeName}}</view>
						<u-icon v-if="item.select" name="checkmark" color="#FCD202" size="28"></u-icon>
					</view>
				</view>
			</view>
		</view>
		
		<view class="padding-lr">
			<view class="margin-tb-sm flex justify-between bg-white padding-sm radius" v-for="(item,index) in shopList"
				:key='index' @click="goShopDet('/pages/index/shop/index?shopId='+item.shopId,item)">
				<image :src="item.shopCover[0]" class="radius" style="width: 160rpx;height: 160rpx;"></image>
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
							<view class="lable" v-for="(ite,ind) in item.shopLable" :key='ind' v-if="item.shopLable">
								{{ite}}
							</view>
						</view>
					</view>
					<view class="flex margin-top-xs">
						<view v-for="(ite,ind) in item.goodsList" :key='ind'
							@click.stop="goDet(ite.goodsId,item.shopId)" style="width: 33%;">
							<image :src="ite.goodsCover" style="width: 120rpx;height: 120rpx;" class="radius" mode="">
							</image>
							<view class="u-line-1 text-df text-bold margin-top-xs">{{ite.goodsName}}</view>
							<view class="text-bold margin-top-xs" style="color: #FD6416;"> <text
									class="text-sm">¥</text> {{ite.goodsMoney}}</view>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- <empty v-if="!shopList.length" ></empty> -->
		<view class="page-box" v-if="!shopList.length">
			<view class="centre">
				<image src="../../static/images/empty.png" mode=""></image>
				<view class="tips">暂无内容</view>
			</view>
		</view>
		<!-- 打样提示 -->
		<view v-if="hintShow" class="hintPopul" @click.stop="hintShow=false">
			<view class="content_">
				<image src="../../static/images/index/shop.png" mode="" style="width: 200rpx;height: 180rpx;">
				</image>
				<view class="text-xl text-bold">店铺打烊啦</view>
				<view class="hintText margin-top-sm text-gray">现在店铺已经打烊了,营业时间</view>
				<view class="margin-top-xs text-gray margin-bottom">{{shop.businessHours}}-{{shop.lockHours}}</view>
				<view class="skuBtn" @click="hintShow=false">知道了</view>
			</view>
		</view>
		<!-- 新人红包 -->
		<view class="hongbao" v-if="HBShow &&waimaiHB == '是'" >
			<view style="width: 52%;margin: 0 auto;position: relative;">
				<view @click="HBShow=false" style="position: absolute;right: -10rpx;top: -10rpx;font-size: 32rpx;font-weight: bold;">X</view>
				<image src="../../static/images/index/hb_bg.png" class="hb_img"></image>
				<image src="../../static/images/index/hb_btn.png" class="hb_btn" @click="takemoney()"></image>
			</view>
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
				HBShow: false,
				isShow: false,
				hintShow: false,
				address: '暂无地址',
				bannerList: [{
						"id": 3,
						"createTime": "2021-12-24 17:52:27",
						"name": "banner3",
						"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2021/12/24/0bfdbabcde9c62b42e77aecdb583033b.png",
						"state": 1,
						"classify": 1,
						"url": "",
						"sort": 3,
						"describess": "banner3"
					},
					{
						"id": 45,
						"createTime": "2021-12-24 17:52:27",
						"name": "banner",
						"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2021/12/24/589d72b40a690147a48854a3b7f88953.png",
						"state": 1,
						"classify": 1,
						"url": "",
						"sort": 1,
						"describess": ""
					},
				],
				gridData: [{
					"id": 22,
					"createTime": "2022-02-18 16:44:17",
					"name": "外卖",
					"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2022/02/18/9c6bac5a332188f22d7a692831b09c1c.png",
					"state": 1,
					"classify": 4,
					"url": "/pages/index/shopList/index",
					"sort": 1,
					"describess": "精品1"
				}, {
					"id": 26,
					"createTime": "2022-02-18 16:44:17",
					"name": "水果",
					"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2022/02/18/8aa4e61ded3fc8b341be14acba170607.png",
					"state": 1,
					"classify": 4,
					"url": "/pages/index/shopList/index",
					"sort": 1,
					"describess": "精品2"
				}, {
					"id": 51,
					"createTime": "2022-02-18 16:44:17",
					"name": "日用好货",
					"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2022/02/18/c52f353674722c5db28186850607ea6d.png",
					"state": 1,
					"classify": 4,
					"url": "/pages/index/shopList/index",
					"sort": 1,
					"describess": "美食类型商铺"
				}, {
					"id": 59,
					"createTime": "2022-02-18 16:44:17",
					"name": "肉蛋蔬菜",
					"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2022/02/18/81a44aeb97590c8961afa6a642ab0a7a.png",
					"state": 1,
					"classify": 4,
					"url": "/pages/index/shopList/index",
					"sort": 1,
					"describess": null
				}, {
					"id": 60,
					"createTime": "2022-02-18 16:44:17",
					"name": "大牌乳品",
					"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2022/02/18/62d5d5700811720d601a448e21fd4564.png",
					"state": 1,
					"classify": 4,
					"url": "/pages/index/shopList/index",
					"sort": 1,
					"describess": null
				}, {
					"id": 61,
					"createTime": "2022-02-18 16:44:17",
					"name": "帮我送",
					"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2022/02/18/5ee033f64352b96941cd55ff5cc2f86e.png",
					"state": 1,
					"classify": 4,
					"url": "/running/Helpsend/Helpsend?index=1",
					"sort": 1,
					"describess": null
				}, {
					"id": 62,
					"createTime": "2022-02-18 16:44:17",
					"name": "帮我取",
					"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2022/02/18/56ab760223015b1cd33caf1f9af73cda.png",
					"state": 1,
					"classify": 4,
					"url": "/running/Helpsend/Helpsend?index=2",
					"sort": 1,
					"describess": null
				}, {
					"id": 63,
					"createTime": "2022-02-18 16:44:17",
					"name": "同城服务",
					"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2022/02/18/d01391db321b35a76a76b3b0c485a0d0.png",
					"state": 1,
					"classify": 4,
					"url": "/running/Cityservice/Cityservice",
					"sort": 1,
					"describess": null
				}, {
					"id": 64,
					"createTime": "2022-02-18 16:44:17",
					"name": "陪玩服务",
					"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2022/02/18/795f621be2eb7df2dcf740ad30c7cedd.png",
					"state": 1,
					"classify": 4,
					"url": "/running/Cityservice/Cityservice?title=陪玩服务",
					"sort": 1,
					"describess": null
				}, {
					"id": 65,
					"createTime": "2022-02-18 16:44:17",
					"name": "同城帮买",
					"imageUrl": "https://diancan.xianmxkj.com/file/uploadPath/2022/02/18/04d342ce358635ff0196f88c8736441e.png",
					"state": 1,
					"classify": 4,
					"url": "/running/Helppay/Helppay",
					"sort": 1,
					"describess": null
				}, ],
				page: 1,
				limit: 10,
				shopList: [],
				screen: '1',
				lng: '',
				lat: '',
				shopType: '1',
				shopTypeList: [{
					id: '0',
					shopTypeName: "全部"
				}],
				userId: '',
				src1: '../../static/images/index/xia.png',
				noticeList: [],
				show: false,
				typeShow: false,
				current: 1,
				sort: 1,
				fabuclassifyId: '',
				typeName: '全部',
				fabuclassifyName: '筛选',
				shopTypeId: '',
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
				defaultIndex: [0, 0, 0, 0],
				filterData: [
					[{
						label: '综合排序',
						value: '1',
					}],
					[{
						label: '距离优先',
						value: '3',
					}],
					[{
						label: '销量优先',
						value: '4',
					}],
					[{
							label: '不限性别',
							value: '0',
						},
						{
							label: '限男生',
							value: 1,
						},
						{
							label: '限女生',
							value: 2,
						}
					],

				],
				shopStatus: '',
				shop: {},
				title: '综合排序',
				title1: '距离优先',
				title2: '销量优先',
				title3: '筛选',
				XCXIsSelect: '是',
				arr: [],
				showModal: true,
				shopId: '',
				totalCount: 0,
				newUserFlagWm: 2,
				waimaiHB: '是'
			}
		},
		onLoad(e) {
			let that = this
			// #ifdef MP-WEIXIN
			that.XCXIsSelect = that.$queue.getData('XCXIsSelect')?that.$queue.getData('XCXIsSelect'):'是'
			// #endif
			that.waimaiHB = that.$queue.getData('waimaiHB')?that.$queue.getData('waimaiHB'): '是'
			that.userId = uni.getStorageSync('userId')?uni.getStorageSync('userId'):''
			that.getBannerList()
			that.getGridList()
			that.getShopType()
			that.getNoticeList()

			uni.getLocation({
				type: 'gcj02', //返回可以用于uni.openLocation的经纬度
				success: function(res) {
					console.log(res)
					that.lat = res.latitude;
					that.lng = res.longitude;
					that.defaultAddress()
					that.getShopList()
				}
			});
			console.log(that.lat,that.lng,'经纬度')
			

			// #ifdef MP-WEIXIN
			
			if (e.scene) {
				const scene = decodeURIComponent(e.scene);
				let list=scene.split(',');
				let shopId;
				
				if(list.length>0){
					shopId = list[0]; //获取shopid
				}
				// uni.setStorageSync('shopId',e.shopId)
				console.log(e,'首页')
				console.log(scene,'商铺id')
				console.log(shopId,'shopId')
				
				if (shopId) {
					that.shopId = shopId
				} 
				
				setTimeout(function() {
					uni.navigateTo({
						url: '/pages/index/shop/index?shopId=' + that.shopId
					})
				}, 10)
			} else if(e.orderId) {
				uni.setStorageSync('orderId',e.orderId)
				setTimeout(function() {
					uni.navigateTo({
						url: '/pages/index/shop/goodsList?shopId=' + e.shopId
					})
				}, 10)
			}
			// #endif
		},
		onShow() {
			let that = this
			uni.getLocation({
				type: 'gcj02', //返回可以用于uni.openLocation的经纬度
				success: function(res) {
					console.log(res)
					that.lat = res.latitude;
					that.lng = res.longitude;
				}
			});
			this.userId = uni.getStorageSync('userId')?uni.getStorageSync('userId'):''
			
			this.lng = uni.getStorageSync('lng') ? uni.getStorageSync('lng') : this.lng
			this.lat = uni.getStorageSync('lat') ? uni.getStorageSync('lat') : this.lat
			console.log(this.userId, '我的id')
			// if (this.lng && this.lat) {
				this.defaultAddress()
				
			// }

			if (this.userId) {
				this.getUserInfo()

				this.$Request.getT('/app/common/type/266').then(res => { //订单取消通知
					if (res.code == 0) {
						if (res.data && res.data.value) {
							this.arr.push(res.data.value)
						}
					}
				})
				this.$Request.getT('/app/common/type/269').then(res => { //订单状态通知
					if (res.code == 0) {
						if (res.data && res.data.value) {
							this.arr.push(res.data.value)
						}
					}
				})
				// #ifdef MP-WEIXIN
				if (this.showModal) {
					this.openMsg()
				}
				// #endif
			}
			// this.getShopList()

		},
		methods: {
			// 红包
			takemoney() {
				this.$Request.getT('/app/userinfo/getNewUserRedPacketWm').then(res => {
					console.log(res.code)
					if (res.code == 0) {
						this.HBShow = false
						this.getUserInfo()
						setTimeout(function() {
							uni.navigateTo({
								url:'/my/coupon/index'
							})
						},100)
					}
				});
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
			// 获取用户信息
			getUserInfo() {
				this.$Request.getT('/app/user/selectUserMessage').then(res => {
					console.log(res)
					if (res.code == 0) {
						this.shopStatus = res.data.shopStatus
						this.newUserFlagWm = res.data.newUserFlagWm
						if(this.newUserFlagWm == 1) {
							this.HBShow = true
						}else {
							this.HBShow = false
						}
					}
				});
			},
			// 获取公告
			getNoticeList() {
				let data = {
					page: 1,
					limit: 100
				}
				this.$Request.get("/app/notice/selectNoticeList", data).then(res => {
					if (res.code == 0) {
						this.noticeList = res.data.list
					}
				});
			},
			confirm(e) {
				this.isShow = false
				this.page = 1
				this.current = e;
				this.getShopList();
			},
			
			// 获取轮播图
			getBannerList() {
				let data = {
					classify: 1
				}
				this.$Request.get("/app/banner/selectBannerList", data).then(res => {
					if (res.code == 0) {
						this.bannerList = res.data
					}
				});
			},
			// 获取轮播图
			getGridList() {
				let data = {
					classify: 4
				}
				this.$Request.get("/app/banner/selectBannerList", data).then(res => {
					if (res.code == 0) {
						this.gridData = res.data
					}
				});
			},
			// 商户类型
			getShopType() {
				this.$Request.getT('/app/shoptype/selectShopTypeList').then(res => {
					if (res.code == 0) {
						// this.shopTypeList = [...this.shopTypeList ,...res.data]

						res.data.forEach(res => {
							res.select = false
						})
						this.options4 = [...this.options4, ...res.data]
						console.log(this.options4,'+++++++++++++++++++')
					}
				})
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

							ret.shopScore = ret.shopScore.toFixed(1)
							ret.shopLable = ret.shopLable ? ret.shopLable.split(',') : ''
							ret.shopCover = ret.shopCover ? ret.shopCover.split(',') :
								'../../static/logo.png'
							ret.errandTime = Math.round(ret.errandTime)
						})

						if (this.page == 1) {
							this.shopList = res.data.list
						} else {
							this.shopList = [...this.shopList, ...res.data.list]
						}
						console.log(this.shopList)
					}
					uni.stopPullDownRefresh();
					uni.hideLoading()
				})
			},
			goNav(url) {
				console.log(url)
				if (this.userId) {
					if (uni.getStorageSync('sendMsg')) {
						console.log('授权+1')
						wx.requestSubscribeMessage({
							tmplIds: this.arr,
							success(re) {
								console.log(JSON.stringify(re), 111111111111)
								var datas = JSON.stringify(re);
								if (datas.indexOf("accept") != -1) {
									// console.log(re)
								}
							},
							fail: (res) => {
								// console.log(res)
							}
						})
					}
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
				if (uni.getStorageSync('sendMsg')) {
					console.log('授权+1')
					wx.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							console.log(JSON.stringify(re), 111111111111)
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								// console.log(re)
							}
						},
						fail: (res) => {
							// console.log(res)
						}
					})
				}
				this.shop = e
				// console.log(e, '当前店铺')

				// let myDate = new Date();
				// let hours = myDate.getHours();
				// let minute = myDate.getMinutes();
				// let openTime = this.shop.businessHours.split(':')[0] //开始小时
				// let openTime1 = this.shop.businessHours.split(':')[1] //开始分钟
				// let closeTime = this.shop.lockHours.split(':')[0] //结束小时
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
			defaultAddress() {
				if (uni.getStorageSync('city')) {
					this.address = uni.getStorageSync('city')
					this.lng = uni.getStorageSync('lng')
					this.lat = uni.getStorageSync('lat')

					this.page = 1
					this.getShopList()
					return
				}
				let data = {
					page: 1,
					limit: 1000
				}
				this.$Request.get('/app/address/selectAddressList', data).then(res => {
					if (res.code == 0 && res.data.list.length) {

						this.address = res.data.list[0].addressDetail
						this.lng = res.data.list[0].lng
						this.lat = res.data.list[0].lat
						res.data.list.forEach(res => {
							if (res.addressDefault) {
								this.address = res.addressDetail
								this.lng = res.lng
								this.lat = res.lat
							}
						})
						console.log('选择的经纬度', this.lng, this.lat)

					} else {
						let data = {
							lat: this.lat,
							lng: this.lng
						}
						this.$Request.get("/app/address/selectCity", data).then(res => {
							if (res.code == 0) {
								this.address = res.data.city
								// console.log(3333333333)
								// this.getShopList()
							}
						});
					}
					this.page = 1
					this.getShopList()
				})
			},
			goAddress() {
				uni.navigateTo({
					url: '/pages/index/selectCampus'
				})
			},
			// 查看金刚区详情
			goDetail(item) {
				if (uni.getStorageSync('sendMsg')) {
					console.log('授权+1')
					wx.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							console.log(JSON.stringify(re), 111111111111)
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								// console.log(re)
							}
						},
						fail: (res) => {
							// console.log(res)
						}
					})
				}

				let userId = this.$queue.getData('userId');
				if (!userId) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
					return
				}
				//如果是小程序跳转就是设置这个
				if (item.describes) {
					uni.navigateToMiniProgram({
						appId: item.describes,
						path: item.classifyUrl,
						success: res => {
							// 打开成功
							console.log("打开成功", res);
						},
						fail: err => {
							console.log(err);
						}
					})
					return
				}
				uni.navigateTo({
					url: item.url
				});

			},
			// 开启订阅消息
			openMsg() {
				console.log('订阅消息')
				var that = this
				uni.getSetting({
					withSubscriptions: true, //是否获取用户订阅消息的订阅状态，默认false不返回
					success(ret) {
						console.log(ret.subscriptionsSetting, '------------------')
						// if (ret.subscriptionsSetting.itemSettings && Object.keys(ret.subscriptionsSetting.itemSettings).length == 2) {
						if (ret.subscriptionsSetting.itemSettings) {
							uni.setStorageSync('sendMsg', true)
							uni.openSetting({ // 打开设置页 
								success(rea) {
									console.log(rea.authSetting)
								}
							});
						} else { // 用户没有点击“总是保持以上，不再询问”则每次都会调起订阅消息
							console.log(99999)
							uni.setStorageSync('sendMsg', false)
							uni.showModal({
								title: '提示',
								content: '为了更好的体验,请绑定消息推送',
								confirmText: '确定',
								cancelText: '取消',
								success: function(res) {
									if (res.confirm) {
										console.log(that.arr)
										wx.requestSubscribeMessage({
											tmplIds: that.arr,
											success(re) {
												console.log(JSON.stringify(re),
													'++++++++++++++')
												var datas = JSON.stringify(re);
												if (datas.indexOf("accept") != -1) {
													console.log(re)
													uni.setStorageSync('sendMsg', true)
												}
											},
											fail: (res) => {
												console.log(res)
											}
										})
										uni.setStorageSync('sendMsg', true)
										console.log('确认')
										that.showModal = false
									} else if (res.cancel) {
										console.log('取消')
										uni.setStorageSync('sendMsg', false)
										that.showModal = true
									}
								}
							})
						}
					}
				})
			},
			// 跳转商品详情
			goDet(goodsId, shopId) {
				let userId = this.$queue.getData('userId');
				if (!userId) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
					return
				}
				uni.navigateTo({
					url: '/pages/index/shop/goodsDet?goodsId=' + goodsId + '&shopId=' + shopId + '&orderType=2'
				})
			},

		},
		onReachBottom: function() {
			if(this.shopList.length<this.totalCount) {
				this.page = this.page + 1;
				this.getShopList()
			} else {
				// uni.showToast({
				// 	title: '已经到底了',
				// 	icon: 'none'
				// })
			}
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.getShopList();
		},
	}
</script>

<style lang="scss" scoped>
	.select{
		color: #FCD202;
	}
	.search-btn {
		width: 132rpx;
		height: 80rpx;
		font-size: 28rpx;
		text-align: center;
		line-height: 80rpx;
		background: #FCD202;
		border-radius: 36px;
	}

	.address {
		width: 100%;
		background-color: #FCD202;
		// #ifndef H5
		position: fixed;
		top: 0;
		height: 180rpx;
		line-height: 260rpx;
		// #endif
		
		// #ifdef H5
		height: 80rpx;
		line-height: 80rpx;
		// #endif
		padding: 0 34rpx;
		font-size: 32rpx;
		
		z-index: 999;
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

	.tabs {
		margin-bottom: 20rpx;
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

	.page-box {
		// position: relative;
		// z-index: 0;
		// top: 70px;
	}

	.centre {
		margin: auto;
		height: 400rpx;
		text-align: center;
		font-size: 32rpx;
	}

	.centre image {
		width: 387rpx;
		height: 341rpx;
		margin: 40rpx auto 20rpx;
	}

	.centre .tips {
		font-size: 32rpx;
		color: #2F3044;
		margin: 20rpx 0 40rpx;
		font-weight: 700;
	}


	.hintPopul {
		width: 100%;
		height: 100%;
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
	
	.hongbao {
		width: 100%;
		/* height: 100px; */
		/* background: #007AFF; */
		position: fixed;
		top: 40%;
		/* bottom: 50%; */
		left: 0rpx;
		right: 0rpx;
		/* display: none; */
	}
	
	.hb_img {
		width: 100%;
		height: 435rpx;
	}
	
	.hb_btn {
		width: 60%;
		height: 72rpx;
		position: absolute;
		top: 315rpx;
		left: 80rpx;
	}
	
</style>
