<template>
	<view>
		<ren-dropdown-filter :filterData='filterData' :border="false" :defaultIndex='defaultIndex' @onSelected='change'
			class="u-skeleton-rect">
		</ren-dropdown-filter>
		<view class="margin-lr-sm margin-top-16" v-if="dataList.length">
			<view class="flex justify-between radius bg padding-sm margin-top-16" @click="goOrder(item)"
				v-for="(item,index) in dataList" :key='index'>
				<image :src="item.homepageImg?item.homepageImg: '../../../static/logo.png'"
					style="width: 200rpx;height: 200rpx;border-radius: 10rpx;"></image>
				<view class="flex-sub margin-left text-white flex flex-direction justify-between">
					<view class="flex justify-between">
						<view class="flex">
							<view v-if="item.authentication == 2||item.authentication == 3">
								<image src="../../../static/images/qi.png" style="width: 28rpx;height: 28rpx;"></image>

							</view>
							<view class="" v-if="item.authentication == 1||item.authentication == 3">
								<image src="../../../static/images/geren.png"
									style="width: 28rpx;height: 28rpx;margin-left: 10rpx;"></image>
							</view>
							<view class="margin-right-xs u-line-1"
								style="display: inline-block;margin-left: 10rpx;width: 260rpx;margin-top: -2px;">
								{{item.myLevel}}
							</view>
						</view>
						<view>
							{{item.distance/1000}}km
						</view>
					</view>
					<view class="flex radius" style="line-height: 34upx;">
						<view style="width: 100%;position: relative;line-height: 40rpx;color: #999999;">
							<text v-for="(item,index) in item.gameName" class="margin-right-sm">{{item}}</text>
						</view>
					</view>
					<view class="flex" style="align-items: center;font-size: 24rpx;" v-if="item.count">
						<view style="color: #999999;background: #F2F2F2; padding: 5rpx 10rpx;">
							已服务{{item.count}}人
						</view>
					</view>
					<view style="width: 100%;display: flex;justify-content: space-between;align-items: center;">
						<view style="color:#FF1200;font-size: 31rpx;">
							￥{{isVip? item.memberMoney :item.money}}元/<text>{{item.unit}}</text>
						</view>
						<view style="background: #557EFD;color: #ffffff;padding: 15rpx 25rpx;border-radius: 45rpx;">
							预约服务
						</view>
					</view>
				</view>
			</view>
		</view>

		<empty v-if="!dataList.length"></empty>
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
				page: 1,
				limit: 10,
				dataList: [],
				isVip: false,
				myId: '',
				city: '',
				defaultIndex: [0, 0, 0],
				filterData: [
					[{
							label: '智能优选',
							value: '',
						},
						{
							label: '距离优先',
							value: 3,
						},
						{
							label: '人气优先',
							value: 2,
						},
						{
							label: '同城',
							value: 1,
						}
					],
					[{
							label: '销量',
							value: '',
						},
						{
							label: '从高到低',
							value: 'desc',
						},
						{
							label: '从低到高',
							value: 'asc',
						}
					],
					[{
							label: '价格',
							value: '',
						},
						{
							label: '从高到低',
							value: 'desc',
						},
						{
							label: '从低到高',
							value: 'asc',
						}
					],
				],
				value1: '',
				value2: '',
				value3: '',
			}
		},
		onLoad() {
			this.isVip = uni.getStorageSync('isVIP') ? uni.getStorageSync('isVIP') : false
			this.myId = uni.getStorageSync('userId')
			this.city = uni.getStorageSync('city')
			this.getDataList()
		},
		methods: {
			// 筛选
			change(e) {

				this.value1 = e[0][0].value
				this.value2 = e[1][0].value
				this.value3 = e[2][0].value
				this.getDataList()
				// this.mescroll.resetUpScroll()
			},
			getDataList() {
				this.$Request.get("/app/orderTaking/queryLowTaking", {
					page: this.page,
					limit: this.limit,
					
					condition: this.value1, //智能优选
					salesNum: this.value2, //不限男女
					by: this.value3, //价格
					city: this.city,
					latitude: uni.getStorageSync('latitude'),
					longitude: uni.getStorageSync('longitude')
				}).then(res => {
					if (res.code == 0) {
						if (this.page == 1) {
							this.dataList = res.data.list
							for (let i = 0; i < this.dataList.length; i++) {
								this.dataList[i].gameName = this.dataList[i].gameName.split(",");
							}
						} else {
							this.dataList = [...this.dataList, ...res.data.list]
							for (let i = 0; i < this.dataList.length; i++) {
								this.dataList[i].gameName = this.dataList[i].gameName.split(",");
							}
						}
					}
					uni.stopPullDownRefresh();
				});
			},

			// 跳转订单
			goOrder(e) {
				let token = uni.getStorageSync('token')
				if (token) {
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
			this.page = this.page + 1;
			this.getDataList();
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.getDataList();
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
</style>
