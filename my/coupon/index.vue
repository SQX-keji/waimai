<template>
	<view class="pages">
		<!-- 切换选项 -->
		<view class="nav">
			<view @tap="change(0)" :class="{nav_btna:status == 0}">可使用</view>
			<view @tap="change(1)" :class="{nav_btna:status == 1}">已使用</view>
			<view @tap="change(2)" :class="{nav_btna:status == 2}">已失效</view>
		</view>
		<!-- 全部订单 -->
		<view class="cont_one">
			<view class="cont_one_ce" v-for="(item,index) in dataList" :key='index'>
				<view class="cont_one_top">
					<view class="cont_one_top_le">
						{{item.couponName}}
					</view>
					<view class="cont_one_top_ri"><text>￥</text>{{item.money}}</view>
				</view>
				<view class="cont_one_text" style="font-size: 28upx;">
					<text>有效期至{{item.expirationTime}}</text>
					<view v-if="item.minMoney" >满{{item.minMoney}}元可用</view>
					<view v-if="!item.minMoney" >无门槛优惠券</view>
				</view>
				<view class="cont_one_bottom">
					<view class="cont_one_bottom_le" ></view>
					<view class="cont_one_bottom_ri" @click="use" v-if="status == 0">立即使用</view>
				</view>
				<view class="cont_one_img" v-if="status == 1">
					<image src="../static/coupon/has.png" mode=""></image>
				</view>
				<view class="cont_one_img" v-if="status == 2">
					<image src="../static/coupon/failure.png" mode=""></image>
				</view>
			</view>
		</view>
		<empty v-if="!dataList.length" ></empty>
		<u-popup v-model="popupShow" closeable mode="center" border-radius="20">
			<view class="margin-tb text-center text-lg text-bold">使用规则</view>
			<view class="padding-lr padding-bottom">
				<view style="color: #333333;font-size: 28upx;width: 550rpx;height: 70vh;" v-html="content"></view>
			</view>
		</u-popup>
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
				btnnum: 0,
				page: 1,
				limit: 10,
				status: 0, //0正常 1已使用 2已失效
				dataList: [],
				popupShow: false,
				content: '',
				totalCount: 0
			};
		},
		onLoad() {
			this.getData()
			// this.getGuize()
		},
		methods: {
			change(e) {
				this.status = e
				this.page = 1
				this.dataList = []
				this.getData()
			},
			getData() {
				let data = {
					status: this.status,
					page: this.page,
					limit: this.limit,
				}
				this.$Request.get("/app/coupon/CouponList", data).then(res => {
					if (res.code == 0) {
						this.totalCount = res.data.totalCount
						if (this.page == 1) {
							this.dataList = res.data.list
						} else {
							this.dataList = [...this.dataList, ...res.data.list]
						}
					}
				});
			},
			//优惠卷兑换规则
			getGuize() {
				this.$Request.getT('/app/common/type/240').then(res => {
					if (res.code == 0) {
						this.content = res.data.value;
					}
				})
			},
			use() {
				uni.switchTab({
					url: '/pages/index/index'
				})
			}
		},
		onReachBottom: function() {
			if(this.dataList.length<this.totalCount) {
				this.page = this.page + 1;
				this.getData()
			} else {
				uni.showToast({
					title: '已经到底了',
					icon: 'none'
				})
			}
		},
	}
</script>

<style scoped>
	/* 切换选项 */
	.nav {
		display: flex;
		align-items: center;
		justify-content: center;
		background-color: #FFFFFF;
		color: #999999;
	}

	.nav view {
		flex-grow: 1;
		margin: 3% 9% 2%;
		text-align: center;
	}

	.nav_btna {
		font-size: 38rpx;
		line-height: 34rpx;
		border-bottom: 14rpx solid #FCD202;
		color: #000000;
		font-weight: bold;
	}

	/* 内容 */
	/* 全部订单 */
	.cont_one {
		/* display: none; */
		width: 94%;
		margin: 0 auto;
	}

	.cont_one_ce {
		width: 100%;
		padding: 3% 3% 2%;
		margin: 3% 0 0;
		background-color: #FFFFFF;
		border-radius: 18rpx;
		position: relative;
	}

	.cont_one_top {
		display: flex;
		width: 100%;
	}

	.cont_one_top_le {
		flex: 2;
		font-size: 35rpx;
		font-weight: 800;
		color: #000000;
		line-height: 32rpx;
	}

	.cont_one_top_le2 {
		flex: 2;
		font-size: 35rpx;
		font-weight: 800;
		color: #999999;
		line-height: 32rpx;
	}

	.cont_one_top_ri {
		flex: 1;
		font-size: 40rpx;
		text-align: right;
		font-family: DINPro;
		font-weight: 500;
		color: #FF130A;
		line-height: 32rpx;
	}

	.cont_one_top_ri text {
		font-size: 30rpx;
	}

	.cont_one_text {
		font-size: 30rpx;
		margin: 2% 0 1%;
		font-weight: 400;
		color: #999999;
		display: flex;
		justify-content: space-between;
	}

	.cont_one_bottom {
		width: 100%;
		padding: 3% 0 0;
		margin-top: 3%;
		display: flex;
		border-top: 2rpx dotted #E6E6E6;
	}

	.cont_one_bottom_le {
		width: 80%;
		font-size: 30rpx;
		font-weight: 500;
		color: #333333;
		line-height: 2;
	}

	.cont_one_bottom_ri {
		width: 20%;
		text-align: center;
		line-height: 2;
		background: rgba(255, 19, 10, 0.2);
		font-size: 24rpx;
		border: 2rpx solid #FF130A;
		color: #FF130A;
		opacity: 0.6;
		border-radius: 50rpx;
	}

	/* 到店取餐 */
	/* .cont_two {
		display: none;width: 94%;margin: 0 auto;
	}
	.cont_two_ce{
		width: 94%;padding: 3% 3% 2%; margin: 3% 0 0 ;background-color: #FFFFFF;
		border-radius: 18rpx;
	} */
	.cont_one_img {}

	.cont_one_img {
		width: 120upx;
		height: 117upx;
		position: absolute;
		top: 0;
		left: 83%;
	}

	.cont_one_img image {
		width: 120upx;
		height: 117upx;
	}



	.cont {
		display: none;

	}

	.cont_dis {
		display: block;
	}
</style>
