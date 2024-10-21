<template>
	<view class="detail_all pages">
		<!-- banner -->
		<view class="detail-banner">
			<!-- <image :src="goodsDet.goodsPicture" mode=""></image> -->
			<swiper class="swiper" :autoplay="true" interval="2000" duration="500" :circular="true"
				style="width: 100%;height: 410rpx;">
				<swiper-item v-for="(item,index) in goodsDet.goodsPicture" :key='index'>
					<image :src="item" mode="scaleToFill" style="width: 100%;"></image>
				</swiper-item>
			</swiper>
		</view>
		<!-- 详情 -->
		<view class="detail">
			<view class="detail_text">{{goodsDet.goodsName}}</view>
			<view class="detail_biao" v-if="goodsDet.goodsLabel.length">
				<view class="detail_biao_sty" v-for="(ite, ind) in goodsDet.goodsLabel" :key='ind'>{{ite}}</view>
			</view>
			<view class="margin-top" v-for="(item,index) in attrList" :key="index">
				<view class="text-bold text-black">{{item.value}}</view>
				<view class="flex margin-tb-sm flex-wrap">
					<view v-for="(ite, ind) in item.detail" :key="ind" @click="skuSel(ite,index,ind)"
						class="margin-bottom-sm">
						<view class="btn" :class="item.goodsId == index && item.attrId == ind?'active': ''">
							{{ite.name}}
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 产品描述 -->
		<view class="detail_describe">
			<view class="detail_describe_ce">
				<view class="detail_describe_text">产品描述</view>
				<u-parse :html="goodsDet.goodsDescribe"></u-parse>
			</view>
		</view>
		<view class="detail_describe" v-if="goodsDet.goodsParticularsPicture.length">
			<view class="detail_describe_ce">
				<view class="detail_describe_text">商品详情图</view>
				<image v-for="(item,index) in goodsDet.goodsParticularsPicture" :key='index' :src="item" style="margin: 0 auto;" mode=""></image>
			</view>
		</view>
		<view style="height: 180rpx;"></view>
		<!-- 加购 -->
		<view class="detail_account">
			<view class="detail_account_top">
				<view class="detail_account_top_le">
					<view class="detail_account_text"><text>￥</text>{{price}}</view>
					<view class="detail_describe_text2">{{checkString}}</view>
				</view>
				<!-- <u-number-box v-model="value" min="1" @change="valChange"></u-number-box> -->
			</view>
			<view class="detail_account_bottom">
				<view class="detail_account_bottom_le" @click="payment()">立即购买</view>
				<view class="detail_account_bottom_ri" @click="orderSel(2)">加入购物车</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				goodsId: '',
				goodsDet: {},
				skuId: '',
				skuMessage: '',
				skuList: [],
				attrList: [],
				checkString: '',
				checkStateList: [],
				CheckattrValue: false,
				userId: '',
				value: 1,
				shopId: '',
				price: 0,
				echoSkuList: [],
				echoSku: [],
				isEchoSku: {},
				orderType: 1,
				orderId: ''
			}
		},
		onLoad(option) {
			console.log(option)
			this.userId = uni.getStorageSync('userId')
			this.goodsId = option.goodsId
			this.shopId = option.shopId
			this.orderId = option.orderId?option.orderId: ''
			this.orderType = option.orderType?option.orderType:1
			uni.showLoading({
				title: '加载中...'
			})
			this.getData()
			this.getEchoOrder()
		},
		onShow() {
			this.userId = uni.getStorageSync('userId')
		},
		methods: {
			getEchoOrder() {
				let data = {
					shopId: this.shopId,
					goodsId: this.goodsId
				}
				this.$Request.get("/app/order/echoOrder", data).then(res => {
					if (res.code == 0 && res.data) {
						this.echoSkuList = res.data
						this.echoSku = res.data[0].skuMessage.split(',')
					}
				});
			},
			valChange(e) {
				console.log('当前值为: ' + e.value)
				this.value = e.value
			},
			// 立即购买
			payment() {
				let token = uni.getStorageSync('token')
				if (!token) {
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
								setTimeout(function() {
									uni.navigateBack()
								}, 1000)
							} else {
								this.$queue.showToast(res.msg);
							}
						});
						return
					}
				}
				if(this.orderId) {
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
								this.skuShow = false
								this.getOrderList()
							}
						} else {
							this.$queue.showToast(res.msg);
						}
					})
				} else {
					let data = {
						goodsId: this.goodsDet.goodsId,
						skuMessage: this.skuMessage,
						skuId: this.skuId,
						num: this.value,
						shopId: this.shopId,
					}
					this.$Request.post("/app/order/insertOrder", data).then(res => {
						if (res.code == 0) {
							this.$queue.showToast('添加成功');
							if (e == 1) {
								uni.navigateTo({
									url: '/pages/diancan/payOrder?shopId=' + this.shopId
								})
							} else {
								setTimeout(function() {
									uni.navigateBack()
								}, 1000)
							}
						} else {
							this.$queue.showToast(res.msg);
						}
					})
				}
				
			},
			// 选择规格
			skuSel(item, index, ind) {
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
				console.log(this.attrList)
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
			// 获取商品详情
			getData() {
				this.$Request.get("/app/goods/selectGoodsById?goodsId=" + this.goodsId).then(res => {
					uni.hideLoading()
					if (res.code == 0) {
						this.goodsDet = res.data
						this.goodsDet.goodsLabel = this.goodsDet.goodsLabel ? this.goodsDet.goodsLabel.split(',') :
							[]
						this.goodsDet.goodsPicture = this.goodsDet.goodsPicture?this.goodsDet.goodsPicture.split(','):[]
						this.goodsDet.goodsParticularsPicture = this.goodsDet.goodsParticularsPicture?this.goodsDet.goodsParticularsPicture.split(','):[]
						
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
					}
				});
			}

		}
	}
