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
			<view class="margin-lr-sm margin-top-16 padding-sm bg radius" v-for="(item,index) in goods" :key='index'
				@click="bindtakeDetail(item.ordersId)">
				<view class="flex justify-between">
					<view class="text-blue">{{item.statusName}}</view>
					<view class="text-white">{{item.createTime}}</view>
				</view>
				<view class=" u-flex u-p-t-30">
					<view class="u-m-r-10">
						<!-- <u-avatar :src="item.homepageImg" mode="square" size="100"></u-avatar> -->
						<!-- <u-avatar :src="order.user.avatar" size="68"></u-avatar> -->
						<image :src="item.homepageImg" style="width: 140rpx;height: 140rpx;"></image>
					</view>
					<view class="u-flex-1 text-white margin-left-xs">
						<view class="text-30  text-bold u-line-1" style="width: 500rpx;">{{item.myLevel}}</view>
						<!-- <view class="u-font-16  text-bold">标题</view> -->

						<view class="u-font-14 margin-top-xs u-tips-color flex justify-between">
							<view class="text-white">
								<view style="display: inline-flex;">
									<view class="margin-top-xs text-white text-df"
										v-for="(item,index) in item.gameName " :key="index"
										style="margin-right: 10rpx;">{{item}}</view>
								</view>
							</view>
						</view>
						<view class="text-white text-right margin-top-sm">
							实收: <text class="text-lg">{{item.oldMoney*item.orderNumber}}元</text>
						</view>
					</view>
				</view>
				<view class="margin-top-sm" v-if="item.remarks">
					备注：<text class="text-red">{{item.remarks}}</text>
				</view>
				<view class="flex justify-end u-p-t-20">
					<u-button v-if="item.state == 1" :custom-style="customStyle" shape="circle" :plain="true"
						@click="cancelOrder(item,3)">拒接接单</u-button>
					<!-- <u-button v-if="item.state == 3" :custom-style="customStyle" shape="circle" :plain="true" @click="delOrder(item)">拒接接单</u-button> -->
					<u-button v-if="item.state == 1" :custom-style="customStyle1" shape="circle" :plain="true"
						@click="cancelOrder(item,2)">完成接单</u-button>
					<u-button :custom-style="customStyle" shape="circle" :plain="true" @click="clickItem(item)">联系TA
					</u-button>
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
	import empty from '../../components/empty.vue'

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
				tabs: [{
						title: '全部',
						status: ''
					}, {
						title: '进行中',
						status: '1'
					}, {
						title: '已完成',
						status: '2'
					},
					{
						title: '已拒绝',
						status: '3'
					}
				],
				tabIndex: 0, // tab下标
				game: [],
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
			}
		},
		onLoad() {
			this.$queue.showLoading("加载中...");
			this.userId = uni.getStorageSync('userId')
			this.nickName = uni.getStorageSync('nickName')
		},
		onShow() {
			this.downCallback()
			this.upCallback(this.page)
		},
		methods: {
			// 接单详情
			bindtakeDetail(e) {
				console.log(e)
				let id = e
				uni.navigateTo({
					url: './takeDetail?id=' + id
				})
			},
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
				this.$Request.get('/app/orders/selectMyTakeOrders', data).then(res => {
					uni.hideLoading();
					this.mescroll.endBySize(res.data.list.length, res.data.totalCount)
					if (page.num == 1) this.goods = []; //如果是第一页需手动制空列表
					res.data.list.forEach(ret => {
						switch (ret.state) {
							case 0:
								ret.statusName = '待接单'
								break;
							case 1:
								ret.statusName = '进行中'
								break;
							case 2:
								ret.statusName = '已完成'
								break;
							case 3:
								ret.statusName = '已拒绝'
								break;
						}
					})
					this.goods = [...this.goods, ...res.data.list]; //追加新数据
					for (let i = 0; i < this.goods.length; i++) {
						this.goods[i].gameName = this.goods[i].gameName.split(",");
					}
					this.mescroll.endSuccess(res.data.total.length); // 隐藏加载状态栏

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

									that.mescroll.resetUpScroll()
								}
							})
						}
					},
				})

			},
			//
			clickItem: function(options) {
				let data = {
					userId: this.userId,
					focusedUserId: options.ordersUserId
				}
				this.$Request.postJson('/app/chat/insertChatConversation ', data).then(res => {
					if (res.code == 0) {
						let id = this.userId == res.data.userId ? res.data.focusedUserId : this.userId
						uni.navigateTo({
							url: '/pages/msg/im?chatConversationId=' + res.data.chatConversationId +
								'&byUserId=' + id
						})
					}
				})
			},
			goNav(e) {
				uni.navigateTo({
					url: e
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
