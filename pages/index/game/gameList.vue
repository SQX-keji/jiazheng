<template>
	<view>
		<!-- <u-dropdown height="80" active-color="#2B7DE2" inactive-color='#fff' >
			<u-dropdown-item v-model="value1" title="智能优选"  @change="change" :options="options1"></u-dropdown-item>
			<u-dropdown-item v-model="value2" title="不限性别" @change="change" :options="options2"></u-dropdown-item>
			<u-dropdown-item v-model="value3" title="价格" @change="change" :options="options3"></u-dropdown-item>
		</u-dropdown> -->
		<!-- #ifdef MP-WEIXIN -->
		<view style="position: fixed;top: 0;left: 0;right: 0;z-index: 9;">
			<!-- #endif -->
			<!-- #ifndef MP-WEIXIN -->
			<view>
				<!-- #endif -->
				<ren-dropdown-filter :filterData='filterData' :border="false" :defaultIndex='defaultIndex'
					@onSelected='change' class="u-skeleton-rect">
				</ren-dropdown-filter>
			</view>

			<!-- #ifdef MP-WEIXIN -->
			<view class="margin-lr-sm" v-if="orderList.length" style="margin-top: 100upx;">
				<!-- #endif -->
				<!-- #ifndef MP-WEIXIN -->
				<view class="margin-lr-sm" v-if="orderList.length">
					<!-- #endif -->
					<view class="flex justify-between margin-top-16 bg padding-sm radius"
						v-for="(item,index) in orderList" :key='item.id' @click="goOrder(item)">
						<image :src="item.homepageImg?item.homepageImg: '../../../static/logo.png'"
							style="width: 200rpx;height: 200rpx;border-radius: 10rpx;z-index: 1;">
						</image>
						<view class="flex-sub margin-left text-white flex flex-direction justify-between">
							<view class="flex justify-between">
								<view class="flex">
									<view v-if="item.authentication == 2||item.authentication == 3">
										<image src="../../../static/images/qi.png" style="width: 28rpx;height: 28rpx;">
										</image>

									</view>
									<view class="" v-if="item.authentication == 1||item.authentication == 3">
										<image src="../../../static/images/geren.png"
											style="width: 28rpx;height: 28rpx;margin-left: 10rpx;"></image>
									</view>
									<view class="margin-right-xs u-line-1"
										style="margin-top: -2px;display: inline-block;margin-left: 10rpx;width: 260rpx;">
										{{item.myLevel}}
									</view>
								</view>
								<view>
									{{item.distance/1000}}km
								</view>
							</view>
							<view class="flex radius" style="line-height: 34upx;">
								<view style="width: 100%;position: relative;line-height: 40rpx;color: #999999;">
									<text class="margin-right-xs" v-for="(item,index) in item.gameName"
										:key="index">{{item}}</text>
								</view>
							</view>
							<view class="flex" style="align-items: center;font-size: 24rpx;padding: 5rpx;"
								v-if="item.count">
								<view style="color: #999999;background: #F2F2F2; padding: 5rpx 10rpx;">
									已服务{{item.count}}人
								</view>
							</view>
							<view style="width: 100%;display: flex;justify-content: space-between;align-items: center;">
								<view style="color:#FF1200;font-size: 31rpx;">
									￥{{isVip? item.memberMoney :item.money}}/<text>{{item.unit}}</text>
								</view>
								<view
									style="background: #557EFD;color: #ffffff;padding: 15rpx 25rpx;border-radius: 45rpx;">
									预约服务
								</view>
							</view>
						</view>
					</view>
				</view>
				<empty v-if="orderList.length == 0"></empty>
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
				gameId: '',
				gameName: '',
				value1: '',
				value2: '',
				value3: '',

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

				options1: [{
						label: '智能排序',
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
				options2: [{
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
				options3: [{
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
				city: null,
				sex: null,
				order: null,
				latitude: null,
				longitude: null,
				orderList: [],
				token: '',
				isVip: false,
				myId: ''
			}
		},
		onLoad(option) {
			uni.setNavigationBarTitle({
				title: option.name
			})
			this.gameId = option.gameId
			this.gameName = option.name;
			// this.city = uni.getStorageSync('city')
			// this.latitude = uni.getStorageSync('latitude')
			// this.longitude = uni.getStorageSync('longitude')
			this.token = uni.getStorageSync('token')
			this.isVip = uni.getStorageSync('isVIP') ? uni.getStorageSync('isVIP') : false
			this.myId = uni.getStorageSync('userId')
			this.getDataList()
		},
		methods: {
			// 筛选
			change(e) {
				console.log(e[0][0], '+++++++++')
				this.page = 1
				// if( e[0][0].value == 1){
				// 	this.value1 = uni.getStorageSync('city')
				// }else {
				// 	this.value1 =''
				// }

				this.value1 = e[0][0].value
				this.value2 = e[1][0].value
				this.value3 = e[2][0].value


				this.getDataList()
			},
			getDataList() {

				let data = {
					id: this.gameName,
					page: this.page,
					limit: this.limit,
					condition: this.value1, //智能优选
					// city: this.value1, //智能优选
					salesNum: this.value2, //不限销量
					by: this.value3, //价格
					latitude: uni.getStorageSync('latitude'),
					longitude: uni.getStorageSync('longitude'),
					city: uni.getStorageSync('city')
				}
				// console.log(data)
				if (this.token) {
					this.$Request.get("/app/orderTaking/queryTaking", data).then(res => {
						if (res.code == 0) {
							if (this.page == 1) {
								this.orderList = res.data.list
								for (let i = 0; i < this.orderList.length; i++) {
									this.orderList[i].gameName = this.orderList[i].gameName.split(",");
								}
							} else {
								this.orderList = [...this.orderList, ...res.data.list]
								for (let i = 0; i < this.orderList.length; i++) {
									this.orderList[i].gameName = this.orderList[i].gameName.split(",");
								}
							}
						}
						uni.stopPullDownRefresh();
					})
				} else {
					this.$Request.get("/app/orderTaking/queryTakings", data).then(res => {
						if (res.code == 0) {
							if (this.page == 1) {
								this.orderList = res.data.list
								for (let i = 0; i < this.orderList.length; i++) {
									this.orderList[i].gameName = this.orderList[i].gameName.split(",");
								}
							} else {
								this.orderList = [...this.orderList, ...res.data.list]
								for (let i = 0; i < this.orderList.length; i++) {
									this.orderList[i].gameName = this.orderList[i].gameName.split(",");
								}
							}
						}
						uni.stopPullDownRefresh();
					})
				}
			},
			// change(value) {
			// 	this.getDataList()
			// },
			// 跳转订单
			goOrder(e) {
				if (this.token) {
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
		background-color: #F5F5F5;
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
