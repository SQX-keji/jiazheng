<template>
	<view class="">
		<view class="u-skeleton">
			<view class="u-skeleton-fillet" style="width: 100%;height: 490rpx;">
				<image class="" style="width: 100%;height: 100%;" :src="order.homepageImg"></image>
			</view>
			<view class="padding-sm bgs u-skeleton-fillet">
				<view class=" padding flex justify-between bg radius ">
					<!-- <u-avatar :src="order.avatar?order.avatar:'../../../static/logo.png'" mode="square"></u-avatar> -->
					<view class="flex-sub ">
						<view class=" flex justify-between align-center">
							<view class="text-xxl" style="color: #557EFD;">
								<text class="text-df ">￥</text>{{isVip? order.memberMoney :order.money}}<text
									style="font-size: 28rpx;color: #000000;">元/{{order.unit}}</text>
								<text class=""
									style="color: #999999;font-size: 25rpx;margin-left: 17rpx;">最低{{order.minNum}}{{order.unit}}</text>
							</view>
							<view
								style="color: #557EFD;background-color: #E1E8FF;font-size: 25rpx;padding: 8upx 10upx;">
								<text>{{order.region?order.region:'不限地区'}}</text>
							</view>

						</view>
						<view class="flex align-center justify-between">
							<view class="text-lg margin-top-sm margin-bottom-sm u-line-1" style="width: 500rpx;">
								{{order.myLevel}}
							</view>
							<view v-if="order.count">已服务{{order.count}}人</view>
						</view>
						<view class="margin-top-xs ">
							<text class="padding-xs margin-right-xs text-sm"
								style="color: #557EFD;background-color: #E1E8FF;" v-for="(item,index) in order.gameName"
								:key="index">{{item}}</text>

						</view>
						<view class="flex align-center margin-tb-sm" v-if="order.detailadd"
							@tap="bindGps(order.latitude,order.longitude,order.detailadd)">
							<u-icon name="map"></u-icon>
							<view class="margin-left-xs text-cut addbox">
								{{order.detailadd}}
							</view>
						</view>
					</view>
				</view>


				<view class="margin-top-sm padding bg text-white radius u-skeleton-fillet">
					<view class="text-lg">用户评价</view>
					<view v-if="commentList.length<=0" class="margin-top-sm"> 暂无评价</view>
					<view class="margin-tb-sm" v-for="(item, index) in commentList" :key='index' v-else>
						<view class="flex justify-between">
							<u-avatar :src="item.avatar" size="48"></u-avatar>
							<view class="flex-sub margin-left-sm" style="line-height: 46upx;">{{item.userName}}</view>
							<view class="flex">
								<u-icon v-for="ite in item.score" :key='ite' color="#2087fe" name="star-fill"></u-icon>
							</view>
						</view>
						<view class="margin-top-sm">{{item.content}}</view>
					</view>
				</view>
				<view class="margin-top-sm bg radius u-skeleton-fillet">
					<view class="text-center padding">服务详情</view>
					<!-- <image src="../../../static/images/detail.png" style="width: 100%;"></image> -->
					<view v-for="(item,index) in order.detailsImg" :key="index"
						@click="saveImg(order.detailsImg,index)">
						<image :src="item" style="width: 100%;" mode="widthFix"></image>
					</view>

				</view>

			</view>

			<view class="u-skeleton-fillet text-white flex justify-between cu-bar foot bg padding-lr"
				v-if="myId != order.userId">
				<!-- <view @click="follow" class="padding-lr" style="width: 120rpx;">
				<image src="../../../static/images/index/guanzhu.png" v-if="!isFollow"
					style="width: 49rpx;height: 43rpx;"></image>
				<image src="../../../static/images/index/guanzhu1.png" v-else style="width: 49rpx;height: 43rpx;">
				</image>
				<view style="font-size: 22upx;">关注</view>
			</view> -->
				<view @click="goMsg" class="padding-lr" style="width: 120rpx;">
					<image src="../../../static/images/index/im.png" style="width: 49rpx;height: 43rpx;"></image>
					<view style="font-size: 22upx;">聊天</view>
				</view>
				<view class="flex-sub">
					<u-button :custom-style="customStyle" @click="goNav()" shape="circle" :hair-line="false">立即预约
					</u-button>
				</view>
			</view>
		</view>
		<!-- <u-picker v-model="show" mode="time" :params="params" @confirm="statusChange"></u-picker> -->
		<u-skeleton :loading="loading" :animation="true" bgColor="#FFF"></u-skeleton>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				game: [],
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
				isPlay: false,
				show: false,
				params: {
					year: false,
					month: true,
					day: true,
					hour: true,
					minute: true,
					second: true,
					timestamp: true
				},
				startTime: '',
				mobile: '',
				name: '',
				cityaddress: '',
				detailaddress: '',
				latitude: '',
				longitude: '',
				province: '',
				city: '',
				district: '',

			}
		},
		onLoad(option) {
			// this.$queue.showLoading("加载中...");
			this.id = option.id
			this.getDet()
			this.getOrderComment()
			this.myId = uni.getStorageSync('userId')
			this.isVip = uni.getStorageSync('isVIP')

			console.log(this.isVip)

		},
		onShow() {

		},
		onReady() {
			this.AUDIO.onEnded(function(res) {
				this.isPlay = false;
			});
		},
		methods: {
			//详情图片放大
			saveImg(imgs, index) {
				// console.log(imgs)
				let that = this;
				let imgArr = imgs
				// imgArr.push(imgs);
				// //预览图片
				uni.previewImage({
					urls: imgArr,
					current: imgArr[index]
				});
			},
			// 地址
			bindAdd() {
				uni.navigateTo({
					url: '../../../my/address/address'
				})
			},
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
			// 详情
			getDet() {
				uni.showLoading({
					title: '加载中...',
					icon: 'none'
				});
				this.$Request.get("/app/orderTaking/queryTakingDetails", {
					id: this.id,
					latitude: uni.getStorageSync('latitude'),
					longitude: uni.getStorageSync('longitude')
				}).then(res => {
					uni.hideLoading();
					this.loading = false
					if (res.code == 0) {

						this.order = res.data
						this.order.gameName = this.order.gameName.split(",");
						this.order.detailsImg = this.order.detailsImg.split(",");
						// if (this.order.region) {

						// 	let region = this.order.region.split(",");
						// 	this.order.region = region[1]
						// } else {
						// 	this.order.region = '不限地区'
						// }
						this.selectFollow()
						// uni.hideLoading();
					} else {
						// this.loading = false;
						// uni.hideLoading();
					}
					// this.loading = true;

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
				// if (this.startTime == '') {
				// 	uni.showToast({
				// 		icon: 'none',
				// 		title: '请选择上门时间'
				// 	});
				// 	return;
				// }
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
		background-color: #FFFFFF;
	}

	.bgs {
		background-color: #F7F7F7;
	}

	.bg {
		background-color: #FFFFFF;
	}

	.line_s {
		display: inline-flex;
		width: 14rpx;
		height: 14rpx;
		background: #1AD566;
		border-radius: 50%;
		margin-right: 10rpx;
	}

	.line_x {
		display: inline-flex;
		width: 14rpx;
		height: 14rpx;
		background: #000000;
		border-radius: 50%;
		margin-right: 10rpx;
	}

	.u-size-default {
		background: #557EFD !important;
	}
	.addbox{
		width: 600upx;
	}
</style>
