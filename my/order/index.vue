<!-- 菜单悬浮的原理: 通过给菜单添加position:sticky实现, 用法超简单, 仅APP端的低端机不兼容 https://caniuse.com/#feat=css-sticky -->
<template>
	<view>
		<!-- 对于mescroll-body: 需设置:sticky="true", 此应避免在mescroll-body标签前面加其他非定位的元素, 否则下拉区域会被挤出, 无法会隐藏.-->
		<!-- 对于mescroll-uni: 则无需设置:sticky="true", 无其他限制和要求 -->

		<!-- sticky吸顶悬浮的菜单, 父元素必须是 mescroll -->
		<view class="sticky-tabs">
			<me-tabs v-model="tabIndex" nameKey='title' :tabs="tabs" @change="tabChange"></me-tabs>
		</view>
		<mescroll-body :sticky="true" ref="mescrollRef" @init="mescrollInit" @down="downCallback" @up="upCallback">
			<!-- 数据列表 -->
			<!-- <view v-if="goods.length > 0" class="margin-sm padding-sm bg radius" v-for="(item,index) in goods"
				:key='index' @click="clickItem(item)"> -->
			<view v-if="goods.length > 0" class="margin-lr-sm margin-top-16 padding-sm bg radius" v-for="(item,index) in goods"
				:key='index' @click="goNav('/my/order/pay?id='+item.ordersId+'&isTrue=1')">
				<view class="flex justify-between">
					<view class="text-blue" v-if="item.state ==0">待付款</view>
					<view class="text-blue" v-if="item.state ==1">进行中</view>
					<view class="text-blue" v-if="item.state ==2">已完成</view>
					<view class="text-blue" v-if="item.state ==3" style="color: #999999;">已取消</view>
					<view style="color: #1E1F31;">{{item.updateTime}}</view>
				</view>
				<view class=" u-flex u-p-t-30">
					<view class="u-m-r-10">
						<u-avatar :src="item.homepageImg?item.homepageImg: '../../static/logo.png'" mode="square" size="100">
						</u-avatar>
					</view>
					<view class="u-flex-1 text-white margin-left-xs">
						<view class="text-30  text-bold u-line-1" style="width: 560rpx;">{{item.myLevel}}</view>
						<view class="u-font-14 margin-top-xs u-tips-color flex justify-between">
							服务地址：{{item.city}}{{item.district}}{{item.detailsAddress}}
						</view>
					</view>
				</view>
				<view class="flex u-p-t-20 justify-between align-center">
					<view class="text-white flex-sub ">
						实付：<text class="text-df">￥</text><text class="text-xl text-bold">{{item.payMoney}}</text>
					</view>
					<view class="flex text-right">
						<u-button v-if="item.state == 0" :custom-style="customStyle" shape="circle" :plain="true"
							@click="cancelOrder(item)">取消订单</u-button>
						<u-button v-if="item.state == 0" :custom-style="customStyle1" shape="circle" :plain="true"
							@click="goNav('/my/order/pay?id='+item.ordersId+'&isTrue=0')">去支付</u-button>
						<u-button v-if="item.state == 1" :custom-style="customStyle1" shape="circle" :plain="true"
							@click="goNav('/my/order/pay?id='+item.ordersId+'&isTrue=1')">查看详情</u-button>
						<u-button v-if="item.state == 1" :custom-style="customStyle" shape="circle" :plain="true"
							@click="cancel(item)">订单完成</u-button>
						<!-- <u-button v-if="item.state == 2" :custom-style="customStyle" shape="circle" :plain="true" @click="goNav('/my/order/complain?id='+item.ordersId)" >去投诉</u-button> -->
						<u-button v-if="item.state == 2 && item.commentCount == 0" :custom-style="customStyle1"
							shape="circle" :plain="true"
							@click="goNav('/my/order/feedback?id='+item.orderTakingId+ '&ordersId='+item.ordersId)">去评价
						</u-button>
						<u-button v-if="item.state == 3" :custom-style="customStyle" shape="circle" :plain="true"
							@click="delOrder(item)">删除订单</u-button>
					</view>
				</view>
			</view>
			<empty v-if="goods.length == 0"></empty>
		</mescroll-body>
	</view>
</template>

