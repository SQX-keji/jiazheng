<template>
	<view>
		<view style="width: 100%;height: 490rpx;">
			<image class="u-skeleton-fillet" style="width: 100%;height: 100%;" :src="order.homepageImg"></image>
		</view>
		<view class="padding-sm bgs">
			<view class=" padding flex justify-between bg radius u-skeleton-fillet">
				<!-- <u-avatar :src="order.avatar?order.avatar:'../../../static/logo.png'" mode="square"></u-avatar> -->
				<view class="flex-sub">
					<view class=" flex justify-between align-center">
						<view class="text-xxl" style="color: #557EFD;">
							<text class="text-df ">￥</text>{{isVip? order.memberMoney :order.money}}<text
								style="font-size: 28rpx;color: #000000;">元/{{order.unit}}</text>
							<text class=""
								style="color: #999999;font-size: 25rpx;margin-left: 17rpx;">最低{{order.minNum}}{{order.unit}}</text>
						</view>
						<view style="color: #557EFD;background-color: #E1E8FF;font-size: 25rpx;padding: 8upx 10upx;">
							<text>{{order.region?order.region:'不限地区'}}</text>
						</view>
					
					</view>
					<view class="text-lg margin-top-sm margin-bottom-sm u-line-1" style="width: 600rpx;">
						{{order.myLevel}}</view>
					<view class="margin-top-xs ">
						<text class="padding-xs margin-right-xs text-sm"
							style="color: #557EFD;background-color: #E1E8FF;"
							v-for="(item,index) in order.gameName">{{item}}</text>

					</view>
				</view>
			</view>

			<view class="margin-top-sm bg radius u-skeleton-fillet">
				<view class="text-center padding">服务详情</view>
				<!-- <image src="../../../static/images/detail.png" style="width: 100%;"></image> -->
				<image v-for="(item,index) in order.detailsImg" :key="index" :src="item" style="width: 100%;"
					mode="widthFix"></image>
			</view>

		</view>


		<view class="text-white flex justify-between cu-bar foot bg padding-lr" v-if="myId != order.userId">
			<view @click="follow" class="padding-lr" style="width: 120rpx;">
				<image src="../../../static/images/index/guanzhu.png" v-if="!isFollow"
					style="width: 49rpx;height: 43rpx;"></image>
				<image src="../../../static/images/index/guanzhu1.png" v-else style="width: 49rpx;height: 43rpx;">
				</image>
				<view style="font-size: 22upx;margin-top: 8upx;">关注</view>
			</view>
			<view @click="goMsg" class="padding-lr" style="width: 120rpx;">
				<image src="../../../static/images/index/im.png" style="width: 49rpx;height: 43rpx;"></image>
				<view style="font-size: 22upx;margin-top: 8upx;">聊天</view>
			</view>
			<view class="flex-sub">
				<u-button :custom-style="customStyle" @click="goNav()" shape="circle" :hair-line="false">立即下单</u-button>
			</view>
		</view>
		<!-- <u-skeleton :loading="loading" :animation="true" elColor='#1E1F31' bgColor='#ffffff' ></u-skeleton> -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loading: true, // 是否显示骨架屏组件
				customStyle: {
					width: '400upx',
					color: '#FFFFFF',
					background: "#2087fe",
					border: 0
				},
				id: '',
				orderDet: {},
				page: 1,
				limit: 10,
				order: {},
				commentList: [],
				isFollow: false,
				myId: '',
				isVip: false,
				AUDIO: uni.createInnerAudioContext(),
				isPlay: false
			}
		},
		onLoad(option) {
			this.id = option.id
			this.getDet()
			this.getOrderComment()
			this.myId = uni.getStorageSync('userId')
			this.isVip = uni.getStorageSync('isVIP')
			console.log(this.isVip)
		},
		onReady() {
			this.AUDIO.onEnded(function(res) {
				this.isPlay = false;
			});
		},
		methods: {
			// 详情
			getDet() {
				this.$Request.get("/app/orderTaking/queryTakingDetails", {
					id: this.id,
				}).then(res => {
					if (res.code == 0) {
						this.order = res.data
						this.order.gameName = res.data.gameName.split(',')
						this.order.detailsImg = res.data.detailsImg.split(',')
						// if (this.order.region) {
						
						// 	let region = this.order.region.split(",");
						// 	this.order.region = region[1]
						// } else {
						// 	this.order.region = '不限地区'
						// }
						this.selectFollow()
					} else {

					}
				});
			},
			// 评论
			getOrderComment() {
				this.$Request.get("/app/takingComment/selectOrderTakingComment", {
					id: this.id,
					page: this.page,
					limit: this.limit
				}).then(res => {
					if (res.code == 0) {
						this.commentList = [...this.commentList, ...res.data.list]
					}
				});
			},
			// 是否关注
			selectFollow() {
				this.$Request.get("/app/userFollow/selectFollowUser", {
					followUserId: this.order.userId
				}).then(res => {
					if (res.data == true) {
						this.isFollow = true
					} else {
						this.isFollow = false
					}
					this.loading = false;
				});
			},
			playVoice() {
				console.log(this.isPlay)
				this.AUDIO.src = this.order.voiceIntroduce;
				if (this.isPlay) {
					this.AUDIO.stop();
				} else {
					this.AUDIO.play();
				}
				this.isPlay = !this.isPlay;
			},
			goNav() {
				uni.navigateTo({
					url: "/pages/index/game/orderDet?id=" + this.id
				})
			},
			goMsg() {
				let data = {
					userId: this.myId,
					focusedUserId: this.order.userId
				}
				this.$Request.postJson('/app/chat/insertChatConversation ', data).then(res => {
					if (res.code == 0) {
						let id = this.order.userId == res.data.userId ? res.data.focusedUserId : this.order.userId
						uni.navigateTo({
							url: '/pages/msg/im?chatConversationId=' + res.data.chatConversationId +
								'&byUserId=' + id
						})
					}
				})
			},
			// 关注
			follow() {
				let that = this
				that.$Request.get("/app/userFollow/insert", {
					followUserId: that.order.userId
				}).then(res => {
					uni.showToast({
						title: res.msg,
						icon: 'none'
					})
					setTimeout(function() {
						that.selectFollow()
					}, 500)
				});
			}
		}

	}
</script>

<style>
	page {
		background-color: #F5F5F5;
	}

	.bg {
		background: #FFFFFF;
	}

	.line_s {
		display: inline-flex;
		width: 10rpx;
		height: 10rpx;
		background: #1AD566;
		border-radius: 50%;
		margin-right: 10rpx;
	}

	.line_x {
		display: inline-flex;
		width: 10rpx;
		height: 10rpx;
		background: #000000;
		border-radius: 50%;
		margin-right: 10rpx;
	}
</style>
