<template>
	<view>
		<view>
			<view class="store-list y-p-30">
				<view class="bg-white store-single y-p-30 y-m-b-30 y-radius-30" v-for="(storeItem, storeIndex) in shoppingCart" :key="storeIndex">
					<view @click="storeSelBtn(storeIndex)" class="store-header y-flex y-align-center">
						<view class="sel-btn y-p-t-5">
							<u-icon v-if="(storeItem.isBuySelect && !isEdit) || (storeItem.isDelSelect && isEdit)" name="checkmark-circle-fill" color="#04BE02" size="40rpx"></u-icon>
							<view v-else class="no-select"></view>
						</view>
						<span class="y-font-size-30 y-m-l-13 y-m-r-10">{{storeItem.shopName}}</span>
						<u-icon name="arrow-right"></u-icon>
					</view>
					<view class="goods-list y-p-l-20">
						<view class="goods-item y-flex y-p-t-20" v-for="(goodsItem, goodsIndex) in storeItem.orderGoodsList" :key="goodsIndex">
							<view @click="goodsSelBtn(storeIndex, goodsIndex)" class="y-flex y-align-center">
								<view class="sel-btn">
									<u-icon v-if="(goodsItem.isBuySelect && !isEdit) || (goodsItem.isDelSelect && isEdit)" name="checkmark-circle-fill" color="#04BE02" size="40rpx"></u-icon>
									<view v-else class="no-select"></view>
								</view>
							</view>
							<view class="y-m-l-15">
								<u-image :src="goodsItem.goodsPicture[0]" radius="10rpx" width="162rpx" height="162rpx"></u-image>
							</view>
							<view class="y-m-l-28 y-flex y-flex-1 y-flex-column">
								<view class="y-flex-1">
									<view class="goods-name y-font-size-28"> {{goodsItem.goodsName}} </view>
									<view class="goods-attr y-flex y-m-t-15">
										{{goodsItem.skuMessage}}
										<!-- <u-tag v-if="goodsItem.skuMessage.length > 0" :text="goodsItem.skuMessage.join(';')" plain size="mini" type="warning"></u-tag> -->
										<!-- <u-tag v-else text="默认规格" plain size="mini" type="warning"></u-tag> -->
									</view>
								</view>
								<view class="goods-price y-flex y-align-end y-flex-1">
									<view class="y-flex-1 y-font-size-30 y-weight-bold color-price"> ￥ {{goodsItem.goodsPrice}} </view>
									<view class="flex align-center"> 
										<image @click="updataNum(storeItem,goodsItem,2)" src="../static/shoppingCar/jian.png" style="width: 54rpx;height: 54rpx;" mode=""></image>
										<view class="padding-lr-sm">{{goodsItem.goodsNum}}</view>
										<image @click="updataNum(storeItem,goodsItem,1)" src="../static/shoppingCar/add.png" style="width: 49rpx;height: 49rpx;" mode=""></image>
										<!-- <u-number-box size="18" v-model="goodsItem.goodsNum" @change="countChange(storeIndex, goodsIndex)"></u-number-box> -->
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view style="height: 120rpx;"></view>
		</view>
		<view class="y-position-fixed y-bottom-0 y-left-0 y-right-0 y-p-y-20 y-p-x-30 bg-white y-flex">
			<view class="y-flex y-flex-1 y-align-center">
				<view @click="allSelBtn()" class="store-header y-flex y-align-center">
					<view class="sel-btn y-p-t-5">
						<u-icon v-if="allSelState" name="checkmark-circle-fill" color="#04BE02" size="40rpx"></u-icon>
						<view v-else class="no-select"></view>
					</view>
					<span class="y-font-size-28 y-m-l-10">全选</span>
				</view>
			</view>
			<view class="y-flex y-align-center y-font-size-28">
				<view v-if="!isEdit" class="y-font-size-33"> 总计: <span class="color-price y-weight-bold y-m-l-8">￥ {{totalPrice}} </span> </view>
				<view class="y-flex y-m-l-25">
					<u-button v-if="!isEdit" shape="circle" :hairline="false" :customStyle="submitBtnStyle"> 结算( {{totalSelCount}} ) </u-button>
					<u-button v-else shape="circle" :hairline="false" :customStyle="submitBtnStyle"> 删除( {{totalSelCount}} ) </u-button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				isEdit: false, //是否编辑（购物/编辑）
				shoppingCart: [],//购物车数据
				submitBtnStyle: {
					background: '#FD7026',
					color: '#ffffff',
					border: 'none'
				},//结算、删除按钮的样式
				page: 1,
				limit: 10,
				
			}
		},
		computed:{
			//是否已经全部选中
			allSelState(){
				let buyAllSelect = true // 购物全选
				let delAllSelect = true // 编辑全选
				this.shoppingCart.forEach(sitem=>{
					sitem.orderGoodsList.forEach(gitem=>{
						if(!gitem.isBuySelect) buyAllSelect = false;
						if(!gitem.isDelSelect) delAllSelect = false;
					})
				})
				if(!this.isEdit) return buyAllSelect;
				else return delAllSelect;
			},
			//总价格
			totalPrice(){
				let totalPrice = 0
				this.shoppingCart.forEach(sitem=>{
					sitem.orderGoodsList.forEach(gitem=>{
						if(gitem.isBuySelect){
							totalPrice = totalPrice*1 + gitem.goodsPrice*gitem.goodsNum
						}
					})
				})
				return totalPrice
			},
			//当前操作下选中的数量
			totalSelCount(){
				let buyCount = 0 // 购物全选
				let delCount = 0 // 编辑全选
				this.shoppingCart.forEach(sitem=>{
					sitem.orderGoodsList.forEach(gitem=>{
						if(gitem.isBuySelect){
							buyCount = buyCount*1 + gitem.goodsNum
						}
						if(gitem.isDelSelect){
							delCount = delCount*1 + 1
						}
					})
				})
				if(!this.isEdit){
					return buyCount
				} else{
					return delCount
				}
			}
		},
		onLoad() {
			this.getData()
		},
		methods: {
			// 结算
			goConfirm() {
				if (this.shoppingCart && this.goodsList.orderGoodsList[0].length > 0) {
					uni.navigateTo({
						url: '/pages/diancan/confirmOrder?shopId=' + this.shop.shopId + '&orderType=' + this
							.orderType
					})
				} else {
					uni.showToast({
						title: '请先添加商品',
						icon: "none"
					})
				}
			
			},
			//获取购物车列表
			getData(){
				let data = {
					// page: 1,
					// limit: 10
				}
				this.$Request.get("/app/order/selectShoppingTrolley", data).then(res => {
					if (res.code == 0&&res.data) {
						res.data.forEach(res=>{
							res.isDelSelect = false
							res.isBuySelect = false
							res.orderGoodsList.forEach(ret=>{
								ret.goodsPicture = ret.goodsPicture.split(',')
								ret.isDelSelect = false
								ret.isBuySelect = false
							})
						})
						
						if(this.page == 1) {
							this.shoppingCart = res.data
						} else {
							this.shoppingCart = [...this.shoppingCart, ...res.data]
						}
					}
				});
			},
			//商家的选中与否
			storeSelBtn(storeIndex){
				if(!this.isEdit){ //购物
					this.shoppingCart[storeIndex].isBuySelect = !this.shoppingCart[storeIndex].isBuySelect
					this.shoppingCart[storeIndex].orderGoodsList.forEach(item=>{
						item.isBuySelect = this.shoppingCart[storeIndex].isBuySelect
					})
				}else{//编辑
					this.shoppingCart[storeIndex].isDelSelect = !this.shoppingCart[storeIndex].isDelSelect
					this.shoppingCart[storeIndex].orderGoodsList.forEach(item=>{
						item.isDelSelect = this.shoppingCart[storeIndex].isDelSelect
					})
				}
			},
			//商品的选中与否
			goodsSelBtn(storeIndex, goodsIndex){
				if(!this.isEdit){ //购物
					this.shoppingCart[storeIndex].orderGoodsList[goodsIndex].isBuySelect = !this.shoppingCart[storeIndex].orderGoodsList[goodsIndex].isBuySelect
					let allIsSel = true //是否已经全部选中
					this.shoppingCart[storeIndex].orderGoodsList.forEach(item=>{
						if(!item.isBuySelect){
							allIsSel = false
						}
					})
					this.shoppingCart[storeIndex].isBuySelect = allIsSel
				}else{//编辑
					this.shoppingCart[storeIndex].orderGoodsList[goodsIndex].isDelSelect = !this.shoppingCart[storeIndex].orderGoodsList[goodsIndex].isDelSelect
					let allIsSel = true //是否已经全部选中
					this.shoppingCart[storeIndex].orderGoodsList.forEach(item=>{
						if(!item.isDelSelect){
							allIsSel = false
						}
					})
					this.shoppingCart[storeIndex].isDelSelect = allIsSel
				}
			},
			//全选
			allSelBtn(){
				let toState = !this.allSelState
				if(!this.isEdit){//购物
					this.shoppingCart.forEach(sitem=>{
						sitem.isBuySelect = toState
						sitem.orderGoodsList.forEach(gitem=>{
							gitem.isBuySelect = toState
						})
					})
				}else{//编辑
					this.shoppingCart.forEach(sitem=>{
						sitem.isDelSelect = toState
						sitem.orderGoodsList.forEach(gitem=>{
							gitem.isDelSelect = toState
						})
					})
				}
			},
			//商品的数量增减
			countChange(storeIndex, goodsIndex){
				//请求后台改变购物车商品的数量。。。storeIndex, goodsIndex
			},
			updataNum(storeItem,goodsItem,type) {
				let data = {
					orderGoodsId: goodsItem.id,
					type: type,
					num: 1,
					shopId: storeItem.shopId
				}
				this.$Request.get("/app/order/updateGoodsNum", data).then(res => {
					if (res.code == 0) {
						
						this.getData()
					}
				});
			}
		}
	}
