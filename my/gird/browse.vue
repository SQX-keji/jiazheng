<template>
	<view class="margin-lr-sm">
		<view v-if="dataList.length != 0" class="flex justify-between margin-top-16 bg padding-sm radius"
			@click="goOrder(item)" @longpress="delData(item)" v-for="(item,index) in dataList" :key="index">
			<image :src="item.orderTaking.homepageImg?item.orderTaking.homepageImg: '../../static/logo.png'"
				style="width: 200rpx;height: 200rpx;border-radius: 10rpx;"></image>
			<view class="flex-sub margin-left text-white flex flex-direction justify-between">
				<view class="flex justify-between">
					<view class="flex">
						<view v-if="item.orderTaking.authentication == 2||item.orderTaking.authentication == 3">
							<image src="../../static/images/qi.png" style="width: 28rpx;height: 28rpx;"></image>

						</view>
						<view class="" v-if="item.orderTaking.authentication == 1||item.orderTaking.authentication == 3">
							<image src="../../static/images/geren.png"
								style="width: 28rpx;height: 28rpx;margin-left: 10rpx;"></image>
						</view>
						<view class="margin-right-xs u-line-1" style="display: inline-block;width: 400rpx;margin-left: 10rpx;margin-top: -2px;">
							{{item.orderTaking.myLevel}}
						</view>
					</view>
				</view>
				<view class="flex radius" style="line-height: 34upx;">
					<view style="width: 100%;position: relative;line-height: 40rpx;color: #999999;">
						<text v-for="(item,index) in item.orderTaking.gameId" :key="index"
							class="margin-right-sm">{{item}}</text>
					</view>
				</view>
				<view class="flex" style="align-items: center;font-size: 24rpx;padding: 5rpx;"
					v-if="item.orderTaking.salesNum == ''">
					<view style="color: #999999;background: #F2F2F2; padding: 5rpx 10rpx;">
						已售{{item.orderTaking.salesNum}}</view>
				</view>
				<view style="width: 100%;display: flex;justify-content: space-between;align-items: center;">
					<view style="color:#FF1200;font-size: 31rpx;">
						￥{{isVip?item.orderTaking.memberMoney:item.orderTaking.money}}<text>/{{item.orderTaking.unit}}</text>
					</view>
					<!-- <view style="background: #557EFD;color: #ffffff;padding: 15rpx 25rpx;border-radius: 45rpx;">
							预约服务
						</view> -->
					<view>{{item.updateTime}}</view>
				</view>
				<!-- <view class="flex justify-between align-center">
					<view><text style="color: #1E1F31;">{{isVip?item.orderTaking.memberMoney:item.orderTaking.money}}元 </text> /{{item.orderTaking.unit}}</view>
					<view>{{item.updateTime}}</view>
				</view> -->
			</view>
		</view>

		<empty v-if="dataList.length == 0"></empty>
	</view>
</template>

<script>
	import empty from '../../components/empty.vue'
	export default {
		components: {
			empty
		},
		data() {
			return {
				dataList: [],
				page: 1,
				limit: 10,
				myId: '',
				isVip: false,
			}
		},
		onLoad(e) {
			this.$queue.showLoading("加载中...");
			this.myId = uni.getStorageSync('userId')
			this.isVip = uni.getStorageSync('isVIP') ? uni.getStorageSync('isVIP') : false
			this.getBrowseList()
		},
		methods: {
			// 足迹
			getBrowseList() {
				let data = {
					page: this.page,
					limit: this.limit
				}
				this.$Request.get("/app/userBrowse/myBrowse", data).then(res => {
					uni.hideLoading();
					if (res.code == 0) {
						if (this.page == 1) {
							this.dataList = res.data.list
							for (let i = 0; i < this.dataList.length; i++) {
								this.dataList[i].orderTaking.gameId = this.dataList[i].orderTaking.gameId.split(
									",");
							}
						} else {
							this.dataList = [...this.dataList, ...res.data.list]
							for (let i = 0; i < this.dataList.length; i++) {
								this.dataList[i].orderTaking.gameId = this.dataList[i].orderTaking.gameId.split(
									",");
							}
						}
					} else {
						console.log(res.msg)
					}
					uni.stopPullDownRefresh();
				})
			},
			// 跳转订单
			goOrder(e) {
				uni.navigateTo({
					url: '/pages/index/game/order?id=' + e.orderTaking.id
				});
			},
			// 删除
			delData(e) {
				let that = this
				uni.showModal({
					title: '提示',
					content: '确定删除吗？',
					success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定');
							let data = {
								id: e.id
							}
							that.$Request.post("/app/userBrowse/deleteMyBrowse", data).then(res => {
								if (res.code == 0) {
									uni.showToast({
										title: '删除成功！',
										icon: 'none'
									})
									that.getBrowseList()
								}
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				})
			}
		},
		onReachBottom: function() {
			this.page = this.page + 1;
			this.getBrowseList()
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.getBrowseList()
		},
	}
</script>

<style>
	page {
		background-color: #f7f7f7;
	}

	.bg {
		background: #ffffff;
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
