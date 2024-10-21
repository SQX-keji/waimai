<template>
	<view style="line-height: 26px;padding: 32upx;" class="home1">
		<!-- <view v-html="tit"> </view> -->
		<view style="font-size: 28upx;" v-html="content"></view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				tit: '',
				content: ''
			}
		},
		onLoad() {
			this.getGuize();
		},
		methods: {
			getGuize() {
				this.$Request.getT('/app/common/type/236').then(res => {
					if (res.code == 0) {
						this.content = res.data.value;
						// this.tit = res.data.min
					}
				});
			}
		}
	}
</script>

<style>
	page {
		background: #FFFFFF;
	}
</style>
