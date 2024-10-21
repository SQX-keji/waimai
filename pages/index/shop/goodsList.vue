<template>
	<view>
		<view>
			<view class="food_all">
				<!-- banner -->
				<swiper class="swiper" :autoplay="true" interval="2000" duration="500" :circular="true"
					style="width: 100%;height: 300rpx;">
					<swiper-item v-for="(item,index) in shopDet.shopBanner" :key='index'>
						<image :src="item" mode="scaleToFill" style="width: 100%;"></image>
					</swiper-item>
				</swiper>
				<view class="shop padding">
					<view class="flex justify-between">
						<view style="width: 500rpx;">
							<view class="text-xl text-bold">{{shopDet.shopName}}</view>
							<view class="flex align-center margin-top-xs">
								<view style="color: #FD6416;">
									<u-icon name="star-fill" color="#FD6416" size="28"></u-icon>
									{{shopDet.shopScore?shopDet.shopScore:0}}
								</view>
								<view class="margin-left-xs">销量 {{shopDet.shopSales?shopDet.shopSales:0}}</view>
								<view class="margin-left-xs" v-if="shopDet.errandTime">配送约{{shopDet.errandTime}}分钟</view>
							</view>
							<view class="text-gray margin-top-xs u-line-2">
								公告：{{shopDet.shopNotice?shopDet.shopNotice:'暂无公告'}}</view>
						</view>
						<view>
							<image :src="shopDet.shopCover" class="radius" style="width: 110rpx;height: 110rpx;">
							</image>
						</view>
					</view>
				</view>
				<!-- 食品 -->
				<view class="food">
					<!-- 店名地址 -->
					<view class="food_address">
						<view class="flex flex-sub bg justify-between padding-right-xl">
							<view @click="switchTab(1)" :class="current==1? 'select':''" class="tabBtn">
								<view class="title">全部商品</view>
								<view :class="current==1? 'active1':''"></view>
							</view>
							<view @click="switchTab(2)" :class="current==2? 'select':''" class="tabBtn">
								<view class="title">评价</view>
								<view :class="current==2? 'active1':''"></view>
							</view>
							<view @click="switchTab(3)" :class="current==3? 'select':''" class="tabBtn">
								<view class="title">商家</view>
								<view :class="current==3? 'active1':''"></view>
							</view>
						</view>
						<!-- <view class="flex Switch">
							<view @click="tabSwidth(1)" :class="orderType==1?'selSwitch':''">自取</view>
							<view @click="tabSwidth(2)" :class="orderType==2?'selSwitch':''">外卖</view>
						</view> -->
						<view>
							<image @click="goPindan()" src="../../../static/images/index/pindan.png" style="width: 160rpx;height: 64rpx;" mode=""></image>
						</view>
					</view>
					<view class="VerticalBox" v-if="current==1">
						<scroll-view class="VerticalNav nav bg-gray" scroll-y scroll-with-animation
							:scroll-top="verticalNavTop" style="height:calc(100vh - 590upx)">
							<view style="font-size: 28upx;" class="cu-item text-lg"
								:class="index==tabCur?'cur':'text-gray'" v-for="(item,index) in dataList" :key="index"
								@tap="TabSelect" :data-id="index">
								{{item.classifyName}}
							</view>
						</scroll-view>
						<scroll-view class="VerticalMain" scroll-y scroll-with-animation
							style="height:calc(100vh - 590rpx)" :scroll-into-view="'main-'+mainCur"
							@scroll="VerticalMain">
							<view class="bg-white padding-sm  margin-bottom-sm" v-for="(item,index) in dataList"
								:key="index" :id="'main-'+index">
								<view class="flex justify-between padding-bottom-sm" @click="goDet(ite.goodsId)"
									v-for="(ite,ind) in item.goodsList" :key='ind'>
									<image :src="ite.goodsCover" mode=""
										style="width: 184rpx;height: 184rpx;border-radius: 10rpx;"></image>
									<view
										class="flex-sub margin-left-sm padding-tb-xs flex flex-direction justify-between">
										<view class="text-black text-lg text-bold" style="font-size: 30upx;">
											{{ite.goodsName}}
										</view>
										<view class="text-gray text-sm u-line-2"
											style="overflow: hidden;line-clamp: 2;-webkit-line-clamp: 2;width: 100%;">
											{{ite.goodsSynopsis?ite.goodsSynopsis:''}}
										</view>
										<view class="flex justify-between">
											<view class="text-sm text-red">
												¥ <text class="text-lg">{{ite.goodsMoney}}</text>
											</view>
											<image @click.stop="selSku(ite)" src="../../../static/images/index/add.png"
												mode="" style="width: 40rpx;height: 40rpx"></image>
										</view>
									</view>
								</view>
							</view>
						</scroll-view>
						<empty v-if="!dataList.length"></empty>
					</view>
					<view class="" v-if="current==2">
						<view class="flex margin-lr padding-tb-sm u-border-bottom" v-if="shopDet.shopScore">
							<view class="flex padding-right">
								<view class="text-sl" style="color: #FD6416;">{{shopDet.shopScore}}</view>
								<view class="flex flex-direction justify-around margin-left-sm">
									<view>商家评分</view>
									<view class="flex">
										<u-icon v-for="ite in shopDet.shopScore1" :key='ite' color="#FCD202"
											name="star-fill"></u-icon>
									</view>
								</view>
							</view>
							<view class="flex-sub flex justify-around u-border-left padding-left">
								<view class="flex flex-direction justify-around text-center">
									<view>好评</view>
									<view>{{EvaluateData.goodReputation}}</view>
								</view>
								<view class="flex flex-direction justify-around text-center">
									<view>中评</view>
									<view>{{EvaluateData.mediumReview}}</view>
								</view>
								<view class="flex flex-direction justify-around text-center">
									<view>差评</view>
									<view>{{EvaluateData.negativeComment}}</view>
								</view>
							</view>
						</view>
						<view class="padding-tb-sm margin-lr">
							<u-button hover-class='none' @click="sel(0)" type="primary" shape="circle" size="mini"
								:plain="false" :custom-style="count==0?customStyle:customStyle1">全部评论</u-button>
							<u-button hover-class='none' @click="sel(1)" type="primary" shape="circle" size="mini"
								:plain="false" :custom-style="count==1?customStyle:customStyle1">
								好评({{EvaluateData.goodReputation}})</u-button>
							<u-button hover-class='none' @click="sel(2)" type="primary" shape="circle" size="mini"
								:plain="false" :custom-style="count==2?customStyle:customStyle1">
								中评({{EvaluateData.mediumReview}})</u-button>
							<u-button hover-class='none' @click="sel(3)" type="primary" shape="circle" size="mini"
								:plain="false" :custom-style="count==3?customStyle:customStyle1">
								差评({{EvaluateData.negativeComment}})</u-button>
						</view>
						<view class="padding-tb-sm margin-lr u-border-bottom" v-for="(item, index) in EvaluateList"
							:key='index'>
							<view class="flex justify-between align-center">
								<view class="flex align-center">
									<u-avatar :src="item.avatar" size="65"></u-avatar>
									<view class=" margin-left-sm" style="line-height: 46upx;">{{item.nickName}}</view>
									<view class="flex margin-left-sm">
										<u-icon v-for="ite in item.score" :key='ite' color="#FCD202" name="star-fill">
										</u-icon>
									</view>
								</view>

								<view>{{item.createTime}}</view>
							</view>
							<view class="margin-top-sm">{{item.evaluateMessage}}</view>
						</view>
						<empty v-if="!EvaluateList.length"></empty>
					</view>
					<view class="margin-lr padding-bottom" v-if="current==3">
						<view class="flex align-center">
							<view class="flex-sub">
								<view class="text-black text-lg text-bold">{{shopDet.shopName}}</view>
								<view class="margin-top-sm" @click="goMap">
									<u-icon name="map" size="28"></u-icon> {{shopDet.detailedAddress}}
								</view>
							</view>
							<image @click="call(shopDet.phone)" src="../../../static/images/index/phone.png"
								class="radius" style="width: 38rpx;height: 49rpx;">
							</image>
						</view>
						<image :src="shopDet.shopCover" class="radius margin-top-sm"
							style="width: 140rpx;height: 140rpx;">
						</image>
						<view class="text-black text-lg text-bold margin-top-xs">商家信息</view>
						<view class="text-black margin-top-xs text-df">
							营业时间：{{shopDet.businessHours}}-{{shopDet.lockHours}}</view>
						<view class="text-black margin-top-xs text-df">商家品类：{{shopDet.shopTypeName}}</view>
						<view class="text-black margin-top-xs text-df">配送范围：{{shopDet.distributionDistance}}以内</view>
						<view class="text-black margin-top-xs text-df">公告：{{shopDet.shopNotice}}</view>
					</view>
				</view>
				<!-- 结算 -->
				<view class="settlement" @click="isPopupShow" v-if="current==1">
					<view class="settlement_img">
						<image src="../../../static/images/index/diancan.png" mode=""></image>
						<view class="settlement_hot">{{goodsNum}}</view>
					</view>
					<view class="settlement_le">
						<text>￥</text>{{totalPrice}}
					</view>
					<view class="settlement_ri" @click.stop="goConfirm()">去结算</view>
				</view>
				<!-- 购物车弹窗 -->
				<u-popup v-model="popupShow" mode="bottom" border-radius="20">
					<view class="padding">
						<view class="flex justify-between align-center margin-bottom-sm">
							<view class="text-bold text-black text-df">已选餐品</view>
							<view class="flex align-center">
								<image src="../../../static/images/index/delete.png" style="width: 28rpx;height: 31rpx;"
									mode=""></image>
								<text class="margin-left-xs" @click="empty">清空购物车</text>
							</view>
						</view>
						<scroll-view scroll-y='true' class="popup">
							<view v-for="(item,index) in goodsList.orderGoodsList[0]" :key='inedx'>
								<view class="flex align-center margin-tb-sm">
									<image :src="item.goodsPicture[0]"
										style="width: 96rpx;height: 96rpx;border-radius: 10rpx;">
									</image>
									<view class="margin-left-sm">
										<view>{{item.goodsName}}</view>
										<view>{{item.skuMessage}}</view>
									</view>
								</view>
								<view class="flex justify-between align-center">
									<view class="text-bold text-sm">¥<text class="text-lg">{{item.goodsPrice}}</text>
									</view>
									<view class="flex align-center justify-between">
										<view @click.stop="noAdd(item,index)">
											<image src="../../../static/images/index/jian.png"
												style="width: 54rpx;height: 54rpx;"></image>
										</view>
										<view class="text-center margin-lr-xs">{{item.goodsNum}}</view>
										<view @click="add(item,index)">
											<image src="../../../static/images/index/add.png"
												style="width: 50rpx;height: 50rpx;"></image>
										</view>
									</view>
								</view>
							</view>
						</scroll-view>
					</view>
					<view class="settlement1 margin-top-lg">
						<view class="settlement_img">
							<image src="../../../static/images/index/diancan.png" mode=""></image>
							<view class="settlement_hot">{{goodsNum}}</view>
						</view>
						<view class="settlement_le">
							<text>￥</text>{{totalPrice}}
						</view>
						<view class="settlement_ri" @click.stop="goConfirm()">去结算</view>
					</view>
				</u-popup>
				<!-- 选择规格弹窗 -->
				<u-popup v-model="skuShow" mode="center" :closeable='true' border-radius="20">
					<view style="width: 700rpx;">
						<view class="text-center text-lg text-bold padding-tb">{{goodsDet.goodsName}}</view>
						<view class="margin-top-sm padding-lr" v-for="(item,index) in attrList" :key="index">
							<view class="text-bold text-black">{{item.value}}</view>
							<view class="flex margin-tb-sm flex-wrap">
								<view v-for="(ite, ind) in item.detail" :key="ind" @click="skuSel(ite,index,ind)"
									class="margin-bottom-sm">
									<view class="skuBtn"
										:class="item.goodsId == index && item.attrId == ind?'active': ''">
										{{ite.name}}
									</view>
								</view>
							</view>
						</view>
						<view class="flex justify-between padding-lr padding-top">
							<view>
								<view class="text-xl text-bold text-black"><text class="text-sm">￥</text>{{price}}
								</view>
								<view class="detail_describe_text2">{{checkString}}</view>
							</view>
							<u-number-box v-model="value" min="1" @change="valChange"></u-number-box>
						</view>
						<view class="detail_account_bottom padding">
							<view class="detail_account_bottom_le" @click="payment()">立即购买</view>
							<view class="detail_account_bottom_ri" @click="orderSel(2)">加入购物车</view>
						</view>
					</view>
				</u-popup>
			</view>


			<u-skeleton :loading="loading" :animation="true" bgColor="#FFF"></u-skeleton>
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
				customStyle: {
					color: '#333333',
					background: '#FCD202',
					marginRight: '20rpx',
					border: 0
				},
				customStyle1: {
					color: '#333333',
					background: '#F2F2F2',
					marginRight: '20rpx',
					border: 0
				},
				current: 1,
				loading: false, // 是否显示骨架屏组件
				tabCur: 0,
				mainCur: 0,
				verticalNavTop: 0,
				load: true,
				dataList: [],
				shopDet: {},
				shopList: [],
				city: '西安',
				lat: '',
				lng: '',
				shopName: '',
				page: 1,
				limit: 10,
				evaluatePage: 1,
				evaluateLimit: 10,
				show: true,
				totalPrice: 0,
				goodsNum: 0,
				goodsList: [],
				popupShow: false,
				skuShow: false,

				userId: '',
				hintShow: false, //
				shouquan: 0,
				orderType: 2,

				value: 1,
				goodsDet: {},
				skuId: '',
				skuMessage: '',
				skuList: [],
				attrList: [],
				checkString: '',
				checkStateList: [],
				CheckattrValue: false,
				price: 0,
				echoSkuList: [],
				echoSku: [],
				isEchoSku: {},
				shopId: '',
				lng: '',
				lat: '',
				commentList: [],
				EvaluateList: [],
				EvaluateData: {},
				grade: '',
				count: 0,
				orderId: ''
			}
		},
		onLoad(option) {
			let that = this
			that.shopId = option.shopId
			that.orderId = uni.getStorageSync('orderId')?uni.getStorageSync('orderId'):''
			console.log('店铺id', option.shopId)
			uni.getLocation({
				type: 'gcj02', //返回可以用于uni.openLocation的经纬度
				success: function(res) {
					that.lat = res.latitude;
					that.lng = res.longitude;
					that.getData()
				}
			});
		},
		onShow() {
			this.userId = uni.getStorageSync('userId')
			this.getOrderList()

		},
		onShareAppMessage(res) { //发送给朋友
			return {
				title: this.shopDet.shopName,
				path: '/pages/index/index?shopId=' + this.shopDet.shopId,
				imageUrl: this.shopDet.shopCover,
			}
		},
		onShareTimeline(res) { //分享到朋友圈
			return {
				title: this.shopDet.shopName,
				path: '/pages/index/index?shopId=' + this.shopDet.shopId,
				imageUrl: this.shopDet.shopCover,
			}
		},
		methods: {
			goNav(url) {
				uni.navigateTo({
					url
				})
			},
			switchTab(e) {
				// this.page = 1
				this.current = e

				if (this.current == 1) {
					this.getData()
				} else if (this.current == 2) {
					this.getEvaluateList()
				} else if (this.current == 3) {

				}
			},
			sel(e) {
				this.grade = e
				this.count = e
				this.evaluatePage = 1
				this.getEvaluateList()
			},
			// 获取评论列表
			getEvaluateList() {
				let data = {
					shopId: this.shopId,
					page: this.evaluatePage,
					limit: this.evaluateLimit,
					grade: this.grade
				}
				this.$Request.get("/app/goods/selectEvaluateByShopId", data).then(res => {
					if (res.code == 0 && res.data) {
						this.EvaluateData = res.data
						if (this.evaluatePage == 1) {
							this.EvaluateList = res.data.pageUtils.list
						} else {
							this.EvaluateList = [...this.EvaluateList, ...res.data.pageUtils.list]
						}
					}
				});
			},
			// 获取店铺信息
			getData() {
				let data = {
					shopId: this.shopId,
					lng: this.lng,
					lat: this.lat
				}
				this.$Request.get("/app/goods/selectGoodsList", data).then(res => {
					if (res.code == 0 && res.data) {
						this.dataList = res.data.list
						this.shopDet = res.data.goodsShop

						for (var i = 0; i < this.dataList.length; i++) {
							this.dataList[i].id = i
						}

						this.shopDet.shopScore1 = Math.floor(this.shopDet.shopScore)
						this.shopDet.errandTime = Math.round(this.shopDet.errandTime)
						this.shopDet.shopBanner = this.shopDet.shopBanner.split(',')
						this.shopDet.shopScore = this.shopDet.shopScore.toFixed(2)
						if (this.shopDet.distributionDistance < 1000) {
							this.shopDet.distributionDistance = '<1000m'
						} else {
							this.shopDet.distributionDistance = Number(this.shopDet.distributionDistance / 1000) +
								'km'
						}

						uni.setNavigationBarTitle({
							title: this.shopDet.shopName
						})
					}
				});
			},
			getEchoOrder() {
				let data = {
					shopId: this.shopId,
					goodsId: this.goodsDet.goodsId
				}
				this.$Request.get("/app/order/echoOrder", data).then(res => {
					if (res.code == 0 && res.data.length) {
						this.echoSkuList = res.data
						this.echoSku = res.data[0].skuMessage.split(',')
					}
				});
			},
			// 弹窗展示商品详情
			selSku(item) {
				console.log(item)
				this.checkStateList = []
				this.checkString = ''
				this.$Request.get("/app/goods/selectGoodsById?goodsId=" + item.goodsId).then(res => {
					uni.hideLoading()
					if (res.code == 0) {
						this.goodsDet = res.data
						this.goodsDet.goodsLabel = this.goodsDet.goodsLabel.split(',')
						this.goodsDet.goodsPicture = this.goodsDet.goodsPicture.split(',')
						console.log(this.goodsDet.goodsPicture)
						this.price = this.goodsDet.goodsMoney
						this.skuList = this.goodsDet.sku
						if (this.goodsDet.attr.length) {
							this.attrList = this.goodsDet.attr[0] ? this.goodsDet.attr[0].attrValue : []
						} else {
							this.skuId = this.goodsDet.sku[0].id
						}

						console.log(this.attrList)
						this.attrList.forEach(res => {
							let data = {
								name: ''
							}
							this.checkStateList.push(data);
							let detail = [];
							res.detail.split(',').forEach(d => {
								let data = {
									name: '',
									state: ''
								}
								data.name = d;
								detail.push(data);
							});
							res.detail = detail;
						})
						this.getEchoOrder()
						this.skuShow = true
					}
				});

			},
			// 选择规格
			skuSel(item, index, ind) {
				console.log(item, index, ind)
				this.attrList[index].goodsId = index
				this.attrList[index].attrId = ind
				this.checkStateList[index].name = item.name;
				this.checkString = '';

				this.checkStateList.forEach(d => {
					if (d.name) {
						if (this.checkString) {
							this.checkString = this.checkString + ',' + d.name;
						} else {
							this.checkString = d.name;
						}
					}
				});
				console.log(this.skuList)
				for (var i = 0; i < this.skuList.length; i++) {
					let d = this.skuList[i];
					if (d.detailJson == this.checkString) {
						console.log(d.detailJson, this.checkString)
						// if (d.stock > 0) {
						this.skuId = d.id
						this.skuMessage = d.detailJson
						// this.price = d.skuPrice
						this.CheckattrValue = true;
						// } else {
						// 	this.$queue.showToast('库存不足请选择其他规格')
						// }

						break
					} else {
						this.CheckattrValue = false;
					}
				}
			},
			valChange(e) {
				console.log('当前值为: ' + e.value)
				this.value = e.value
			},
			// 立即购买
			payment() {
				if (!this.userId) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
					return
				}
				if (this.attrList.length) {
					if (this.checkString == '' || !this.CheckattrValue) {
						this.$queue.showToast('请选择正确的商品规格');
						return;
					}
				}
				let data = {
					goodsId: this.goodsDet.goodsId,
					skuMessage: this.skuMessage,
					skuId: this.skuId,
					num: this.value,
					orderType: this.orderType,
					shopId: this.shopId,
				}
				this.$Request.post("/app/order/buyGoods", data).then(res => {
					if (res.code == 0) {
						this.skuShow = false
						uni.navigateTo({
							url: '/pages/index/shop/payOrder?orderId=' + res.data + '&orderType=' + this
								.orderType
						})

					} else {
						this.$queue.showToast(res.msg);
					}
				})
			},
			// 加入购物车
			orderSel(e) {
				if (!this.userId) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
					return
				}
				if (this.attrList.length) {
					if (this.checkString == '' || !this.CheckattrValue) {
						this.$queue.showToast('请选择正确的商品规格');
						return;
					}
				}
				console.log(this.goodsDet)
				console.log(this.echoSku, 'echoSku')
				if (this.echoSku.length) {
					this.echoSkuList.forEach(res => {
						if (res.skuMessage == this.skuMessage) {
							this.isEchoSku = res
						}
					})
					if (JSON.stringify(this.isEchoSku) != "{}") {
						let data = {
							orderGoodsId: this.isEchoSku.id,
							type: 1,
							num: this.value,
							shopId: this.shopId,
						}

						this.$Request.get("/app/order/updateGoodsNum", data).then(res => {
							if (res.code == 0) {
								// setTimeout(function() {

								this.skuShow = false
								this.getOrderList()
								// },1000)
							} else {
								this.$queue.showToast(res.msg);
							}
						});
						return
					}
				}

				let data = {
					goodsId: this.goodsDet.goodsId,
					skuMessage: this.skuMessage,
					skuId: this.skuId,
					num: this.value,
					shopId: this.shopId,
					orderId: this.orderId
				}

				this.$Request.post("/app/order/joinOrder", data).then(res => {
					if (res.code == 0) {
						this.$queue.showToast('添加成功');
						if (e == 1) {
							uni.navigateTo({
								url: '/pages/diancan/confirmOrder?shopId=' + this.shopId
							})
						} else {
							// setTimeout(function() {
							this.skuShow = false
							this.getOrderList()
							// },1000)
						}
					} else {
						this.$queue.showToast(res.msg);
					}
				})
			},
			// 切换外卖/自提
			tabSwidth(e) {
				this.orderType = e
				this.getOrderList()
			},
			// 展示购物车弹窗
			isPopupShow() {
				// console.log(this.goodsList.orderGoodsList[0])
				if (this.goodsList && this.goodsList.orderGoodsList[0].length > 0) {
					this.popupShow = true
				} else {
					uni.showToast({
						title: '请先添加商品',
						icon: "none"
					})
				}
			},
			// 添加数量
			add(item, index) {
				// this.count++;
				console.log(item, index)
				this.goodsList.orderGoodsList[0][index].goodsNum++

				let data = {
					orderGoodsId: this.goodsList.orderGoodsList[0][index].id,
					type: 1,
					num: 1,
					shopId: this.shopId
				}
				this.$Request.get("/app/order/updateGoodsNum", data).then(res => {
					if (res.code == 0) {
						this.getOrderList()
					} else {
						this.$queue.showToast(res.msg);
						this.goodsList.orderGoodsList[0][index].goodsNum--
					}
				});
			},
			// 减少数量
			noAdd(item, index) {
				console.log(item, index)
				this.goodsList.orderGoodsList[0][index].goodsNum--
				// this.count--;
				let data = {
					orderGoodsId: this.goodsList.orderGoodsList[0][index].id,
					type: 2,
					num: 1,
					shopId: this.shopId
				}
				this.$Request.get("/app/order/updateGoodsNum", data).then(res => {
					if (res.code == 0) {
						this.getOrderList()
					}
				});
			},
			// 清空购物车
			empty() {
				let data = {
					shopId: this.shopId,
				}
				this.$Request.post("/app/order/emptyShoppingTrolley", data).then(res => {
					if (res.code == 0) {
						this.popupShow = false
						this.goodsList = []
						this.echoSkuList = []
						this.echoSku = []
						this.getOrderList()
						this.getEchoOrder()
					}
				});
			},
			// 获取购物车商品列表
			getOrderList() {
				let data = {
					shopId: this.shopId,
					page: 1,
					limit: 1000,
					status: 1,
					// orderType: this.orderType
				}
				this.$Request.get("/app/order/selectAllOrderList", data).then(res => {
					if (res.code == 0) {
						this.goodsList = res.data.pageUtils.list[0]
						if (this.goodsList) {
							console.log(this.goodsList)
							this.goodsList.orderGoodsList[0].forEach(res => {
								res.goodsPicture = res.goodsPicture.split(',')
							})
							if (!this.goodsList.orderGoodsList[0].length) {
								this.popupShow = false
							}
						}

						this.totalPrice = res.data.money
						this.goodsNum = 0

						if (this.goodsList && this.goodsList.orderGoodsList[0]) {
							this.goodsList.orderGoodsList[0].forEach(res => {
								this.goodsNum += res.goodsNum
							})
						}
					}
				});
			},
			// 跳转商品详情
			goDet(id) {
				uni.navigateTo({
					url: '/pages/index/shop/goodsDet?goodsId=' + id + '&shopId=' + this.shopId + '&orderType=' +
						this.orderType + '&orderId=' + this.orderId
				})
			},
			// 去结算
			goConfirm() {
				if (!this.userId) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
					return
				}
				console.log(this.goodsList)
				if (this.goodsList && this.goodsList.orderGoodsList[0].length > 0) {
					uni.navigateTo({
						url: '/pages/index/shop/confirmOrder?shopId=' + this.shopId + '&orderType=' + this
							.orderType
					})
				} else {
					uni.showToast({
						title: '请先添加商品',
						icon: "none"
					})
				}

			},
			TabSelect(e) {
				console.log(e.currentTarget.dataset.id)
				this.tabCur = e.currentTarget.dataset.id;
				this.mainCur = e.currentTarget.dataset.id;
				this.verticalNavTop = (e.currentTarget.dataset.id - 1) * 50
			},
			VerticalMain(e) {
				let that = this;
				let tabHeight = 0;
				if (this.load) {
					for (let i = 0; i < this.dataList.length; i++) {
						let view = uni.createSelectorQuery().select("#main-" + this.dataList[i].id);
						view.fields({
							size: true
						}, data => {
							this.dataList[i].top = tabHeight;
							tabHeight = tabHeight + data.height;
							this.dataList[i].bottom = tabHeight;
						}).exec();
					}
					this.load = false
				}
				let scrollTop = e.detail.scrollTop;
				// console.log(scrollTop,'111')
				for (let i = 0; i < this.dataList.length; i++) {
					if (scrollTop > this.dataList[i].top && scrollTop < this.dataList[i].bottom) {
						this.verticalNavTop = (this.dataList[i].id - 1) * 50
						this.tabCur = this.dataList[i].id
						console.log(scrollTop) //不能删除
						return false
					}
				}
			},
			// 打电话
			call(e) {
				uni.makePhoneCall({
					phoneNumber: e
				});
			},
			// 点击调起地图查看位置
			goMap() {
				//查看位置需要传经纬度才能执行
				let that = this
				uni.authorize({
					scope: 'scope.userLocation',
					success(res) {
						uni.openLocation({
							latitude: that.shopDet.shopLat,
							longitude: that.shopDet.shopLng,
							success: function() {}
						});
					},
					fail(err) {

					}
				})
			},
			goPindan() {
				if (!this.userId) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
					return
				}
				if(this.orderId) {
					uni.navigateTo({
						url: '/pages/index/shop/pindanDet?shopId=' + this.shopId +'&orderId=' + this.orderId
					})
				} else {
					let data = {
						shopId: this.shopId,
					}
					this.$Request.post("/app/order/shareTheBill", data).then(res => {
						if (res.code == 0) {
							uni.navigateTo({
								url: '/pages/index/shop/pindanDet?shopId=' + this.shopId +'&orderId=' + res.data
							})
						}
					});
				}
			}
		},
		onReachBottom: function() {
			if (this.current == 2) {
				this.evaluatePage = this.evaluatePage + 1;
				this.getEvaluateList()
			}
		},
	}
