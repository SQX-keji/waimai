<template>
	<view>
		<view style="width: 100%;padding-bottom: 140rpx;">
			<view style="display: flex;flex-direction: column;" v-for="(item,index) in ListItem" :key='index'>
				<view style="margin-top: 15rpx;width: 100%;text-align: center;font-size: 26rpx;color: #999999;">
					{{item.createTime}}
				</view>

				<view v-if="item.userId === userId" style="width: 83%;margin-left: 15%;">
					<view class="chat-listitem" style="float: right;">
						<view v-if="item.content && item.messageType === 1" @longpress="copy(item.content)"
							class="chat-listitem-text" style="margin-right: 20rpx;">{{item.content}}</view>
						<image @tap="viewImg(item.content)" v-if="item.content && item.messageType === 2"
							:src="item.content" style="height: 200rpx;width: 200rpx;margin-right: 20rpx;"></image>
						<view>
							<image v-if="item.avatar" :src="item.avatar" class="chat-listitem-image"></image>
							<image v-else src="../../../static/logo.png" class="chat-listitem-image"></image>
						</view>
					</view>
				</view>
				<view v-if="item.userId != userId" style="width: 83%;margin-right: 15%;">
					<view class="chat-listitem" style="float: left;margin-left: 10rpx;">
						<view v-if="item.shopId">
							<image :src="item.shopCover" class="chat-listitem-image"></image>
						</view>
						<view v-if="item.riderId">
							<image :src="item.riderAvatar" class="chat-listitem-image"></image>
						</view>
						<view v-if="item.content && item.messageType === 1" class="chat-listitem-text"
							style="margin-left: 20rpx;">{{item.content}}</view>
						<image @tap="viewImg(item.content)" v-if="item.content && item.messageType === 2"
							:src="item.content" style="height: 200rpx;width: 200rpx;margin-left: 20rpx;"></image>
					</view>
				</view>
			</view>
		</view>
		<!-- 底部聊天输入框 -->
		<view class="input-box ">
			<view class="justify-between padding-lr align-center"
				style="display: flex;width: 100%;background-color: #EEEEEE;">
				<image src="../../../static/images/msg/add.png" @click="chooseImage(['album'])"
					style="width: 50rpx;height: 50rpx;margin-right: 12rpx;border-radius: 52upx;"></image>
				<input confirm-type="send" @confirm='setChatSave(1)' type="text" v-model="content"
					style="width: 72%;height: 70rpx;background: #fff;margin: 0 10rpx;border-radius: 5rpx;padding-left: 10rpx;" />
				<view class="save" @tap='setChatSave(1)'>发送</view>
			</view>
		</view>
		
	</view>
</template>

