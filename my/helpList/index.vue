<template>
	<view>
		<view style="margin-top: 4upx;" class="bg-white flex justify-between align-center padding" v-for="(item,index) in dataList" :key='index' @click="goDet(item)" >
			<view class="text-lg">{{index+1}}.{{item.title}}</view>
			<image src="../../static/images/index/right2.png" style="width: 20rpx;height: 34rpx;" mode="aspectFill"></image>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				page: 1,
				limit: 10,
				dataList: []
			}
		},
		onLoad() {
			this.getDataList()
		},
		methods: {
			getDataList() {
				let data = {
					page: this.page,
					limit: this.limit,
					type: 1
				}
				this.$Request.getT("/app/userinfo/trainingCenterList",data).then(res => {
					this.dataList = res.data.list
				})
			},
			goDet(e) {
				uni.navigateTo({
					url: '/my/helpList/helpDet?id='+e.trainingId
				})
			}
		},
		onReachBottom: function() {
		   this.page = this.page + 1;
		   this.getDataList();
		},
	}
</script>

<style>
</style>
