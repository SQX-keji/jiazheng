<template>
	<view class="text-white padding-lr">
		<view class="flex  padding-tb" @click="goNav('/pages/public/pwd')">
			<view class="flex-sub text-df" style="line-height: 50upx;">修改密码</view>
			<image src="../../static/images/my/right.png" style="line-height: 50upx;width: 15rpx;height: 30rpx;"></image>
		</view>
		<!-- <view class="flex  padding-tb">
			<view class="flex-sub text-df" style="line-height: 50upx;">帮助中心</view>
			<image src="../../static/images/my/right.png" style="line-height: 50upx;width: 15rpx;height: 30rpx;"></image>
		</view> -->
		<view class="flex  padding-tb" @click="goNav('/my/setting/xieyi')">
			<view class="flex-sub text-df" style="line-height: 50upx;">用户协议</view>
			<image src="../../static/images/my/right.png" style="line-height: 50upx;width: 15rpx;height: 30rpx;"></image>
		</view>
		<view class="flex  padding-tb" @click="goNav('/my/setting/mimi')">
			<view class="flex-sub text-df" style="line-height: 50upx;">隐私政策</view>
			<image src="../../static/images/my/right.png" style="line-height: 50upx;width: 15rpx;height: 30rpx;"></image>
		</view>
		<view class="flex  padding-tb" @click="goNav('/my/setting/about')">
			<view class="flex-sub text-df" style="line-height: 50upx;">关于我们</view>
			<image src="../../static/images/my/right.png" style="line-height: 50upx;width: 15rpx;height: 30rpx;"></image>
		</view>
		<view class="btn" @click="goOut" >退出登录</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		onLoad() {
			
		},
		methods: {
			goNav(e) {
				uni.navigateTo({
					url:e
				})
			},
			goOut() {
				uni.showModal({
					title: '提示',
					content: '确定退出登录吗？',
					success: function (res) {
						if (res.confirm) {
							console.log('用户点击确定');
							uni.removeStorageSync('userName')
							uni.removeStorageSync('avatar')
							uni.removeStorageSync('userId')
							uni.removeStorageSync('token')
							uni.removeStorageSync('phone')
							uni.removeStorageSync('zhiFuBaoName')
							uni.removeStorageSync('zhiFuBao')
							uni.removeStorageSync('invitationCode')
							uni.removeStorageSync('unionId')
							uni.removeStorageSync('openId')
							uni.removeStorageSync('isVIP')
							uni.showToast({
								title: '退出成功！',
								icon: 'none'
							})
							setTimeout(function() {
								uni.navigateBack()
							},1000)
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				})
			}
		}
		
	}
</script>

<style>
	.btn {
		width: 100%;
		height: 80upx;
		background: #557EFD;
		border-radius: 6upx;
		text-align: center;
		line-height: 80upx;
		margin-top: 40upx;
		font-size: 34upx;
		color: #fff;
	}
</style>
