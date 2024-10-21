<template>
	<view class="">
		<mescroll-body :sticky="true" ref="mescrollRef" @init="mescrollInit" @down="downCallback" @up="upCallback">
			<view class="padding-lr padding-top-sm bg flex " @click.stop="goSearch">
				<view class="flex justify-between margin-right-sm" @tap.stop="goSelectCity" style="line-height: 68rpx;">
					<image src="../../static/images/index/place.png" style="width: 27rpx;height: 37rpx;"
						class="margin-top-xs"></image>
					<view class="localName text-white margin-left-sm">{{city}}</view>
				</view>
				<u-search class="flex-sub" placeholder="搜索你需要的服务" shape="square" disabled :show-action="false"
					:animation="true" bg-color="#F7F7F7" color="#1A1A1A"></u-search>
			</view>
			<view class="sticky-tabs u-skeleton-rect">
				<me-tabs v-model="tabIndex" nameKey='gameName' :tabs="tabData" @change="tabChange"></me-tabs>
			</view>

			<view v-if="tabIndex == 0" class="" style="background-color: #F7F7F7;">
				<view class="padding-lr-sm ">
					<swiper class="screen-swiper" style="height: 260rpx;" :circular="true" :autoplay="true"
						interval="2500" duration="800">
						<swiper-item v-for="(item,index) in swiperList" :key="index">
							<image :src="item.imageUrl" mode="aspectFit" class="radius"></image>
						</swiper-item>
					</swiper>
				</view>
				<view class="" style="color: #333333;margin-bottom: 15rpx;">
					<u-grid :col="5" :border="false">
						<u-grid-item bg-color="#F7F7F7" v-for="(item,index) in gridData" :key='index'
							@click="goNav(item.url)">
							<image :src="item.imageUrl" style="width: 92rpx;height: 92rpx;border-radius: 92rpx;">
							</image>
							<view class="grid-text">{{item.name}}</view>
						</u-grid-item>
					</u-grid>
				</view>

				<view style="width: 95%;margin: 0 auto;" v-if="renzheng && XCXIsSelect != '否'">
					<view style="width: 100%;height: 130rpx;" @click="bindRz">
						<image src="../../static/images/ruzhu.png" style="width: 100%;height: 100%;"></image>
					</view>
				</view>

				<!-- 乐享低价 -->
				<view class="bg margin-lr-sm padding-sm radius " v-if="lowTaking.length">
					<view class="flex justify-between text-white padding-bottom-sm ">
						<view style="font-weight: 600;">优惠专区</view>
						<view @tap="goLowTaking()">
							更多
							<image class="margin-left-sm" style="width: 14rpx;height: 24rpx;display: inline-block;"
								src="../../static/images/index/right.png"></image>
						</view>
					</view>
					<view class="flex justify-between" style="overflow: hidden;">
						<view v-for="(item,index) in lowTaking" :key='index' @click="goOrder(item)"
							style="width: 200rpx;">
							<view class="u-relative">
								<image style="width: 200rpx;height: 200rpx;border-radius: 10rpx;"
									:src="item.homepageImg?item.homepageImg: '../../static/logo.png'">
								</image>
							</view>
							<view style="display: inline-flex;width: 100%;">
								<!-- <view class="margin-top-xs text-white text-df" v-for="item in game ">{{item}}</view> -->
								<view v-if="item.authentication == 2||item.authentication == 3">
									<image src="../../static/images/qi.png"
										style="width: 28rpx;height: 28rpx;margin-top: 8rpx;"></image>

								</view>
								<view class="" v-if="item.authentication == 1||item.authentication == 3">
									<image src="../../static/images/geren.png"
										style="width: 28rpx;height: 28rpx;margin-left: 10rpx;margin-top: 8rpx;"></image>
								</view>
								<view class="text-white text-30 u-line-1 " style="margin-left: 10rpx;">{{item.myLevel}}
								</view>
							</view>
							<view class="margin-top-xs">
								<view style="color:#FF1200">
									￥{{isVip? item.memberMoney :item.money}}元/{{item.unit}}
								</view>
							</view>
						</view>
					</view>
				</view>
			</view>


			<view style="background-color: #F5F5F5;padding-top: 1rpx;" v-if="orderList.length">
				<view class="text-center padding-tb-sm text-lg text-bold" v-if="tab ==0">推荐服务</view>
				<ren-dropdown-filter :filterData='filterData' :border="false" :defaultIndex='defaultIndex'
					@onSelected='change' class="u-skeleton-rect">
				</ren-dropdown-filter>
				<view class="flex justify-between margin-top-xs bg padding-sm radius  " v-for="(item,index) in orderList"
					:key='index' @click="goOrder(item)">
					<image :src="item.homepageImg?item.homepageImg: '../../static/logo.png'"
						style="width: 200rpx;height: 200rpx;border-radius: 10rpx;"></image>
					<view class="flex-sub margin-left text-white flex flex-direction justify-between">
						<view class="flex justify-between align-center">
							<view class="flex">
								<view v-if="item.authentication == 2||item.authentication == 3">
									<image src="../../static/images/qi.png"
										style="width: 28rpx;height: 28rpx;margin-top: 10rpx;"></image>
								</view>
								<view class="" v-if="item.authentication == 1||item.authentication == 3">
									<image src="../../static/images/geren.png"
										style="width: 28rpx;height: 28rpx;margin-left: 10rpx;margin-top: 10rpx;">
									</image>
								</view>
								<view class="margin-right-xs text-30"
									style="display: inline-block;width: 260rpx;margin-left: 10rpx; overflow: hidden;white-space: nowrap;text-overflow: ellipsis;">
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
			<empty v-if="orderList.length == 0"></empty>
		</mescroll-body>
		<u-skeleton :loading="loading" :animation="true" elColor='#FFFFFF' bgColor='#FFFFFF'></u-skeleton>
	</view>
