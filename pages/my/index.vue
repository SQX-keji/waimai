<template>
	<view class="content">

		<view class="head">
			<view class="head_image">
				<view class="lovip" v-if="isVip">
					<image src="../../static/images/my/vip.png" style="width: 88rpx;height: 32rpx;"></image>
				</view>
				<image :src="avatar?avatar:'../../static/logo.png'"></image>
			</view>
			<view class="head_name" @click="bindlogin">
				<view class="name" v-if="token" >{{ userName }}</view>
				<view class="name" v-if="!token">登录</view>
			</view>
		</view>
		<view class="margin-lr padding-lr-sm padding-tb radius flex justify-between" v-if="XCXIsSelect=='是'">
			<view class="text-center text-black" @click="goShop('/my/coupon/index')">
				<view class="text-lg text-bold">{{msgData.countCoupon}}</view>
				<view>外卖优惠券</view>
			</view>
			<view class="text-center text-black" @click="goShop('/my/wallet/index')">
				<view class="text-lg text-bold">{{msgData.userMoney}}</view>
				<view>我的余额</view>
			</view>
			<view class="text-center text-black" @click="goShop('/my/task/index')">
				<view class="text-lg text-bold">{{msgData.userIntegral}}</view>
				<view>我的积分</view>
			</view>
		</view>
		<view class="margin-bottom padding-lr" style="position: relative;" v-if="XCXIsSelect=='是'">
			<image src="../../static/images/my/bg.png" style="width: 100%;height: 110rpx;" mode=""></image>
			<view class="flex justify-between  margin-lr padding-tb-sm radius"
				style="position: absolute;top: 0;width: 640rpx;">
				<image src="../../static/images/my/huiyuan.png" style="width: 70rpx;height: 70rpx;"></image>
				<view class="flex-sub margin-left text-lg text-bold" style="line-height: 74rpx;color: #604320;">
					开通享受贵族福利
				</view>
				<view v-if="!isVip" class="btn-bg" style="color: #604320;" @click="goNav({url:'/my/vip/index'})">去开通</view>
				<view v-if="isVip" class="btn-bg" style="color: #604320;" @click="goNav({url:'/my/vip/index'})">已开通</view>
			</view>
		</view>
		<view class="center flex justify-between bg-white margin-top padding-lr padding-tb-lg radius">
			<view class="flex justify-between flex-sub padding-right" style="border-right: 2rpx solid #CCCCCC;"
				@click="goShop('/my/task/index')">
				<view class="">
					<view class="text-black text-xl text-bold">每日任务</view>
					<view class="text-sm margin-top-xs">每日签到领积分</view>
				</view>
				<image src="../../static/images/my/renwu.png" mode="" style="width: 88rpx;height: 88rpx;"></image>
			</view>
			<view class="flex justify-between flex-sub padding-left" @click="goShop('/my/integral/index')">
				<view class="">
					<view class="text-black text-xl text-bold">积分商城</view>
					<view class="text-sm margin-top-xs flex align-center">积分兑换优惠券</view>
				</view>
				<image src="../../static/images/my/jifen.png" mode="" style="width: 88rpx;height: 88rpx;"></image>
			</view>
		</view>
		
		<view class="margin padding-lr-sm padding-tb bg-white radius">
			<view class="flex justify-between align-center">
				<view class="text-lg text-bold text-black">推荐工具</view>
			</view>
			<view class="flex flex-wrap">
				<!-- #ifdef MP-WEIXIN -->
				<view class="text-center margin-tb-sm" style="width: 25%;" v-if="XCXIsSelect=='是'" >
					<button class="btn" open-type="share">
						<image src="../../static/images/my/4.png" style="width: 50rpx;height: 48rpx;" mode=""></image>
						<view>分享好友</view>
					</button>
				</view>
				
				<view class="text-center margin-tb-sm" style="width: 25%;" @click="goApplet('wx251e3e099f1a0936')"
					v-if="shopStatus == 1&&XCXIsSelect=='是'">
					<image src="../../static/images/my/1.png" style="width: 55rpx;height: 55rpx;" mode="scaleToFill">
					</image>
					<view class="text-sm">商家中心</view>
				</view>
				<view class="text-center margin-tb-sm" style="width: 25%;" @click="goNav({url:'/my/apply/index'})" v-if="shopStatus != 1&&XCXIsSelect=='是'">
					<image src="../../static/images/my/1.png" style="width: 55rpx;height: 55rpx;" mode="scaleToFill">
					</image>
					<view class="text-sm">商家入驻</view>
				</view>
				<view class="text-center margin-tb-sm" style="width: 25%;" @click="goApplet('wx3b6a387766fd95b9')" v-if="XCXIsSelect=='是'" >
					<image src="../../static/images/my/11.png" style="width: 55rpx;height: 55rpx;" mode="scaleToFill">
					</image>
					<view class="text-sm">骑手入驻</view>
				</view>
				<!-- #endif -->
				<!-- #ifndef MP-WEIXIN -->
				<view class="text-center margin-tb-sm" style="width: 25%;" @click="goNav({url:'/my/apply/index'})" v-if="shopStatus != 1">
					<image src="../../static/images/my/1.png" style="width: 55rpx;height: 55rpx;" mode="scaleToFill">
					</image>
					<view class="text-sm">商家入驻</view>
				</view>
				<!-- #endif -->
				<view class="text-center margin-tb-sm" style="width: 25%;position: relative;" v-for="(item, index) in mylist" :key='index'
					@click="goNav(item)" v-if="XCXIsSelect=='是'">
					<image :src="item.image" style="width: 55rpx;height: 55rpx;" mode="scaleToFill"></image>
					<view class="text-sm">{{item.name}}</view>
					<view v-if="index==7&&item.messageCount>0" style="height: 32rpx;width: 32rpx;border-radius: 100rpx;background-color: red;color: #FFF;text-align: center;position: absolute;top:-10rpx;right: 24rpx;">{{item.messageCount}}</view>
				</view>
				<view class="text-center margin-tb-sm" style="width: 25%;" v-for="(item, index) in mylist1" :key='index'
					@click="goNav(item)" v-if="XCXIsSelect=='否'">
					<image :src="item.image" style="width: 55rpx;height: 55rpx;" mode="scaleToFill"></image>
					<view class="text-sm">{{item.name}}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				mylist: [{
						id: 1,
						name: '我的投诉',
						image: '../../static/images/order/tousu.png',
						url: '/my/tousu/index'
					},{
						id: 2,
						name: '帮助中心',
						image: '../../static/images/my/12.png',
						url: '/my/helpList/index'
					}, {
						id: 3,
						name: '消息中心',
						image: '../../static/images/my/3.png',
						url: '/my/msg/index'
					},{
						id: 6,
						name: '联系客服',
						image: '../../static/images/my/6.png',
						url: '/my/setting/chat'
					}, {
						id: 7,
						name: '地址管理',
						image: '../../static/images/my/7.png',
						url: '/my/address/index'
					}, {
						id: 8,
						name: '跑腿红包',
						image: '../../static/images/my/9.png',
						url: '/my/hongbao/hongbao'
					}, 
					{
						id: 9,
						name: '聊天室',
						image: '../../static/images/order/kefu.png',
						url: '/my/chat/index',
						messageCount: 0
					},
					{
						id: 10,
						name: '系统设置',
						image: '../../static/images/my/8.png',
						url: '/my/setting/index'
					},
				],
				mylist1: [{
					id: 3,
					name: '消息中心',
					image: '../../static/images/my/3.png',
					url: '/my/msg/index'
				}, {
					id: 6,
					name: '联系客服',
					image: '../../static/images/my/6.png',
					url: '/my/setting/chat'
				}, {
					id: 7,
					name: '地址管理',
					image: '../../static/images/my/7.png',
					url: '/my/address/index'
				}, {
						id: 9,
						name: '聊天室',
						image: '../../static/images/order/kefu.png',
						url: '/my/chat/index'
					},
					{
						id: 10,
						name: '系统设置',
						image: '../../static/images/my/8.png',
						url: '/my/setting/index'
					}, ],
				avatar: '',
				userName: '',
				checkCertification: -1,
				arr: [],
				showModal: true,
				msgData: {
					userIntegral: 0,
					countCoupon: 0,
					userMoney: 0
				},

				tuiguang: '',
				tuiguangImg: '',
				token: '',
				XCXIsSelect: '否',
				shopStatus: '',
				isVip: false,
				messageCount: 0,
				time: ''
			}
		},
		onLoad() {
			let that = this
			that.time = setInterval(function() {
				that.messageCount = uni.getStorageSync('messageCount')
				if(that.messageCount) {
					that.mylist[7].messageCount = that.messageCount
				}else {
					that.mylist[7].messageCount = 0
				}
			},3000)
			this.getZiZhi()
			this.XCXIsSelect = this.$queue.getData('XCXIsSelect')?this.$queue.getData('XCXIsSelect'): '是'
			console.log("this.XCXIsSelect___:" + this.XCXIsSelect)
		},
		onHide() {
			clearInterval(this.time)
		},
		onShow() {
			let that = this
			// this.avatar = this.$queue.getData('avatar') || '';
			// this.userName = this.$queue.getData('userName') || '';
			this.token = this.$queue.getData("token")?this.$queue.getData("token"):''

			if (this.token) {
				this.getUserInfo();
				this.getMsgData()
				
				that.messageCount = uni.getStorageSync('messageCount')
				if(that.messageCount) {
					that.mylist[7].messageCount = that.messageCount
				}else {
					that.mylist[7].messageCount = 0
				}
				
			} else {
				this.isVip = false
				this.userName = ''
				this.avatar = ''
				this.msgData.userIntegral = 0
				this.msgData.countCoupon = 0
				this.msgData.userMoney = 0
			}
		},
		onShareAppMessage(res) { //发送给朋友
			return {
				title: this.tuiguang,
				path: '/pages/index/index',
				imageUrl: this.tuiguangImg,
			}
		},
		onShareTimeline(res) { //分享到朋友圈
			return {
				title: this.tuiguang,
				path: '/pages/index/index',
				imageUrl: this.tuiguangImg,
			}
		},
		methods: {
			getMsgData() {
				this.$Request.get("/app/userintegral/findUserMessage").then(res => {
					if (res.code == 0) {
						this.msgData = res.data
					}
				});
			},
			// 分享文案和图片
			getZiZhi() {
				this.$Request.getT('/app/common/type/239').then(res => {
					if (res.code === 0) {
						this.tuiguang = res.data.value;
					}
				});
				this.$Request.getT('/app/common/type/238').then(res => {
					if (res.code === 0) {
						this.tuiguangImg = res.data.value;
					}
				});
			},
			goSwt(e) {
				uni.setStorageSync('current', e)
				setTimeout(function() {
					uni.switchTab({
						url: '/pages/order/index',
					})
				}, 10)
			},
			goApplet(e) {
				uni.navigateToMiniProgram({
					appId: e,
					path: 'pages/index/index',
					success(res) {
						// 打开成功
					}
				})
			},
			goNav(e) {
				if (this.token) {
					if (e.name == '注册骑手') {
						uni.navigateToMiniProgram({
							appId: 'wx5ed22ce813e47796',
							path: '/pages/login/login',
							extraData: {
								'data1': 'test'
							},
							success(res) {
								// 打开成功
								console.log("打开成功")
							}
						})
					} else if (e.name == '分享好友') {
						uni.share({
							provider: "weixin",
							scene: "WXSceneSession",
							type: 1,
							summary: "我正在使用HBuilderX开发uni-app，赶紧跟我一起来体验！",
							success: function(res) {
								console.log("success:" + JSON.stringify(res));
							},
							fail: function(err) {
								console.log("fail:" + JSON.stringify(err));
							}
						});
					} else {
						uni.navigateTo({
							url: e.url
						})
					}

				} else {
					this.bindlogin();
				}
			},
			goShop(url) {
				if (this.token) {
					uni.navigateTo({
						url
					})
				} else {
					this.bindlogin();
				}
			},
			getUserInfo() {
				this.$Request.getT('/app/user/selectUserMessage').then(res => {
					console.log(res)
					if (res.code == 0) {
						if (parseInt(res.data.checkCertification)) {
							this.checkCertification = parseInt(res.data.checkCertification)
						} else {
							this.checkCertification = -1;
						}
						this.isVip = res.data.isVip
						this.shopStatus = res.data.shopStatus
						this.$queue.setData("avatar", res.data.avatar ? res.data.avatar :
							'../../static/logo.png');
						this.$queue.setData("userId", res.data.userId);
						this.$queue.setData("status", res.data.status);
						this.$queue.setData("userName", res.data.userName ? res.data.userName : res
							.data.nickName);
						this.avatar = res.data.avatar ? res.data.avatar : '../../static/logo.png';
						this.userName = res.data.userName ? res.data.userName : res.data.nickName
					}
				});
			},
			bindlogin() {
				if (!this.token) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},
			bindTo(name) {
				console.log(name)
				if (this.token) {
					if (name == '我的红包') {
						uni.navigateTo({
							url: '/pages/my/hongbao/hongbao'
						})
					} else if (name == '注册骑手') {
						uni.navigateToMiniProgram({
							appId: 'wx5ed22ce813e47796',
							path: '/pages/index/index',
							extraData: {
								'data1': 'test'
							},
							success(res) {
								// 打开成功
								console.log("打开成功")
							}
						})
					} else if (name == '意见反馈') {
						uni.navigateTo({
							url: '/pageA/feedback/feedback'
						})
					} else if (name == '联系客服') {
						uni.navigateTo({
							url: '/pageA/kefu/kefu'
						})
					} else if (name == '系统设置') {
						uni.navigateTo({
							url: '/pages/my/set/set'
						})
					} else if (name == '地址管理') {
						uni.navigateTo({
							url: '/pageA/address/address'
						})
					}
				} else {
					this.bindlogin();
				}
			},
			bindapprove() {

				if (this.token) {
					uni.navigateTo({
						url: '/pages/my/approve/approve'
					})
				} else {
					this.bindlogin();
				}
			},
			binduser() {

				if (this.token) {
					// uni.navigateTo({
					// 	url: '/pages/my/userphone/userphone'
					// })
				} else {
					this.bindlogin();
				}
			}
		}
	}