<script>
	import MescrollMixin from "@/components/mescroll-uni/components/mescroll-uni/mescroll-mixins.js";
	import mescrollBody from "@/components/mescroll-uni/components/mescroll-body/mescroll-body.vue";
	import meTabs from "@/components/mescroll-uni/me-tabs/me-tabs.vue";
	import empty from '@/components/empty.vue'

	export default {
		mixins: [MescrollMixin], // 使用mixin
		components: {
			mescrollBody,
			meTabs,
			empty
		},
		data() {
			return {
				goods: [], // 数据列表
				game: [],
				tabs: [{
					title: '全部',
					status: ''
				}, {
					title: '待付款',
					status: '0'
				}, {
					title: '进行中',
					status: '1'
				}, {
					title: '已完成',
					status: '2'
				}, {
					title: '已取消',
					status: '3'
				}],
				tabIndex: 0, // tab下标

				page: 1,
				limit: 10,
				userId: 0,
				status: 1,
				nickName: '',
				avatar: '',
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
				// customStyle: {
				// 	color: '#1789FD',
				// 	backgroundColor: '#1E1F31',
				// 	border: "8rpx",
				// 	width: '180rpx',
				// 	height: '54rpx',
				// 	margin: "0 0 0 20rpx"
				// }
			}
		},
		onLoad() {
			// this.$queue.showLoading("加载中...");
			this.userId = uni.getStorageSync('userId')
			this.nickName = uni.getStorageSync('nickName')
		},
		onShow() {
			this.mescroll.resetUpScroll()
		},
		methods: {
			/*下拉刷新的回调 */
			downCallback() {
				// 这里加载你想下拉刷新的数据, 比如刷新轮播数据
				// loadSwiper();
				// 下拉刷新的回调,默认重置上拉加载列表为第一页 (自动执行 page.num=1, 再触发upCallback方法 )
				this.mescroll.resetUpScroll()
			},
			/*上拉加载的回调: 其中page.num:当前页 从1开始, page.size:每页数据条数,默认10 */
			upCallback(page) {
				let curTab = this.tabs[this.tabIndex].status

				let data = {
					status: curTab,
					page: page.num,
					limit: page.size
				}
				this.$Request.get('/app/orders/selectMyOrder', data).then(res => {
					this.mescroll.endBySize(res.data.list.length, res.data.totalCount)
					if (page.num == 1) this.goods = []; //如果是第一页需手动制空列表
					this.goods = [...this.goods, ...res.data.list]; //追加新数据
					for (var index in this.goods) {
						let gameNameList = this.goods[index].gameName.split(",");
						this.goods[index].gameName = gameNameList;
						this.game = this.goods[index].gameName
					}
					this.goods.forEach(ret => {
						switch (ret.state) {
							case '0':
								ret.statusName = '待付款'
								break;
							case '1':
								ret.statusName = '进行中'
								break;
							case '2':
								ret.statusName = '已完成'
								break;
							case '3':
								ret.statusName = '已取消'
								break;
						}
					})
					this.mescroll.endSuccess(res.data.list.length); // 隐藏加载状态栏
					// uni.hideLoading();
				}).catch(() => {
					//联网失败, 结束加载
					this.mescroll.endErr();
				});
			},
			// 切换菜单
			tabChange() {
				this.goods = []; // 置空列表,显示加载进度条
				this.mescroll.resetUpScroll()
			},
			// 取消订单
			cancelOrder(e) {
				let that = this
				uni.showModal({
					title: '提示',
					content: '确认取消订单吗?',
					success: function(res) {
						if (res.confirm) {
							let data = {
								id: e.ordersId,
								status: '3'
							}
							that.$Request.get('/app/orders/cancelOrder', data).then(res => {
								if (res.code == 0) {
									that.mescroll.resetUpScroll()
								}
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			// 完成订单
			cancel(e) {
				let that = this
				uni.showModal({
					title: '提示',
					content: '订单完成后款项将支付给服务方，确认完成订单吗?',
					success: function(res) {
						if (res.confirm) {
							let data = {
								id: e.ordersId,
								status: '2'
							}
							that.$Request.get('/app/orders/cancelOrder', data).then(res => {
								if (res.code == 0) {
									that.mescroll.resetUpScroll()
								}
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			//删除
			delOrder(e) {
				let that = this
				uni.showModal({
					title: '提示',
					content: '确定删除订单吗?',
					success: function(res) {
						if (res.confirm) {
							let data = {
								id: e.ordersId,
							}
							that.$Request.get('/app/orders/deleteOrder', data).then(res => {
								if (res.code == 0) {
									uni.showToast({
										title: "删除成功"
									})
									that.mescroll.resetUpScroll()
								}
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			// clickItem: function(e) {
			// 	console.log('点击', e)
			// 	// uni.navigateTo({
			// 	// 	url: '/pages/index/game/order?id=' + e.orderTakingId + '&userId=' + e.userId
			// 	// });
			// 	uni.navigateTo({
			// 		url: '../../my/order/pay?id=' + e.ordersId + '&isTrue=1'
			// 	})
			// },
			goNav(url) {
				uni.navigateTo({
					url
				})
			}
		}
	}
</script>

<style lang="scss">
	/*
	sticky生效条件：
	1、父元素不能overflow:hidden或者overflow:auto属性。(mescroll-body设置:sticky="true"即可, mescroll-uni本身没有设置overflow)
	2、必须指定top、bottom、left、right4个值之一，否则只会处于相对定位
	3、父元素的高度不能低于sticky元素的高度
	4、sticky元素仅在其父元素内生效,所以父元素必须是 mescroll
	*/
	.sticky-tabs {
		z-index: 990;
		position: sticky;
		top: var(--window-top);
		// background-color: #fff;
	}

	// 使用mescroll-uni,则top为0
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

	page {
		background-color: #F7F7F7;
	}

	.bg {
		background-color: #FFFFFF;
	}

	// .u-size-default {
	// 	// background: #557EFD !important;
	// 	border:1rpx solid #557EFD !important;
	// 	color: #557EFD !important;
	// 	background-color: #FFFFFF !important;
	// }
</style>
