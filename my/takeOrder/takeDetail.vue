<template>
	<view class="padding">
		<!-- <view class="bg padding radius">
			<view class="flex">
				<view class="">
					<image src="../../static/images/place1.png" style="width: 80rpx;height: 80rpx;"></image>
				</view>
				<view class="text-white margin-left-sm">
					<view>欢乐<text>19999999999999</text></view>
					<view class="margin-top-xs">西安智能大厦</view>
				</view>
			</view>
		</view> -->
		<view class="bg padding radius">
			<view class=" u-flex " @click="binddetail(order)">
				<view class="u-m-r-10">
					<!-- <u-avatar :src="order.user.avatar" size="68"></u-avatar> -->
					<image :src="order.orderTaking.homepageImg" style="width: 140rpx;height: 140rpx;"></image>
				</view>
				<view class="u-flex-1 text-white">
					<view class="u-font-14  text-bold padding-bottom-xs u-line-1" style="width: 480rpx;">
						{{order.orderTaking.myLevel}}
					</view>
					<!-- <view class="margin-top-xs margin-bottom-xs "><text class="padding-xs text-sm">{{order.orderNumber}}</text></view> -->
					<view class="padding-top-xs text-lg">
						<text style="font-size: 20rpx;">￥</text>
						{{order.orderTaking.oldMoney}}元/{{order.orderTaking.unit}}<text
							style="font-size: 20rpx;margin-left: 10rpx;">*</text>
						<text style="color: #666666;">{{order.orderNumber}}{{order.orderTaking.unit}}</text>
					</view>
				</view>
			</view>
			<view class="flex justify-between margin-top">
				<view style="width: 120upx;">实付款</view>
				<view class="text-white text-lg">
					<text class="text-sm">￥</text>{{price}}
				</view>
			</view>
		</view>

		<view class="bg padding radius margin-top">
			<view class="text-lg text-bold">
				服务信息
			</view>
			<view class="margin-right-xs">
				<view class="flex justify-between margin-top-lg">
					<view class="text-bold" style="width: 165rpx;">服务类型</view>
					<view class="text-white">
						<text v-for="(item,index) in order.orderTaking.gameId" :key="index"
							class="margin-left-sm">{{item}}</text>
					</view>
				</view>
				<view class="flex justify-between margin-top-lg">
					<view class="text-bold" style="width: 165rpx;">服务时间</view>
					<view class="text-white">{{order.startTime}}时</view>
				</view>
				<view class="flex justify-between margin-top-lg">
					<view class="text-bold" style="width: 165rpx;">服务地点</view>
					<view class="text-white" @tap="bindGps(order.latitude,order.longitude,order.detailsAddress)">
						{{order.city}}{{order.district}}{{order.detailsAddress}}
						<u-icon name="map"></u-icon>
					</view>
				</view>
				<view class="flex justify-between margin-top-lg">
					<view class="text-bold" style="width: 165rpx;">联系方式</view>
					<view class="text-white" @click="bindphone(order.phone)">{{order.phone}}<u-icon name="phone"></u-icon>
					  <text class="margin-left-sm"> {{order.name}}</text></view>
				</view>
				<view class=" margin-top-lg">
					<view class="text-bold" style="width: 165rpx;">备注</view>
					<view class="text-white margin-top">{{order.remarks}}</view>
				</view>
			</view>
			<!-- <view class="flex justify-between margin-top">
				<view @click="goMsg">
					<u-icon name="chat" size="32" color="#557EFD" class="margin-right-sm"></u-icon>联系TA
				</view>
				<view class="text-white">实付：<text class="text-lg text-bold">{{order.payMoney}}元</text></view>
			</view> -->
		</view>
		<view class="bg padding radius margin-top" style="margin-bottom: 140rpx;">
			<view class="text-lg text-bold">
				订单信息
			</view>
			<view class="margin-right-xs">
				<view class="flex justify-between margin-top-lg">
					<view class="text-bold" style="width: 165rpx;">订单编号</view>
					<view class="text-white">{{order.ordersNo}}</view>
				</view>
				<view class="flex justify-between margin-top-lg">
					<view class="text-bold" style="width: 165rpx;">下单时间</view>
					<view class="text-white">{{order.createTime}}</view>
				</view>
				<!-- <view class="flex justify-between margin-top-lg">
					<view style="width: 120upx;">支付方式</view>
					<view class="text-white">{{order.createTime}}</view>
				</view>
 -->
			</view>
		</view>

		<!-- <view class="flex tabber padding-top-sm padding-bottom-sm align-center" v-if="isTrue == 0">
			<u-button @click="cancelOrder(order)" shape="circle" class="margin-right-sm " :custom-style="customStyle"
				:hair-line="false">取消订单
			</u-button>
			<u-button @click="pay" class="margin-right-sm " shape="circle" :custom-style="customStyle2"
				:hair-line="false">立即支付
			</u-button>
		</view> -->
		<view class="flex tabber padding-top-sm padding-bottom-sm align-center margin-right-xs padding-right"
			v-if="order.state ==1">
			<u-button v-if="order.state == 1" :custom-style="customStyle" shape="circle" :plain="true"
				@click="cancelOrder(order,3)">拒接接单</u-button>
			<!-- <u-button v-if="item.state == 3" :custom-style="customStyle" shape="circle" :plain="true" @click="delOrder(item)">拒接接单</u-button> -->
			<u-button v-if="order.state == 1" :custom-style="customStyle1" shape="circle" :plain="true"
				@click="cancelOrder(order,2)">完成接单</u-button>
			<!-- <u-button :custom-style="customStyle" shape="circle" :plain="true" @click="clickItem(item)">联系TA
			</u-button> -->
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				customStyle: {
					color: '#999999',
					border: '2rpx solid #999999',
					// backgroundColor: '#1E1F31',
					border: "8rpx",
					width: '180rpx',
					height: '54rpx',
					margin: "0 0 0 20rpx"
				},
				customStyle1: {
					color: '#557EFD',
					border: '2rpx solid #557EFD',
					// backgroundColor: '#1E1F31',
					border: "8rpx",
					width: '180rpx',
					height: '54rpx',
					margin: "0 0 0 20rpx"
				},
				id: '',
				order: {
					user: {},
					game: {}
				},
				isTrue: 0,
				vipMoney: '',
				data: [],
				price: ''
			}
		},
		onLoad(e) {
			console.log(e)
			var jsObject = JSON.stringify(e);
			console.log(jsObject)
			this.isTrue = e.isTrue
			if (this.isTrue) {
				uni.setNavigationBarTitle({
					title: '订单详情'
				})
			}
			this.id = e.id
			this.getOrder()
		},
		methods: {
			// 一键导航
			bindGps(latitude, longitude, name) {
				uni.openLocation({
					latitude: latitude - 0, //要去的纬度-地址       
					longitude: longitude - 0, //要去的经度-地址
					name: name, //地址名称
					address: name, //详细地址名称
					success: function() {
						console.log('导航成功');
					},
					fail: function(error) {
						console.log(error)
					}
				});
			},
			// 拨打电话
			bindphone(phone) {
				let that = this
				uni.showModal({
					title: '提示',
					content: '是否拨打电话',
					success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定', that.phone);
							uni.makePhoneCall({
								phoneNumber: phone //仅为示例
							});
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			binddetail(e) {
				console.log(e)
				uni.navigateTo({

					url: '/pages/index/game/order?id=' + e.orderTakingId
				})
			},
			getOrder() {
				let data = {
					id: this.id
				}
				this.$Request.get('/app/orders/queryOrders', data).then(res => {
					if (res.code == 0) {
						this.order = res.data

						this.order.orderTaking.gameId = this.order.orderTaking.gameId.split(",");
						this.price = (res.data.orderTaking.oldMoney * res.data.orderNumber)
							.toFixed(2)
						this.vipMoney = (res.data.orderTaking.money * res.data.orderNumber - res.data.payMoney * 1)
							.toFixed(2)
						console.log(this.vipMoney, 'vipvipvipv')
					}
				})
			},
			// 取消订单
			cancelOrder(e, status) {
				let that = this
				let content = ''
				if (status == 3) {
					content = '确定拒绝接单吗？'
				} else if (status == 2) {
					content = '确定订单已经完成吗？如果未完成，客户投诉将采取封号处理'
				}
				uni.showModal({
					title: '提示',
					content: content,
					success: function(res) {
						if (res.confirm) {
							let data = {
								id: e.ordersId,
								status
							}
							that.$Request.get('/app/orders/cancelOrder', data).then(res => {
								if (res.code == 0) {
									that.getOrder()
								}
							})
						}
					},
				})

			},
			// 支付订单
			pay() {
				let that = this
				uni.showModal({
					title: '付款提示',
					content: '确认支付' + that.order.payMoney + '元吗?',
					success: function(re) {
						if (re.confirm) {
							console.log('用户点击确定');
							that.$Request.post("/app/orders/payMoney", {
								ordersId: that.order.ordersId,
							}).then(res => {
								if (res.code == 0) {
									uni.showToast({
										title: '支付成功'
									})
									setTimeout(function() {
										uni.navigateBack()
									}, 1000)
								} else {
									uni.showToast({
										title: res.msg,
										icon: 'none'
									})
								}
							});
						} else if (re.cancel) {
							console.log('用户点击取消');
						}
					}

				})

			},
		
			// cancelOrder(e) {
			// 	let data = {
			// 		id: e.ordersId,
			// 		status: '3'
			// 	}
			// 	this.$Request.get('/app/orders/cancelOrder', data).then(res => {
			// 		if (res.code == 0) {
			// 			uni.showToast({
			// 				title: '取消成功',
			// 				icon: 'none'
			// 			})
			// 			setTimeout(function() {
			// 				uni.navigateBack()
			// 			}, 1000)
			// 		}
			// 	})
			// },
			goMsg() {
				let data = {
					userId: uni.getStorageSync('userId'),
					focusedUserId: this.order.user.userId
				}
				this.$Request.postJson('/app/chat/insertChatConversation ', data).then(res => {
					if (res.code == 0) {
						let id = this.order.user.userId == res.data.userId ? res.data.focusedUserId : this.order
							.user.userId
						uni.navigateTo({
							url: '/pages/msg/im?chatConversationId=' + res.data.chatConversationId +
								'&byUserId=' + id
						})
					}
				})
			},
		}
	}
</script>

<style>
	page {
		background: #f7f7f7;
	}

	.bg {
		background: #FFFFFF;
	}

	.tabber {
		width: 100%;
		background: #ffffff;
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		justify-content: flex-end;
		height: 127rpx;
	}
</style>