</script>

<style scoped lang="scss">
	page {
		background-color: #FFFFFF;
	}

	.bg1 {
		background: #FFFFFF;
	}

	.sticky-tabs {
		z-index: 990;
		position: sticky;
		top: var(--window-top);
		// background-color: #fff;
	}

	/* // 使用mescroll-uni,则top为0 */
	.mescroll-uni,
	/deep/.mescroll-uni {
		.sticky-tabs {
			top: 0;
		}
	}

	.shop {
		position: absolute;
		top: 150rpx;
		left: 0;
		right: 0;
		width: 686rpx;
		margin: auto;
		background: #FFFFFF;
		border-radius: 20rpx;
		box-shadow: 0 0 20rpx #CCCCCC;
	}

	.skuBtn {
		padding: 10rpx 30rpx;
		border-radius: 10rpx;
		text-align: center;
		margin: 10rpx 20rpx;
		font-size: 28rpx;
		color: #333333;
		border: 2px solid #F2F2F2;
	}

	.active {
		background: rgba(252, 210, 2, 0.2) !important;
		border: 2px solid #FCD202 !important;
		opacity: 0.6;
	}

	.active1 {
		/* width: 82rpx; */
		height: 16rpx;
		background: #FCD202;
		position: relative;
		top: -10rpx;
		z-index: 9;
	}

	.popup {
		/* height: 500rpx; */
		max-height: 500rpx;
		/* overflow-y: auto; */
	}

	.tabBtn {
		/* background-color: #f6f6fa; */
		height: 60rpx;
		line-height: 60rpx;
		color: #999999;
		font-size: 38rpx;
	}

	.Switch {
		width: 164rpx;
		height: 62rpx;
		color: #FFFFFF;
		background: #FCD202;
		border-radius: 30rpx;
		display: flex;
		align-items: center;
		padding: 2rpx;
	}

	.Switch>view {
		/* flex: 1; */
		width: 80rpx;
		text-align: center;
		line-height: 62rpx;
		height: 58rpx;
	}

	.selSwitch {

		color: #333333;
		background: #FFFFFF;
		border-radius: 30rpx;
	}

	.hintPopul {
		width: 100%;
		height: 100vh;

		position: absolute;
		top: 0;
		background: rgba(0, 0, 0, .4);
	}

	.content {
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

	.content image {
		position: absolute;
		top: -50rpx;
		left: 0;
		right: 0;
		margin: auto;
	}

	.hintText {
		font-size: 30rpx;

	}


	.VerticalNav.nav {
		width: 200upx;
		white-space: initial;
	}

	.VerticalNav.nav .cu-item {
		width: 100%;
		text-align: center;
		background-color: #f1f1f1;
		margin: 0;
		border: none;
		height: 50px;
		position: relative;
	}

	.VerticalNav.nav .cu-item.cur {
		background-color: #fff;
	}


	.VerticalBox {
		display: flex;
	}

	.VerticalMain {
		background-color: #f1f1f1;
		flex: 1;
	}

	.detail_describe_text2 {
		font-weight: 500;
		margin-top: 2%;
		color: #999999;
	}

	.detail_account_bottom {
		margin-top: 20rpx;
		width: 100%;
		display: flex;
		justify-content: space-between;
		/* margin: 4% 0 3%; */
	}

	.detail_account_bottom_le {
		width: 47.5%;
		/* margin-right: 5%; */
		border-radius: 44rpx;
		text-align: center;
		line-height: 2.8;
		border: 2rpx solid #FCD202;
	}

	.detail_account_bottom_ri {
		width: 47.5%;
		border-radius: 44rpx;
		text-align: center;
		line-height: 2.8;
		background-color: #FCD202;
	}

	.food_all {
		position: relative;
	}

	.text-through {
		text-decoration: line-through
	}

	/* 食物 */
	.food {
		width: 100%;
		overflow: hidden;
		// position: absolute;
		// top: 350rpx;
		background-color: #FFFFFF;
		border-top-left-radius: 18rpx;
		border-top-right-radius: 18rpx;
	}

	.food_address {
		margin: 3%;
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-top: 120rpx;
	}

	.food_address_le {
		width: 75%;
	}

	.food_address_le_top {
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.food_address_le_top_le {
		width: 40rpx;
		height: 37rpx;
	}

	.food_address_le_top_le image {
		width: 40rpx;
		height: 37rpx;
	}

	.food_address_le_top_ce {
		line-height: 1.6;
		font-size: 38rpx;
		font-weight: 600;
		color: #333333;
	}

	.food_address_le_top_ce_ri {
		width: 14rpx;
		/* margin: 1% 0 0 2%; */
		height: 24rpx;
		margin-left: 2%;
	}

	.food_address_le_top_ce_ri image {
		width: 14rpx;
		height: 24rpx;
	}

	.food_address_text {
		font-weight: 500;
		font-size: 24rpx;
		color: #999999;
	}

	.food_address_ri {
		width: 24%;
		height: 55rpx;
		display: flex;
		padding: 0.5% 1%;
		background-color: #FCD202;
		border-radius: 31rpx;
		color: #FFFFFF;
	}

	.food_address_ri_sty {
		flex: 1;
		text-align: center;
		padding-top: 4%;
		font-size: 24rpx;
	}

	.food_address_ri_sty_active {
		width: 100%;
		overflow: hidden;
		background-color: #FFFFFF;
		border-radius: 31rpx;
		color: #333333;
		text-align: center;
	}

	/* 结算 */
	.settlement {
		width: 94%;
		background-color: #000000;
		line-height: 3.4;
		border-radius: 49rpx;
		position: fixed;
		bottom: 10rpx;
		left: 3%;
		display: flex;
		justify-content: space-between;
	}

	.settlement1 {
		width: 94%;
		background-color: #000000;
		line-height: 2.8;
		border-radius: 49rpx;
		position: relative;
		/* bottom: 10rpx; */
		left: 0;
		right: 0;
		/* left: 3%; */
		display: flex;
		justify-content: space-between;
		margin: 20rpx auto;
	}

	.settlement_le {
		width: 45%;
		padding-left: 20%;
		color: #FFFFFF;
		font-size: 30rpx;
	}

	.settlement_le text {
		font-size: 22rpx;
	}

	.settlement_ri {
		width: 35%;
		background-color: #FCD202;
		font-family: PingFang SC;
		font-weight: 800;
		color: #333333;
		text-align: center;
		border-top-right-radius: 49rpx;
		border-bottom-right-radius: 49rpx;
	}

	.settlement_img {
		width: 91rpx;
		height: 96rpx;
		position: absolute;
		// bottom: 30rpx;
		left: 5%;
	}

	.settlement_img image {
		width: 74rpx;
		height: 81rpx;
	}

	.settlement_hot {
		width: 35rpx;
		height: 35rpx;
		line-height: 35rpx;
		text-align: center;
		border-radius: 50%;
		position: absolute;
		top: -10rpx;
		right: 0;
		background-color: #FF130A;
		color: #FFFFFF;
		font-size: 20rpx;
		font-weight: bold;
		color: #FFFFFF;
	}


	.select_all {
		width: 100%;
		position: relative;
	}

	/* 餐厅 */
	.select {
		color: #000000;
		font-weight: bold;
		background-color: #fff;
		z-index: 10;
	}

	.select_line {
		width: 15%;
		height: 6rpx;
		margin: 0 auto 4%;
		background: #E6E6E6;
		border-radius: 4rpx;
	}

	.select_search {
		width: 100%;
		margin: 2% 0;
		display: flex;
		align-items: center;
	}

	.select_search_le {
		/* width: 10%; */
		font-size: 30rpx;
		line-height: 2.5;
		margin-right: 1%;
	}

	.select_search_ce {
		/* width: 5%; */
		/* margin-top: 1%; */
		margin-right: 20rpx;
		margin-left: 6rpx;
		/* margin: 0 20rpx; */
		width: 20rpx;
		height: 10rpx;
	}

	.select_search_ri {
		/* width: 84%; */
		height: 72rpx;
		flex: 1;
		display: flex;
	}

	.select_search_ri input {
		flex: 1;
		/* width: 100%; */
		height: 72rpx;
		background: #F5F5F5;
		color: #999999;
		border-radius: 36rpx;
		text-decoration: 42rpx;
		text-align: center;
	}

	/* 附近 */
	.nearby {
		width: 100%;
		position: relative;
	}

	.nearby_text {
		width: 18%;
		font-size: 30rpx;
		font-weight: 800;
		color: #333333;
		text-align: center;
		border-bottom: 16rpx solid #FCD202;
		/* line-height: 20rpx; */
		margin-top: 10rpx;
	}

	.nearby_address_active {
		border: 2rpx solid #FCD202;
		width: 100%;
		padding: 3%;
		margin-top: 3%;
		border-radius: 10upx;
		display: flex;
	}

	.nearby_address {
		width: 100%;
		padding: 3%;
		margin-top: 3%;
		border-radius: 10upx;
		display: flex;
	}

	.nearby_address_le {
		flex: 1;
	}

	.nearby_address_ri {
		line-height: 2.5;
		text-align: right;
		/* flex: 1; */
	}

	.nearby_text2 {

		/* line-height: 1.5; */
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.nearby_text2_ {
		font-size: 30rpx;
		font-weight: 800;
		color: #333333;
	}

	.nearby_text3 {
		font-size: 24rpx;
		font-weight: 500;
		color: #999999;
		margin-top: 2%;
	}

	/* 添加地址 */
	.goorder {
		width: 100%;
		padding: 2% 3%;
		position: fixed;
		bottom: 0;
		background-color: #FFFFFF;
		border-top: 1rpx solid #999999;
	}

	.goorder_but {
		width: 100%;
		line-height: 2.5;
		text-align: center;
		background: #FCD202;
		border-radius: 36rpx;
	}
</style>
