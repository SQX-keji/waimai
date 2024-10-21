<template>
	<view class="content">
		<view class="search-box">
			<!-- mSearch组件 如果使用原样式，删除组件元素-->
			<!-- <mSearch class="mSearch-input-box" :mode="2" button="inside" :placeholder="defaultKeyword"
				@search="doSearch(false)" @input="inputChange" @confirm="doSearch(false)" v-model="keyword"></mSearch> -->
			<!-- 原样式 如果使用原样式，恢复下方注销代码 -->

			<!-- <view class="input-box">
				<input type="text" :adjust-position="true" :placeholder="defaultKeyword" @input="inputChange" v-model="keyword" @confirm="doSearch(false)"
				 placeholder-class="placeholder-class" confirm-type="search">
			</view>
			<view class="search-btn" @tap="doSearch(false)">搜索</view> -->
			<view class="margin-lr">
				<u-search style="width: 100%;" placeholder="请输入商家或者商品名称" :focus="true" v-model="keyword"
					:show-action="true" :animation="true" shape="square" action-text="取消" @custom="goBack()"
					@search="doSearch(false)">
				</u-search>
			</view>

			<!-- <u-dropdown v-show="isShowKeywordList">
				<u-dropdown-item v-model="value1" :title="title" :options="options1" @change="confirm">
				</u-dropdown-item>
				<u-dropdown-item v-model="value2" :title="title1" :options="options2" @change="confirm2">
				</u-dropdown-item>
			</u-dropdown> -->
			<view v-show="isShowKeywordList" style="width: 100%;z-index: 999;">
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
				<view v-if="isShow" style="position: absolute;top: 150rpx;width: 100%;z-index: 1000;background: rgba(0,0,0,.5);height: 100vh;" @click="isShow =false">
					<view class="padding-lr bg-white">
						<view class="flex justify-between align-center padding-tb-sm u-border-bottom" v-for="(item,index) in options4" :key='index' @click.stop="getSelect(item)" >
							<view class="text-df" :class="item.select?'select':''">{{item.shopTypeName}}</view>
							<u-icon v-if="item.select" name="checkmark" color="#FCD202" size="28"></u-icon>
						</view>
					</view>
				</view>
			</view>
			<!-- 原样式 end -->
		</view>
		<view class="search-keyword">
			<view class="keyword-list-box" v-show="isShowKeywordList" scroll-y>

				<view class="padding-lr">
					<view class="" v-for="(item,index) in keywordList" :key='index'>
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
										<view class="lable" v-if="item.exemptMinMoney">满{{item.exemptMinMoney}}免配送费
										</view>
										<view class="lable" v-for="(ite,ind) in item.shopLable" :key='ind'
											v-if="item.shopLable">
											{{ite}}
										</view>
									</view>
								</view>
								<view class="flex margin-top-xs">
									<view v-for="(ite,ind) in item.goodsList" :key='ind'
										@click.stop="goDet(ite.goodsId,item.shopId)" style="width: 33%;">
										<image :src="ite.goodsCover" style="width: 120rpx;height: 120rpx;"
											class="radius" mode=""></image>
										<view class="u-line-1 text-df text-bold margin-top-xs">{{ite.goodsName}}</view>
										<view class="text-bold margin-top-xs" style="color: #FD6416;"> <text
												class="text-sm">¥</text> {{ite.goodsMoney}}</view>
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
				<!-- <view v-if="keywordList.length == 0">
					暂无数据
				</view> -->
				<empty v-if="keywordList.length == 0"></empty>
			</view>
			<view class="keyword-box" v-show="!isShowKeywordList" scroll-y>
				<view class="keyword-block" v-if="hotKeywordList.length>0">
					<view class="keyword-list-header">
						<view>热门搜索</view>
						<view>
							<image @tap="hotToggle" :src="'/static/images/index/attention'+forbid+'.png'"></image>
						</view>
					</view>
					<view class="keyword" v-if="forbid==''">
						<view v-for="(keyword,index) in hotKeywordList" @tap="doSearch(keyword)" :key="index">
							{{keyword}}
						</view>
					</view>
					<view class="hide-hot-tis" v-else>
						<view>当前搜热已隐藏</view>
					</view>
				</view>
				<view class="keyword-block" v-if="oldKeywordList.length>0">
					<view class="keyword-list-header">
						<view>历史记录</view>
						<view>
							<image @tap="oldDelete" src="/static/images/index/delete.png"></image>
						</view>
					</view>
					<view class="keyword">
						<view v-for="(keyword,index) in oldKeywordList" @tap="doSearch(keyword.message)" :key="index">
							{{keyword.message}}
						</view>
					</view>
				</view>
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
				isShow: false,
				defaultKeyword: "",
				keyword: "",
				oldKeywordList: [], //历史记录
				hotKeywordList: [], //热搜
				keywordList: [], //搜索列表
				forbid: '',
				isShowKeywordList: false,
				limit: 10,
				page: 1,
				userId: '',
				isVip: false,
				lng: '',
				lat: '',

				value1: 1,
				value2: 0,
				options1: [{
						label: '综合排序',
						value: 1,
					},
					{
						label: '距离优先',
						value: 3,
					},
					{
						label: '销量优先',
						value: 4,
					}
				],
				options2: [{
					value: '',
					label: "全部"
				}],
				options4: [{
					id: '',
					select: true,
					shopTypeName: "全部"
				}],
				current: 1,
				shopTypeId: '',
				title: '综合排序',
				title1: '筛选',
				totalCount: 0
			}
		},
		onLoad() {
			let that = this
			this.getSearchList()
			this.userId = uni.getStorageSync('userId') ? uni.getStorageSync('userId') : ''

			this.getShopType()
			this.isVip = uni.getStorageSync('isVIP')
			uni.getLocation({
				type: 'gcj02', //返回可以用于uni.openLocation的经纬度
				success: function(res) {
					that.lat = res.latitude;
					that.lng = res.longitude;
				}
			});
		},
		methods: {
			getSelect(e) {
				this.options4.forEach(res=>{
					if(res.id == e.id) {
						res.select = true
						this.page = 1
						this.shopTypeId = e.id
						// this.getShopList();
						this.doSearch(this.keyword);
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
				
				this.doSearch(this.keyword);
			},
			confirm2(e) {
				console.log(e)
				this.page = 1
				this.shopTypeId = e
				if (e == 0) {
					this.title1 = '筛选'
				} else {
					this.title1 = this.options2[e].label
				}
				this.doSearch(this.keyword);
			},
			// 获取搜索历史
			getSearchList() {
				let data = {
					page: this.page,
					limit: this.limit
				}
				this.$Request.get("/app/searchhistory/selectSearchHistory", data).then(res => {
					console.log(res)
					if (res.code == 0) {
						this.oldKeywordList = res.data.list
					}
				});
				this.$Request.getT('/app/common/type/309').then(res => { //订单状态通知
					if (res.code == 0) {
						this.hotKeywordList = res.data.value.split(',')
					}
				})
			},
			//清除历史搜索
			oldDelete() {
				uni.showModal({
					content: '确定清除历史搜索记录？',
					success: (res) => {
						if (res.confirm) {
							console.log('用户点击确定');
							this.$Request.get("/app/searchhistory/deleteSearchHistory").then(res => {
								if (res.code == 0) {
									this.getSearchList()
								}
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			//执行搜索
			doSearch(keyword) {
				this.keyword = keyword === false ? this.keyword : keyword;
				this.isShowKeywordList = true;
				if (!this.keyword) {
					uni.showToast({
						title: '请输入内容',
						icon: 'none',
						duration: 1000
					})
					return
				}

				let data = {
					impotr: this.keyword,
					screen: this.current,
					shopTypeId: this.shopTypeId,
					limit: this.limit,
					page: this.page,
					lng: this.lng,
					lat: this.lat,
					userId: this.userId
				}

				this.$Request.get("/app/goods/selectShop", data).then(res => {
					if (res.code == 0) {
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
							ret.errandTime = Math.round(ret.errandTime)
							ret.shopScore = ret.shopScore.toFixed(1)
						})

						if (this.page == 1) this.keywordList = [];
						this.keywordList = [...this.keywordList, ...res.data.list]
					}
				});
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
			// 点击取消返回首页
			goBack() {
				uni.navigateBack()
			},
			//热门搜索开关
			hotToggle() {
				this.forbid = this.forbid ? '' : '_forbid';
			},
			// 跳转商品详情
			goDet(goodsId, shopId) {
				uni.navigateTo({
					url: '/pages/index/shop/goodsDet?goodsId=' + goodsId + '&shopId=' + shopId + '&orderType=1'
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
			// 跳转订单
			goOrder(e) {
				if (this.userId) {
					uni.navigateTo({
						url: '/pages/index/game/order?id=' + e.id
					});
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					});
				}
			},

		},
		onReachBottom: function() {
			// this.page = this.page + 1;
			// this.doSearch(false);
			if(this.keywordList.length<this.totalCount) {
				this.page = this.page + 1;
				this.doSearch(false);
			} else {
				uni.showToast({
					title: '已经到底了',
					icon: 'none'
				})
			}
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.doSearch(false);
		},
	}
</script>
<style>
	page {
		/* background-color: #FFFFFF; */
	}
	.select{
		color: #FCD202;
	}
	.search-box {
		width: 100%;
		/* background-color: rgb(242, 242, 242); */
		padding: 15upx 0 0;
		/* display: flex; */
		/* justify-content: space-between; */
		position: sticky;
		top: 0;
		background-color: #FFF;
		z-index: 99;
	}

	.search-box .mSearch-input-box {
		width: 100%;
	}

	.search-box .input-box {
		width: 85%;
		flex-shrink: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.search-box .search-btn {
		width: 15%;
		margin: 0 0 0 2%;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-shrink: 0;
		font-size: 28upx;
		/* color: #fff; */
		background: linear-gradient(to right, #ff9801, #ff570a);
		border-radius: 60upx;
	}

	.search-box .input-box>input {
		width: 100%;
		height: 60upx;
		font-size: 32upx;
		border: 0;
		border-radius: 60upx;
		-webkit-appearance: none;
		-moz-appearance: none;
		appearance: none;
		padding: 0 3%;
		margin: 0;
		background-color: #ffffff;
	}

	.placeholder-class {
		color: #9e9e9e;
	}

	.search-keyword {
		width: 100%;
		/* background-color: #111224; */
	}

	.keyword-list-box {
		height: calc(100vh - 110upx);
		/* padding-top: 10upx; */
		/* border-radius: 20upx 20upx 0 0; */
		/* background-color: #fff; */
	}

	.keyword-entry-tap {
		background-color: #eee;
	}

	.keyword-entry {
		width: 94%;
		height: 80upx;
		margin: 0 3%;
		font-size: 30upx;
		color: #333;
		display: flex;
		justify-content: space-between;
		align-items: center;
		border-bottom: solid 1upx #e7e7e7;
	}

	.keyword-entry image {
		width: 60upx;
		height: 60upx;
	}

	.keyword-entry .keyword-text,
	.keyword-entry .keyword-img {
		height: 80upx;
		display: flex;
		align-items: center;
	}

	.keyword-entry .keyword-text {
		width: 90%;
	}

	.keyword-entry .keyword-img {
		width: 10%;
		justify-content: center;
	}

	.keyword-box {
		height: calc(100vh - 110upx);
		/* border-radius: 20upx 20upx 0 0; */
		/* background-color: #111224; */
	}

	.keyword-box .keyword-block {
		padding: 10upx 0;
	}

	.keyword-box .keyword-block .keyword-list-header {
		width: 94%;
		padding: 10upx 3%;
		font-size: 27upx;
		font-weight: 700;
		/* color: #FFFFFF; */
		display: flex;
		justify-content: space-between;
	}

	.keyword-box .keyword-block .keyword-list-header image {
		width: 40upx;
		height: 40upx;
	}

	.keyword-box .keyword-block .keyword {
		width: 94%;
		padding: 3px 3%;
		display: flex;
		flex-flow: wrap;
		justify-content: flex-start;
	}

	.keyword-box .keyword-block .hide-hot-tis {
		display: flex;
		justify-content: center;
		font-size: 28upx;
		/* color: #FFFFFF; */
	}

	.keyword-box .keyword-block .keyword>view {
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 10upx;
		padding: 0 20upx;
		margin: 10upx 20upx 10upx 0;
		height: 60upx;
		font-size: 28upx;
		background-color: #eee;
		/* color: #FFFFFF; */
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
