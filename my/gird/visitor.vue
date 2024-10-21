<template>
	<view class="content">
		<view v-if="dataList.length != 0" class="bg u-flex u-p-l-30 u-p-t-30 u-p-b-10 u-p-r-30"
			v-for="(item,index) in dataList" :key='index' @longpress="delData(item)">
			<view class="u-m-r-10">
				<u-avatar :src="item.avatar?item.avatar: '../../static/logo.png'" size="100"></u-avatar>
			</view>
			<view class="u-flex-1 text-white margin-left-xs">
				<view class="u-font-16  text-bold">{{item.userName}}</view>
				<view class="u-font-14 margin-top-sm u-tips-color" @click="goNav('/pages/me/vip/index')">
					{{item.updateTime}}访问了你
				</view>
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
				limit: 10
			}
		},
		onLoad(e) {
			// uni.setNavigationBarTitle({
			// 	title: e.name
			// })
			this.$queue.showLoading("加载中...");
			this.getVisitorList()

		},
		methods: {
			// 访客
			getVisitorList() {
				let data = {
					page: this.page,
					limit: this.limit
				}
				this.$Request.get("/app/userBrowse/myVisitor", data).then(res => {
					uni.hideLoading();
					if (res.code == 0) {
						if (this.page == 1) {
							this.dataList = res.data.list
						} else {
							this.dataList = [...this.dataList, ...res.data.list]
						}
					} else {
						console.log(res.msg)
					}
					uni.stopPullDownRefresh();
				})
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
							that.$Request.post("/app/userBrowse/deleteMyVisitor", data).then(res => {
								if (res.code == 0) {
									uni.showToast({
										title: '删除成功！',
										icon: 'none'
									})
									that.getVisitorList()
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
			this.getVisitorList()
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.getVisitorList()
		},
	}
</script>

<style>
	page {
		background-color: #F7F7F7;
	}

	.bg {
		background-color: #FFFFFF;
	}
</style>