<script>
	import configdata from '../../../common/config.js';
	export default {
		data() {
			return {
				connected: false,
				connecting: false,
				msg: false,
				type4: [],
				listRight: {
					chat: {
						userHead: ""
					},
					content: "",
					sendType: 1,
					type: 1
				},
				content: '',
				chatId: '',
				type: 1,
				ListItem: [],
				ShopState: false,
				ShopordersId: '',
				Shopimage: '',
				Shopmoney: '',
				ShopTitle: '',
				orderState: false,
				ordersId: '',
				userId: '',
				orderimage: '',
				orderNum: '',
				teamId: '',
				hand: 1,
				index: 0,
				page: 0,
				size: 1000,
				countDown: '',
				ordersId: '',
				byUserId: '',
				RiderUnreadCount: 0,
			};
		},
		computed: {
			showMsg() {
				if (this.connected) {
					if (this.msg) {
						return '收到消息：' + this.msg
					} else {
						return '等待接收消息'
					}
				} else {
					return '尚未连接'
				}
			}
		},
		onUnload() {
			// uni.closeSocket()
			uni.hideLoading()
		},
		onLoad(d) {
			this.userId = this.$queue.getData('userId');
			this.byUserId = d.byUserId
			this.ordersId = d.ordersId;
			this.connect();
			
			// if (d.teamName) {
			// 	uni.setNavigationBarTitle({
			// 		title: d.teamName
			// 	});
			// }
		},
		onShow() {
			if (this.connected || this.connecting) {

			} else {
				this.connect();
			}
		},
		onHide() {
			// uni.closeSocket()
		},
		onUnload() {
			this.close()
		},
		methods: {
			copy(content) {
				uni.showModal({
					title: '温馨提示',
					content: '确认要复制此文字吗？',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							uni.setClipboardData({
								data: content,
								success: r => {
									this.$queue.showToast('复制成功');
								}
							});
						}
					}
				});
			},
			getDateDiff(data) {
				// 传进来的data必须是日期格式，不能是时间戳
				//var str = data;
				//将字符串转换成时间格式
				var timePublish = new Date(data);
				var timeNow = new Date();
				var minute = 1000 * 60;
				var hour = minute * 60;
				var day = hour * 24;
				var month = day * 30;
				var result = "2";

				var diffValue = timeNow - timePublish;
				var diffMonth = diffValue / month;
				var diffWeek = diffValue / (7 * day);
				var diffDay = diffValue / day;
				var diffHour = diffValue / hour;
				var diffMinute = diffValue / minute;


				if (diffMonth > 3) {
					result = timePublish.getFullYear() + "-";
					result += timePublish.getMonth() + "-";
					result += timePublish.getDate();
				} else if (diffMonth > 1) { //月
					result = data.substring(0, 10);
				} else if (diffWeek > 1) { //周
					result = data.substring(0, 10);
				} else if (diffDay > 1) { //天
					result = data.substring(0, 10);
				} else if (diffHour > 1) { //小时
					result = parseInt(diffHour) + "小时前";
				} else if (diffMinute > 1) { //分钟
					result = parseInt(diffMinute) + "分钟前";
				} else {
					result = "刚刚";
				}
				return result;
			},

			connect() {
				let that = this;
				let userId = that.$queue.getData('userId');
				if (that.connected || that.connecting) {
					uni.showModal({
						content: '正在连接或者已经连接，请勿重复连接',
						showCancel: false
					})
					return false
				}
				that.connecting = true
				uni.showLoading({
					title: '连接中...'
				})
				console.log(userId, '*******************')
				uni.connectSocket({
					// url: 'ws://192.168.1.17:8881/gameTeamChat/' + userId + '_' + this.teamId,
					// url: 'wss://game.shengqianxiong.com.cn/wss/gameTeamChat/' + userId + '_' + this.teamId,
					// url: 'ws://192.168.1.17:8180/sqx_fast/chatSocket/' + userId,
					url: this.config("WSHOST1") + this.ordersId,
					data() {
						return {
							msg: 'Hello'
						}
					},
					header: {
						'content-type': 'application/json'
					},
					method: 'GET',
					success(res) {
						uni.hideLoading();
						that.getTimeOrListItem1();
						// 这里是接口调用成功的回调，不是连接成功的回调，请注意
					},
					fail(err) {
						// 这里是接口调用失败的回调，不是连接失败的回调，请注意
						console.log("--------------" + JSON.stringify(err))
					}
				});
				uni.onSocketOpen((res) => {
					that.connecting = false
					that.connected = true
					uni.hideLoading()
					// uni.showToast({
					// 	icon: 'none',
					// 	title: '连接成功'
					// })
					console.log('onOpen', res);
				});
				uni.onSocketError((err) => {
					that.connecting = false
					that.connected = false
					uni.hideLoading()
					uni.showModal({
						content: '网络较差，请稍后再试',
						showCancel: false
					})
					console.log('onError', err);
				});
				uni.onSocketMessage(function(res) {
					console.log('收到服务器内容1111：' + JSON.stringify(res));

					setTimeout(() => {
						that.getTimeOrListItem1();
					}, 50);

				});
				uni.onSocketClose((res) => {
					that.connected = false
					that.startRecive = false
					that.msg = false
					console.log('onClose', res)
				});
			},
			close() {
				uni.closeSocket()
			},
			getTimeOrListItem1() {
				this.$Request.getMsg('/app/ordersChat/selectGameChatDetails?type=1&page=1&limit=1000&ordersId=' + this.ordersId)
					.then(
						res => {
							this.ListItem = [];
							if (res.data) {
								var time = '';
								res.data.list.forEach(d => {
									if (!d.avatar) {
										// d.chat.userHead = '../../static/logo.png';
										let avatar = this.$queue.getData('avatar');
										d.avatar = avatar
									}
									this.ListItem.push(d);
								});
								this.ListItem = this.ListItem.reverse();
								setTimeout(() => {
									uni.pageScrollTo({
										scrollTop: 99999,
										duration: 0
									});
								}, 50);
							}
							uni.hideLoading();
						});
			},
			getChatSave() {
				let userId = this.$queue.getData('userId');
				let phone = this.$queue.getData('phone');
				let userName = this.$queue.getData('userName');
				if (!phone) {
					phone = this.$queue.getData('userName');
				}
				let avatar = this.$queue.getData('avatar');
				let data = {
					userId: userId,
					userHead: avatar,
					userName: userName,
					storeId: '0',
					storeHead: '同城外卖',
					storeName: ''
				}
				this.$Request.postJson('/chat/save', data).then(res => {
					if (res.status === 0) {
						this.chatId = res.data.chatId;
						uni.showLoading({
							title: '加载中...'
						});
						this.getTimeOrListItem1();
					}
				});
			},
			setChatSave(type) {
				//type:1文字 2图片
				if (type === 1 && this.content == '') {
					this.$queue.showToast('请输入聊天内容');
					return;
				}
				// if (this.chatId == '' || this.chatId == undefined) {
				// 	this.$queue.showToast('网络较差，请稍后再试');
				// 	return;
				// }
				let userId = this.$queue.getData('userId');
				let avatar = this.$queue.getData('avatar');
				let phone = this.$queue.getData('phone');
				let userName = this.$queue.getData('userName');
				if (!phone) {
					phone = this.$queue.getData('userName');
				}
				console.log(this.byUserId)
				let data = {
					content: this.content,
					messageType: type,
					userId: userId,
					ordersId: this.ordersId,

				}
				data = JSON.stringify(data);
				let that = this;
				uni.sendSocketMessage({
					data: data,
					success(res) {

						let avatar = that.$queue.getData('avatar');
						if (!avatar) {
							avatar = '../../static/logo.png';
						}
						let data = {
							chat: {
								userHead: avatar
							},
							content: that.content,
							type: type,
							userId: userId
						}
						console.log(data, 'data99999999999999999')

						setTimeout(() => {
							that.getTimeOrListItem1();
						}, 50);
						console.log(that.content);
					},
					fail(err) {
						console.log(err);
					}
				})
				this.content = '';
			},
			//发送图片
			chooseImage(sourceType) {
				uni.chooseImage({
					count: 1,
					sourceType: ['album', 'camera'],
					success: res => {
						for (let i = 0; i < res.tempFilePaths.length; i++) {
							this.$queue.showLoading("上传中...");
							uni.uploadFile({ // 上传接口
								url: this.config("APIHOST1") + '/alioss/upload', //真实的接口地址
								filePath: res.tempFilePaths[i],
								name: 'file',
								success: (uploadFileRes) => {
									console.log(uploadFileRes)
									this.content = JSON.parse(uploadFileRes.data).data;
									this.setChatSave(2);
									uni.hideLoading();
								}
							});
						}
					}
				})
			},
			config: function(name) {
				var info = null;
				if (name) {
					var name2 = name.split("."); //字符分割
					if (name2.length > 1) {
						info = configdata[name2[0]][name2[1]] || null;
					} else {
						info = configdata[name] || null;
					}
					if (info == null) {
						let web_config = cache.get("web_config");
						if (web_config) {
							if (name2.length > 1) {
								info = web_config[name2[0]][name2[1]] || null;
							} else {
								info = web_config[name] || null;
							}
						}
					}
				}
				return info;
			},
			//查看大图
			viewImg(item) {
				let imgsArray = [];
				imgsArray[0] = item;
				uni.previewImage({
					current: 0,
					urls: imgsArray
				});
			},
			
		},
	};