</template>

<script>
	import MescrollMixin from "@/components/mescroll-uni/components/mescroll-uni/mescroll-mixins.js";
	import mescrollBody from "@/components/mescroll-uni/components/mescroll-body/mescroll-body.vue";
	import meTabs from "@/components/mescroll-uni/me-tabs/me-tabs.vue";
	import empty from '@/components/empty.vue'

	import RenDropdownFilter from '@/components/ren-dropdown-filter/ren-dropdown-filter.vue'

	export default {
		mixins: [MescrollMixin], // 使用mixin
		components: {
			mescrollBody,
			meTabs,
			empty,
			RenDropdownFilter
		},
		data() {
			return {
				loading: true, // 是否显示骨架屏组件
				defaultSelected: [],
				tabIndex: 0, // tab下标
				tabData: [{
					createTime: "",
					gameName: '推荐',
					gameImg: "",
					id: 0,
					status: 0,
					updateTime: "",
				}],
				swiperList: [],
				gridData: [],
				lowTaking: [],

				value1: '',
				value2: '',
				value3: '',
				game: [],
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

				city: '请选择城市',
				latitude: '',
				longitude: '',
				orderList: [],
				token: '',
				XCXIsSelect: '否',
				isVip: false,
				myId: uni.getStorageSync('userId') ? uni.getStorageSync('userId') : '',
				showModal: true,
				arr: [],
				tab: '',
				geRen: 0,
				Qe: 0,
				renzheng: false
			}
		},
		onLoad(e) {
			let that = this
			that.getClassfly()



			uni.getLocation({
				type: 'gcj02',
				geocode: true, //设置该参数为true可直接获取经纬度及城市信息
				success: function(res) {
					console.log(res, '地理位置')
					that.latitude = res.latitude
					that.longitude = res.longitude
					uni.setStorageSync('latitude', res.latitude)
					uni.setStorageSync('longitude', res.longitude)

					// #ifdef APP-PLUS
					that.city = res.address.city
					uni.setStorageSync('city', res.address.city)
					let data = {
						num: 1,
						size: 10
					}
					that.getData(data)

					// #endif

					// #ifdef H5

					that.selectCity(that.longitude, that.latitude);
					// #endif

					// #ifdef MP-WEIXIN

					uni.request({
						url: 'https://apis.map.qq.com/ws/geocoder/v1/?location=' + that.latitude +
							',' + that
							.longitude + '&key=5DLBZ-QYMR6-334SQ-MOZUI-Z3GVO-SBFB4',
						success(re) {
							if (re.statusCode === 200) {
								let citydata = re.data.result.address_component.city
								console.log("获取城市名称成功", citydata)
								that.city = citydata ? citydata : '未知'
								uni.setStorageSync('city', citydata)
								let data = {
									num: 1,
									size: 10
								}
								that.getData(data)
							} else {
								console.log("获取信息失败，请重试！")
							}
						}
					});
					// #endif
				},
				fail: function() {
					console.log('获取地址失败')
				}
			})

			// 获取邀请码保存到本地
			if (e.invitation) {
				that.$queue.setData('inviterCode', e.invitation);
			}

			if (this.myId) {
				that.$Request.getT('/app/common/type/235').then(res => { //报名成功通知
					if (res.code == 0) {
						if (res.data && res.data.value) {
							that.arr.push(res.data.value)
						}
					}
				})
				that.$Request.getT('/app/common/type/236').then(res => { //活动即将开始提醒
					if (res.code == 0) {
						if (res.data && res.data.value) {
							that.arr.push(res.data.value)
						}
					}
				})
			}
			// this.Qe = uni.getStorageSync("Qe")
			// this.geRen = uni.getStorageSync("geRen")
		},
		
		onShow() {
			let that = this
			that.XCXIsSelect = that.$queue.getData('XCXIsSelect');
			that.Qe = uni.getStorageSync("Qe")
			that.geRen = uni.getStorageSync("geRen")

			if (that.Qe == 2 || that.geRen == 2) {
				this.renzheng = false
			} else {
				this.renzheng = true
			}
			that.city = uni.getStorageSync('city') ? uni.getStorageSync('city') : '请选择城市'
			that.getBannerList()
			that.getGrid()
			that.getLowTaking()
			// that.getRenZheng()

			that.token = uni.getStorageSync('token')
			if (uni.getStorageSync('token')) {
				that.getIsVip()
			}
			that.myId = uni.getStorageSync('userId')
			console.log(that.showModal, '------', that.myId)
			// #ifdef MP-WEIXIN
			//订阅
			if (that.myId) {
				if (this.showModal) {
					// this.openMsg()
				}
			}
			// #endif

		},
		methods: {

			bindRz() {
				// uni.setStorageSync('renzheng',res.data.status)
				let token = uni.getStorageSync('token')
				if (token) {
					uni.navigateTo({
						url: '../../my/renzheng/index?classify=' + 1
					})
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}

			},
			selectCity(longitude, latitude) {
				this.$Request.get('/app/Login/selectCity?lat=' + latitude + '&lng=' + longitude).then(res => {
					if (res.code == 0) {
						this.city = res.data.city ? res.data.city : '未知'
						uni.setStorageSync('city', res.data.city)
						let data = {
							num: 1,
							size: 10
						}
						this.getData(data)
					}
				});
			},
			// 开启订阅消息
			openMsg() {
				console.log('订阅消息')
				var that = this
				uni.getSetting({
					withSubscriptions: true, //是否获取用户订阅消息的订阅状态，默认false不返回
					success(ret) {
						console.log(ret.subscriptionsSetting, '------------------')
						// if (ret.subscriptionsSetting.itemSettings && Object.keys(ret.subscriptionsSetting.itemSettings).length == 2) {
						if (ret.subscriptionsSetting.itemSettings) {
							uni.setStorageSync('sendMsg', true)
							uni.openSetting({ // 打开设置页 
								success(rea) {
									console.log(rea.authSetting)
								}
							});
						} else { // 用户没有点击“总是保持以上，不再询问”则每次都会调起订阅消息
							console.log(99999)
							uni.setStorageSync('sendMsg', false)
							uni.showModal({
								title: '提示',
								content: '为了更好的体验,请绑定消息推送',
								confirmText: '确定',
								cancelText: '取消',
								success: function(res) {
									if (res.confirm) {
										wx.requestSubscribeMessage({
											tmplIds: that.arr,
											success(re) {
												console.log(JSON.stringify(re),
													'++++++++++++++')
												var datas = JSON.stringify(re);
												if (datas.indexOf("accept") != -1) {
													console.log(re)
													// uni.setStorageSync('sendMsg', true)
												}
											},
											fail: (res) => {
												console.log(res)
											}
										})
										// uni.setStorageSync('sendMsg', true)
										console.log('确认')
										that.showModal = false
									} else if (res.cancel) {
										console.log('取消')
										// uni.setStorageSync('sendMsg', false)
										that.showModal = true
									}
								}
							})
						}
					}
				})
			},
			getIsVip() {
				this.$Request.get("/app/UserVip/isUserVip").then(res => {
					if (res.code == 0) {
						this.isVip = res.data
						uni.setStorageSync('isVIP', res.data)
					}
				});
			},
			/*下拉刷新的回调 */
			downCallback() {
				// 这里加载你想下拉刷新的数据, 比如刷新轮播数据
				// loadSwiper();
				// 下拉刷新的回调,默认重置上拉加载列表为第一页 (自动执行 page.num=1, 再触发upCallback方法 )
				// this.$refs.uDropdown.close();
				this.mescroll.resetUpScroll()
			},
			/*上拉加载的回调: 其中page.num:当前页 从1开始, page.size:每页数据条数,默认10 */
			upCallback(page) {
				// console.log(page,'*************')
				this.getData(page)
			},
			getData(page) {
				let curTab = this.tabData[this.tabIndex].gameName
				if (curTab == '推荐') {
					curTab = ''
					this.tab = 0

				} else if (curTab != '推荐') {
					curTab = this.tabData[this.tabIndex].gameName
					this.tab = 1
				}
				let num
				if (this.tabIndex == 0) {
					num = 0
				} else {
					num = 2
				}
				let data = {
					id: curTab,
					page: page.num,
					limit: page.size,
					isRecommend: num,
					condition: this.value1, //智能优选
					salesNum: this.value2, //不限男女
					by: this.value3, //价格
					latitude: this.latitude,
					longitude: this.longitude,
					city: this.city
				}
				// console.log(data)
				if (this.token) {
					this.$Request.get("/app/orderTaking/queryTaking", data).then(res => {
						this.mescroll.endBySize(res.data.list.length, res.data.list)
						if (res.code == 0) {
							if (page.num == 1) {
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
							this.loading = false;
						}
						this.mescroll.endSuccess(res.data.list.length); // 隐藏加载状态栏
					}).catch(() => {
						//联网失败, 结束加载
						this.mescroll.endErr();
					});
				} else {
					this.$Request.get("/app/orderTaking/queryTakings", data).then(res => {
						this.mescroll.endBySize(res.data.list.length, res.data.list)
						this.loading = false;
						if (res.code == 0) {
							if (page.num == 1) {
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
						this.mescroll.endSuccess(res.data.list.length); // 隐藏加载状态栏
					}).catch(() => {
						//联网失败, 结束加载
						this.mescroll.endErr();
					});
				}
				this.getClassfly()
				this.getBannerList()
				this.getGrid()
				this.getLowTaking()

			},
			// 切换菜单
			tabChange() {
				if (uni.getStorageSync('sendMsg')) {
					console.log('授权+1')
					wx.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							console.log(JSON.stringify(re), 111111111111)
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								// console.log(re)
							}
						},
						fail: (res) => {
							// console.log(res)
						}
					})
				}
				this.defaultIndex = [0, 0, 0]
				// this.$refs.uDropdown.close();
				// this.orderList = []; // 置空列表,显示加载进度条
				this.mescroll.resetUpScroll()
			},
			// 获取游戏类型
			getClassfly() {
				this.$Request.get("/app/appGame/queryGameName").then(res => {
					if (res.code == 0) {
						this.tabData = [{
							createTime: "",
							gameName: '推荐',
							gameImg: "",
							id: 0,
							status: 0,
							updateTime: "",
						}]
						this.tabData = [...this.tabData, ...res.data]

					}
				});
			},
			//获取轮播图
			getBannerList() {
				this.$Request.get("/app/banner/selectBannerList", {
					classify: 1
				}).then(res => {
					if (res.code == 0) {
						this.swiperList = res.data
					}
				});
			},
			// 获取金刚区分类
			getGrid() {
				this.$Request.get("/app/banner/selectBannerList", {
					classify: 2
				}).then(res => {
					if (res.code == 0) {
						this.gridData = res.data
						// console.log(this.gridData, '；；；；；；')
					}
				});
			},
			// 乐享低价
			getLowTaking() {
				this.$Request.get("/app/orderTaking/queryLowTaking", {
					city: this.city
				}).then(res => {
					if (res.code == 0) {
						this.lowTaking = res.data
						// console.log(this.lowTaking, '0000000000000')
					}
				});
			},
			// 筛选
			change(e) {

				this.value1 = e[0][0].value
				this.value2 = e[1][0].value
				this.value3 = e[2][0].value

				this.mescroll.resetUpScroll()
			},
			// 选择城市
			goSelectCity() {
				uni.navigateTo({
					url: '/pages/index/citys/citys'
				});
			},
			// 乐享低价
			goLowTaking() {
				uni.navigateTo({
					url: '/pages/index/game/lowTaking'
				});
			},
			// 跳转游戏列表
			goNav(url) {
				console.log(url, '1111112333')
				if (uni.getStorageSync('sendMsg')) {
					console.log('授权+1')
					wx.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							console.log(JSON.stringify(re), 111111111111)
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
				if (url.indexOf('/pages/') !== -1) {
					uni.navigateTo({
						url
					});
				} else {
					//#ifndef H5
					uni.navigateTo({
						url: '/pages/index/webView?url=' + url
					});
					//#endif
					//#ifdef H5
					window.location.href = url;
					//#endif
				}
			},
			// 跳转搜索
			goSearch() {
				if (uni.getStorageSync('sendMsg')) {
					console.log('授权+1')
					wx.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							// console.log(JSON.stringify(re), 111111111111)
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
				uni.navigateTo({
					url: '/pages/index/search/index'
				});
			},
			// 跳转订单
			goOrder(e) {
				console.log('授权', uni.getStorageSync('sendMsg'))
				if (uni.getStorageSync('sendMsg')) {
					console.log('授权+1')
					wx.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							console.log(JSON.stringify(re), 111111111111)
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
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
		}
	}
</script>

<style lang="scss">
	page {
		background-color: #FFFFFF;
	}

	.bg {
		background: #FFFFFF;
	}

	.sticky-tabs {
		z-index: 990;
		position: sticky;
		top: var(--window-top);
		// background-color: #fff;
	}

	/* // 使用mescroll-uni,则top为0 */
	.mescroll-uni,
	/deep/.mescroll-uni {
		.sticky-tabs {
			top: 0;
		}
	}

	.demo-tip {
		padding: 18upx;
		font-size: 24upx;
		text-align: center;
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