</script>

<style lang="scss" scoped>
	.w-90{
		width: 90rpx;
	}
	.sel-btn{
		width: 45rpx;
		height: 45rpx;
	}
	.no-select{
		width: 39rpx;
		height: 39rpx;
		border-radius: 50%;
		border: 1px solid rgb(235, 236, 238);
	}
	
	.y-position-fixed{
		position: fixed;
	}
	.y-top-0{
		top: 0;
	}
	.y-bottom-0{
		bottom: 0;
	}
	.y-left-0{
		left: 0;
	}
	.y-right-0{
		right: 0;
	}
	.y-flex-column{
		flex-direction: column !important;
	}
	.y-w-100{
		width: 100%;
	}
	/*系统状态栏高度*/
	.y-system-height{
		height: var(--status-bar-height);
	}
	/* 圆角大小例：radius-1 ,radius-10... */
	@for $i from 1 through 50 {
	  .y-radius-#{$i} {  border-radius: $i*1rpx;}
		/* margin */
		.y-m-t-#{$i} { margin-top: $i*1rpx; }
		.y-m-b-#{$i} { margin-bottom: $i*1rpx; }
		.y-m-l-#{$i} { margin-left: $i*1rpx; }
		.y-m-r-#{$i} { margin-right: $i*1rpx; }
		.y-m-x-#{$i} { margin-left: $i*1rpx; margin-right: $i*1rpx; }
		.y-m-y-#{$i} { margin-top: $i*1rpx; margin-bottom: $i*1rpx; }
		.y-m-#{$i} { margin: $i*1rpx; }
		/* padding */
		.y-p-t-#{$i} { padding-top: $i*1rpx; }
		.y-p-b-#{$i} { padding-bottom: $i*1rpx; }
		.y-p-l-#{$i} { padding-left: $i*1rpx; }
		.y-p-r-#{$i} { padding-right: $i*1rpx; }
		.y-p-x-#{$i} { padding-left: $i*1rpx; padding-right: $i*1rpx; }
		.y-p-y-#{$i} { padding-top: $i*1rpx; padding-bottom: $i*1rpx; }
		.y-p-#{$i} { padding: $i*1rpx; }
		/* font-size */
		.y-font-size-#{$i} { font-size: $i*1rpx; }
	}
	/* 自体加粗例：weight-100 ,weight-150 ,weight-600... */
	@for $i from 1 through 9 {
	  .y-weight-#{$i*100} {  font-weight: $i*100;}
		.y-weight-#{$i*100 + 50} {  font-weight: 50 + $i*100;}
	}
	.y-justify-end{
		justify-content: flex-end;
	}
	.y-align-center{
		align-items: center;
	}
	.y-align-start{
		align-items: flex-start;
	}
	.y-align-end{
		align-items: flex-end;
	}
	.y-flex{
		display: flex;
	}
	.y-flex-column{
		flex-direction: column;
	}
	.y-flex-1{
		flex: 1;
	}
	.y-justify-start{
		justify-content: flex-start;
	}
	.y-justify-end{
		justify-content: flex-end;
	}
	.y-justify-around{
		justify-content: space-around;
	}
	.y-justify-between{
		justify-content: space-between;
	}
	.y-weight-bold{
		font-weight: bold;
	}
	.y-border-bottom {
	  border-bottom: 1rpx solid rgba($color: #707070, $alpha: 0.12);
	}
	.color-price{
		color: #C8222A;
	}
</style>