</script>

<style scoped>
	.btn {
		padding: 10rpx 30rpx;
		/* height: 60rpx; */
		/* background-color: #F2F2F2; */
		border-radius: 10rpx;
		text-align: center;
		/* line-height: 60rpx; */
		margin-right: 20rpx;
		font-size: 28rpx;
		color: #333333;
		border: 2px solid #F2F2F2;
	}

	.active {
		/* border: 2px solid #FCD202 !important;
		background: #FCD202 !important; */
		background: rgba(252, 210, 2, 0.2) !important;
		border: 2px solid #FCD202 !important;
		opacity: 0.6;
	}

	.detail_all {
		position: relative;
	}

	/* banner */
	.detail-banner {
		width: 100%;
		height: 410rpx;
	}

	.detail-banner image {
		width: 100%;
		height: 410rpx;
	}

	/* 详情 */
	.detail {
		width: 100%;
		padding: 3%;
		overflow: hidden;
		position: relative;
		top: -20rpx;
		background-color: #FFFFFF;
		border-radius: 32rpx 0 0 0;
	}

	.detail_text {
		font-size: 36rpx;
		font-weight: 800;
		color: #333333;
	}

	.detail_biao {
		display: flex;
		padding: 2% 0;
	}

	.detail_biao_sty {
		/* padding: 1% 1.5%; */
		padding: 10rpx 20rpx;
		background: rgba(216, 2, 4, 0.2);
		opacity: 0.6;
		color: #D80204;
		border-radius: 8rpx;
		font-size: 24rpx;
		margin-right: 2%;
	}

	.detail_text2 {
		font-size: 24rpx;
		font-family: PingFang SC;
		color: #333333;
		margin-top: 1%;
	}

	.detail_biao2 {
		display: flex;
		padding: 1% 0;
		font-size: 30rpx;
	}

	.detail_biao2_sty {
		padding: 1% 2.5%;
		/* width: 13%; */
		text-align: center;
		background: rgba(252, 210, 2, 0.2);
		border: 2rpx solid #FCD202;
		opacity: 0.6;
		border-radius: 8rpx;
		margin-right: 2%;
	}

	.detail_biao2_sty2 {
		padding: 1% 2.5%;
		/* width: 13%; */
		text-align: center;
		border: 2rpx solid #999999;
		opacity: 0.6;
		color: #999999;
		border-radius: 8rpx;
		margin-right: 3%;
	}

	/* 产品描述 */
	.detail_describe {
		width: 100%;
		padding: 3% 0;
		/* margin-bottom: 180rpx; */
		position: relative;
		top: -40rpx;
	}

	.detail_describe_ce {
		width: 100%;
		background-color: #FFFFFF;
		padding: 3%;
		font-size: 24rpx;
		padding-bottom: 100upx;
	}

	.detail_describe_text {
		font-size: 30rpx;
		font-weight: 500;
		color: #333333;
		margin-bottom: 20rpx;
	}

	.detail_describe_text2 {
		font-weight: 500;
		margin-top: 2%;
		color: #999999;
	}

	/* 加购 */
	.detail_account {
		width: 100%;
		background-color: #FFFFFF;
		padding: 3%;
		margin-top: 2%;
		position: fixed;
		bottom: 0;
		box-shadow: 0 -10rpx 20rpx #CCCCCC;
	}

	.detail_account_text {
		font-size: 35rpx;
		font-weight: bold;
		color: #333333;
	}

	.detail_account_text text {
		font-size: 27rpx;
	}

	.detail_account_top {
		width: 100%;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.detail_account_top_le {
		width: 80%;
		padding-bottom: 32upx;
		padding-top: 32upx;
	}

	.detail_account_top_ri {
		width: 20%;
		display: flex;
		margin-top: 2%;
	}

	.detail_account_top_text {
		padding: 2%;
		line-height: 54rpx;
	}

	.detail_account_top_ri image {
		width: 54rpx;
		height: 54rpx;
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
</style>