</script>

<style>
	button::after {
		border: none;
		background-color: none;
	}

	button {
		position: relative;
		display: block;
		margin-left: auto;
		margin-right: auto;
		padding-left: 0px;
		padding-right: 0px;
		box-sizing: border-box;
		text-decoration: none;
		line-height: 1.35;
		overflow: hidden;
		color: #666666;
		background-color: #fff;
		width: 100%;
		height: 100%;
	}
	.btn-bg {
		width: 64px;
		height: 28px;
		background: linear-gradient(90deg, #CDA26E 0%, #DCB78A 100%);
		border-radius: 28px;
		text-align: center;
		line-height: 28px;
		
		color: '#604320'
	}
	body {
		background: #F5F5F5;
	}

	/* #ifndef MP-WEIXIN */
	page {
		background: #F2EDED;
	}

	/* #endif */

	.content {
		width: 100%;
	}

	.btn {
		font-size: 24upx;
		/* width: 95%; */
		text-align: center;
		background: #FFFFFF;
		margin-top: 6rpx;
	}

	.head {
		/* width: 100%; */

		/* height: 200rpx; */
		display: flex;
		align-items: center;
		padding: 30rpx;
		border-radius: 16rpx;
		background-image: linear-gradient(#FEFBDA, #F7F7F7);
	}

	.head_image {
		
	}

	.head_image > image {
		width: 90rpx;
		height: 90rpx;
		border-radius: 50%
	}

	.head_name {
		margin-left: 10rpx;
	}

	.name {

		font-size: 38rpx;
		font-weight: bold;
	}

	.approve {
		position: absolute;
		top: 100rpx;
		font-size: 24rpx;
		color: #999999;
	}

	/* 列表 */
	.use_list {
		width: 100%;
		background: #ffffff;
		margin-top: 20rpx;
	}

	.list_box {
		width: 90%;
		margin: 0 auto;
		display: flex;
		height: 110rpx;
	}

	.box_left {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.box_right {
		flex: 1;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		color: #808080;
	}

	.use_name {
		margin-left: 30rpx;
		font-size: 32rpx;
	}

	.use_image image {
		width: 50rpx;
		height: 50rpx;
	}

	.center {
		width: 94%;

		/* line-height: 1.5; */
		background-color: #FFFFFF;
		border-radius: 18rpx;
		margin: 0 auto 0;
		display: flex;
		justify-content: space-between;
	}

	.header_text2 {
		font-size: 24rpx;
		font-weight: 500;
		color: #999999;
		margin-top: 10rpx;
	}

	.header_text4 {
		font-size: 32rpx;
		font-weight: bold;
		color: #333333;
	}
</style>
