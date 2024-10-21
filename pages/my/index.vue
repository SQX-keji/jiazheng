<template>
	<view class="text-white bg">
		<view class=" u-flex u-p-l-30 u-p-t-30 u-p-b-30">
			<view class="u-m-r-10">
				<image :src="avatar" style="width: 100rpx;height: 100rpx;border-radius: 100rpx;"
					@click="goNav('/pages/my/userinfo')"></image>
				<view class="lovip" v-if="isVip == true&& XCXIsSelect != '否'">
					<image src="../../static/images/my/vip.png" style="width: 100%;height: 100%;"></image>
				</view>
			</view>
			<view class="u-flex-1 u-m-l-10 text-white" v-if="!isLogin">
				<view class="u-font-18  text-bold">
					<view class="margin-right-sm" v-if="XCXIsSelect != '否'">
						<view class="margin-left-sm ">{{userName}}</view>
						<view class="margin-top-xs">
							<view v-if="geRen == 0&&Qe == 0" class="round margin-left-xs u-font-12"
								@click.stop="goNav('/my/renzheng/index?classify='+1)"
								style="display: inline-block;background-color: #F2F2F2; color: #666666;padding: 2upx 16upx;font-weight: 400;">
								未入驻
							</view>
							<view v-else-if="geRen == 1||Qe == 1" class="round margin-left-xs u-font-12"
								@click.stop="goNav('/my/renzheng/index?classify='+1)"
								style="display: inline-block;background-color: #F2F2F2; color: #666666;padding: 2upx 16upx;font-weight: 400;">
								审核中
							</view>
							<view v-else-if="geRen == 2||Qe == 2" class="round margin-left-xs u-font-12"
								style="display: inline-block;background-color: #F2F2F2; color: #666666;padding: 2upx 16upx;font-weight: 400;">
								已入驻
							</view>
							<view v-else-if="geRen == 3||Qe == 3" class="round margin-left-xs u-font-12"
								@click.stop="goNav('/my/renzheng/index?classify='+1)"
								style="display: inline-block;background-color: #F2F2F2; color: #666666;padding: 2upx 16upx;font-weight: 400;">
								已拒绝
							</view>
						</view>
					</view>
				</view>

				<!-- 	<view class="u-font-14 u-tips-color" style="color: #000000;font-size: 24rpx;margin-top: 5rpx;"
					@click="goNav('/pages/me/vip/index')">邀请码:{{invitationCode}}</view> -->
			</view>
			<view v-else class="text-xl u-p-l-20 text-bold" @click="goLogin('/pages/public/login')">
				登录
			</view>
			<!-- <view v-if="!isLogin" class="" @click="goNav('/pages/my/invitationUser')">
				<image src="../../static/images/my/yaoqing.png" style="width: 163rpx;height: 58rpx;" mode=""></image>
			</view> -->
		</view>
		<!-- <view class="flex justify-around padding-tb">
			<view style="text-align: center;" @click="goNav('/my/gird/guanzhu?name=我的粉丝&type=1')">
				<view class="text-xxl">{{fans}}</view>
				粉丝
			</view>
			<view style="text-align: center;" @click="goNav('/my/gird/guanzhu?name=我的关注&type=2')">
				<view class="text-xxl">{{follow}}</view>
				关注
			</view>
			<view style="text-align: center;" @click="goNav('/my/gird/visitor')">
				<view class="text-xxl">{{visitor}}</view>
				访客
			</view>
			<view style="text-align: center;" @click="goNav('/my/gird/browse')">
				<view class="text-xxl">{{browse}}</view>
				足迹
			</view>
		</view> -->
		<view class=" padding-lr" style="position: relative;" v-if="XCXIsSelect != '否'">
			<image src="../../static/images/my/bg.png" style="width: 100%;height: 110rpx;" mode=""></image>
			<view class="flex justify-between  margin-lr padding-tb-sm radius"
				style="position: absolute;top: 0;width: 640rpx;">
				<image src="@/static/images/my/huiyuan.png" style="width: 70rpx;height: 70rpx;"></image>
				<view class="flex-sub margin-left text-lg" style="line-height: 74rpx;color: #604320;">
					开通享受贵族福利
				</view>
				<view v-if="!isVip" class="btn-bg" style="color: #604320;" @click="goNav('/my/vip/index')">去开通</view>
				<view v-if="isVip" class="btn-bg" style="color: #604320;" @click="goNav('/my/vip/index')">已开通</view>
			</view>
		</view>
		<view>
			<!-- 我的钱包 -->
			<view class="flex padding-lr padding-tb dfs" @click="goNav(list[0].url)" v-if="XCXIsSelect != '否'">
				<image :src="list[0].image" style="width: 50rpx;height: 50rpx;" mode=""></image>
				<view class="flex-sub margin-left text-df" style="line-height: 50upx;">{{list[0].title}}</view>
				<image src="../../static/images/my/right.png" class="images" mode=""></image>
			</view>
			<!-- 我的订单 -->
			<view class="flex padding-lr padding-tb dfs" @click="goNav(list[1].url)">
				<image :src="list[1].image" style="width: 50rpx;height: 50rpx;" mode=""></image>
				<view class="flex-sub margin-left text-df" style="line-height: 50upx;">{{list[1].title}}</view>
				<image src="../../static/images/my/right.png" class="images" mode=""></image>
			</view>

			<!-- 我的足迹 -->
			<view class="flex padding-lr padding-tb dfs" @click="goNav(list[2].url)">
				<image :src="list[2].image" style="width: 50rpx;height: 50rpx;" mode=""></image>
				<view class="flex-sub margin-left text-df" style="line-height: 50upx;">{{list[2].title}}</view>
				<image src="../../static/images/my/right.png" class="images" mode=""></image>
			</view>
			<!-- 我的地址-->
			<view class="flex padding-lr padding-tb dfs" @click="goNav(list[3].url)">
				<image :src="list[3].image" style="width: 50rpx;height: 50rpx;" mode=""></image>
				<view class="flex-sub margin-left text-df" style="line-height: 50upx;">{{list[3].title}}</view>
				<image src="../../static/images/my/right.png" class="images" mode=""></image>
			</view>
			<!-- 在线客服 -->
			<view class="flex padding-lr padding-tb" @click="goNav(list[4].url)">
				<image :src="list[4].image" style="width: 50rpx;height: 50rpx;" mode=""></image>
				<view class="flex-sub margin-left text-df" style="line-height: 50upx;">{{list[4].title}}</view>
				<image src="../../static/images/my/right.png" class="images" mode=""></image>
			</view>
			<!-- 意见反馈 -->
			<view class="flex padding-lr padding-tb dfs" @click="goNav(list[5].url)">
				<image :src="list[5].image" style="width: 50rpx;height: 50rpx;" mode=""></image>
				<view class="flex-sub margin-left text-df" style="line-height: 50upx;">{{list[5].title}}</view>
				<image src="../../static/images/my/right.png" class="images" mode=""></image>
			</view>
			<!-- 帮助中心 -->
			<view class="flex padding-lr padding-tb dfs" @click="goNav(list[6].url)">
				<image :src="list[6].image" style="width: 50rpx;height: 50rpx;" mode=""></image>
				<view class="flex-sub margin-left text-df" style="line-height: 50upx;">{{list[6].title}}</view>
				<image src="../../static/images/my/right.png" class="images" mode=""></image>
			</view>
			<!-- 设置中心 -->
			<view class="flex padding-lr padding-tb dfs" @click="goNav(list[7].url)">
				<image :src="list[7].image" style="width: 50rpx;height: 50rpx;" mode=""></image>
				<view class="flex-sub margin-left text-df" style="line-height: 50upx;">{{list[7].title}}</view>
				<image src="../../static/images/my/right.png" class="images" mode=""></image>
			</view>

		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				avatar: '../../static/logo.png',
				isLogin: true,
				userName: '匿名',
				browse: 0, //浏览数
				fans: 0, //粉丝数
				follow: 0, //关注数
				visitor: 0, //访客数
				userId: '',
				isVip: false,
				invitationCode: '', //邀请码
				list: [{
						image: '../../static/images/my/qianbao.png',
						title: '我的钱包',
						url: '/my/wallet/index'
					},
					{
						image: '../../static/images/my/order.png',
						title: '我的订单',
						url: '/my/order/index'
					},

					{
						image: '../../static/images/my/eye.png',
						title: '我的足迹',
						url: '/my/gird/browse'
					},
					{
						image: '../../static/images/my/address.png',
						title: '我的地址',
						url: '/my/address/address?id=' + 0
					},
					{
						image: '../../static/images/my/kefu.png',
						title: '在线客服',
						url: '/my/setting/customer'
					},
					{
						image: '../../static/images/my/yijian.png',
						title: '意见反馈',
						url: '/my/feedback/index'
					},
					{
						image: '../../static/images/my/help.png',
						title: '帮助中心',
						url: '/my/setting/help'
					},
					{
						image: '../../static/images/my/set.png',
						title: '设置中心',
						url: '/my/setting/index'
					},
					{
						image: '../../static/images/my/ruzhu.png',
						title: '商家入驻',
						url: '/my/renzheng/index?classify=1'
					},
					{
						image: '../../static/images/my/orders.png',
						title: '我的接单',
						url: '/my/takeOrder/index'
					},
					{
						image: '../../static/images/my/dabu.png',
						title: '我的发布',
						url: '/my/publish/index'
					}
				],
				geRen: 0,
				Qe: 0,
				XCXIsSelect: '否',
				renzheng: false
			}
		},
		onLoad() {
			this.XCXIsSelect = this.$queue.getData("XCXIsSelect");

		},
		onShow() {
			this.Qe = uni.getStorageSync("Qe")
			this.geRen = uni.getStorageSync("geRen")
			if (this.Qe == 2 || this.geRen == 2) {
				this.renzheng = false
			} else {
				this.renzheng = true
			}
			this.userId = uni.getStorageSync('userId')
			if (this.userId) {
				this.isLogin = false
				this.getUserInfo()
				this.getRenZheng()
				this.getRenZhengs()
				this.getAmount()
				this.getIsVip()
			} else {
				this.isLogin = true
				this.userName = '匿名'
				this.browse = 0
				this.fans = 0
				this.follow = 0
				this.visitor = 0
				this.avatar = '../../static/logo.png'
			}

		},
		methods: {

			goNav(e, name) {
				console.log(e)
				if (this.userId) {
					uni.navigateTo({
						url: e
					})

				} else {
					uni.showModal({
						title: '提示',
						content: '您还未登录,请先登录',
						success: function(res) {
							if (res.confirm) {
								console.log('用户点击确定');
								uni.navigateTo({
									url: '/pages/public/login'
								})
							} else if (res.cancel) {
								console.log('用户点击取消');
							}
						}
					})
				}
			},
			goLogin(e) {
				uni.navigateTo({
					url: e
				})
			},
			getAmount() {
				this.$Request.get("/app/userBrowse/selectAmount").then(res => {
					if (res.code == 0) {
						this.browse = res.data.browse
						this.fans = res.data.fans
						this.follow = res.data.follow
						this.visitor = res.data.visitor
					}
				});
			},
			getUserInfo() {
				this.$Request.get("/app/user/selectUserById").then(res => {
					if (res.code == 0) {
						this.userName = res.data.userName
						this.invitationCode = res.data.invitationCode
						this.avatar = res.data.avatar ? res.data.avatar : '../../static/logo.png'
						this.isAuthentication = res.data.isAuthentication

						uni.setStorageSync('isAuthentication', res.data.isAuthentication)

						uni.setStorageSync('avatar', res.data.avatar)
						uni.setStorageSync('invitationCode', res.data.invitationCode)
						uni.setStorageSync('zhiFuBao', res.data.zhiFuBao)
						uni.setStorageSync('zhiFuBaoName', res.data.zhiFuBaoName)

					}
				});
			},
			// 个人认证数据
			getRenZheng() {
				let classify
				this.$Request.get("/app/userCertification/queryInsert?classify=" + 1).then(res => {
					console.log(res)
					if (res.code == 0) {
						if (res.data == null) {
							this.geRen = 0
							uni.setStorageSync("geRen", this.geRen)
						} else if (res.data.status == 0) {
							this.geRen = 1
							uni.setStorageSync("geRen", this.geRen)
						} else if (res.data.status == 1) {
							this.geRen = 2
							uni.setStorageSync("geRen", this.geRen)
						} else if (res.data.status == 2) {
							this.geRen = 3
							uni.setStorageSync("geRen", this.geRen)
						}
					}
				});
			},
			// 企业认证数据
			getRenZhengs() {
				let classify
				this.$Request.get("/app/userCertification/queryInsert?classify=" + 2).then(res => {
					console.log(res)
					if (res.code == 0) {
						if (res.data == null) { //未实名
							this.Qe = 0
							uni.setStorageSync("Qe", this.Qe)
						} else if (res.data.status == 0) { //审核中
							this.Qe = 1
							uni.setStorageSync("Qe", this.Qe)
						} else if (res.data.status == 1) { //已实名
							this.Qe = 2
							uni.setStorageSync("Qe", this.Qe)
						} else if (res.data.status == 2) { //已拒绝
							this.Qe = 3
							uni.setStorageSync("Qe", this.Qe)
						}
					}
				});
			},
			getIsVip() {
				this.$Request.get("/app/UserVip/isUserVip").then(res => {
					if (res.code == 0) {
						this.isVip = res.data
						uni.setStorageSync('isVIP', res.data)
					}
				});
			}

		}
	}
</script>

<style lang="scss">
	.bg {
		background: #FFFFFF;
	}

	.camera {
		width: 54px;
		height: 44px;

		&:active {
			background-color: #ededed;
		}
	}

	.btn-bg {
		width: 64px;
		height: 28px;
		background: linear-gradient(90deg, #CDA26E 0%, #DCB78A 100%);
		border-radius: 28px;
		text-align: center;
		line-height: 28px;
		margin-top: 4px;
		color: '#604320'
	}

	.images {
		width: 18rpx;
		height: 30rpx;
	}

	.dfs {
		display: flex;
		align-items: center;
	}

	.lovip {
		width: 100rpx;
		height: 35rpx;
		position: relative;
		top: -39rpx;
	}
</style>