</script>

<style>
	page {
		/* background: #1c1b20; */
	}

	.input-box {
		position: fixed;
		bottom: 0;
		left: 0;
		height: 120rpx;
		width: 100%;
		display: flex;
		box-sizing: content-box;
		z-index: 999;
		/* background-color: #ececec; */
		/* padding: 0 5rpx; */
	}

	.chat-listitem {
		display: flex;
		margin-top: 20rpx;
		padding: 10rpx;
	}

	.chat-listitem-text {
		color: #000000;
		background: #FFFFFF;
		margin-top: 10rpx;
		width: fit-content;
		padding: 15rpx;
		font-size: 30rpx;
		height: max-content;
		word-wrap: break-word;
		word-break: break-all;
		border-radius: 10rpx;
	}

	.chat-listitem-image-type4 {
		color: #000000;
		background: #FFFFFF;
		width: fit-content;
		font-size: 30rpx;
		height: max-content;
		word-wrap: break-word;
		word-break: break-all;
		border-top-left-radius: 20rpx;
		border-top-right-radius: 20rpx;
	}

	.chat-listitem-image {
		margin-top: 5rpx;
		width: 75rpx;
		height: 75rpx;
		border-radius: 5rpx;
	}

	.save {
		width: 130rpx;
		text-align: center;
		border-radius: 70rpx;
		height: 70rpx;
		color: #FFF;
		background: #1789FD;
		margin: 5rpx 10rpx 0;
		line-height: 70rpx;
	}

	
</style>
