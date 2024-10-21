<template>
	<view class="">
		<view v-if="msgList.length" class="margin-topW">
			<view class="flex padding-tb radius padding-lr-sm bg" @click="goMsg" v-for="(item,index) in msgList"
				:key='index'>
				<view>
					<image style="width: 80rpx;height: 80rpx;border-radius: 80rpx;"
						src="../../static/images/msg/msg.png"></image>
				</view>
				<view class="flex-sub margin-left-sm">
					<view class="flex justify-between">
						<view class="text-white">{{item.title?item.title: '系统消息'}}</view>
						<view v-if="messageCount>0"
							style="height: 32rpx;width: 32rpx;border-radius: 100rpx;background-color: red;color: #FFF;text-align: center;">
							{{messageCount}}</view>
					</view>
					<view>
						<view class="text-grey">{{item.content}}</view>
					</view>
				</view>
			</view>
		</view>

		<view v-if="chatList.length" class="margin-top-sm  content ">
			<view class="radius padding-lr-sm bg" style="margin-top: 4rpx;" @click="goIM(item)"
				v-for="(item,index) in chatList" :key='index'>
				<view class="flex padding-tb " v-if="userId == item.userId">
					<view>
						<u-image shape="circle" width='80rpx' height="80rpx" :src="item.byAvatar"></u-image>
					</view>
					<view class="flex-sub margin-left-sm">
						<view class="flex justify-between">
							<view class="text-white">{{item.byUserName}}</view>
							<view class="text-grey">{{item.messageTime}}</view>
						</view>
						<view class="flex justify-between">
							<view class="text-grey">{{ item.messageType == 1? item.content : '[图片]'}}</view>
							<view v-if="item.contentCount"
								style="height: 32rpx;width: 32rpx;border-radius: 100rpx;background-color: red;color: #FFF;text-align: center;">
								{{item.contentCount}}</view>
						</view>
					</view>
				</view>
				<view class="flex padding-tb" v-else>
					<view>
						<u-image shape="circle" width='80rpx' height="80rpx" :src="item.avatar"></u-image>
					</view>
					<view class="flex-sub margin-left-sm">
						<view class="flex justify-between">
							<view class="text-white">{{item.userName}}</view>
							<view class="text-grey">{{item.messageTime}}</view>
						</view>
						<view class="flex justify-between">
							<view class="text-grey">{{ item.messageType == 1? item.content : '[图片]'}}</view>
							<view v-if="item.contentCount"
								style="height: 32rpx;width: 32rpx;border-radius: 100rpx;background-color: red;color: #FFF;text-align: center;">
								{{item.contentCount}}</view>
						</view>
					</view>
				</view>
			</view>
		</view>

		<empty v-if="!chatList.length && !msgList.length" content='暂无消息'></empty>
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
				page: 1,
				limit: 100,
				chatList: [],
				userId: '',
				msgList: [],
				time: '',
				messageCount: 0
			}
		},
		onLoad() {
			this.getChatList()
		},
		onShow() {
			let that = this
			that.userId = uni.getStorageSync('userId')
			if (that.userId) {
				that.time = setInterval(function() {
					that.getChatList()
					that.getMsgList()
					that.$nextTick(function() {
						that.messageCount = uni.getStorageSync('messageCount')
					})

				}, 1000)

			} else {
				that.chatList = []
				that.msgList = []
			}
		},
		onHide() {
			clearInterval(this.time)
		},
		methods: {
			getChatList() {
				this.$Request.get("/app/chat/selectChatConversationPage", {
					page: this.page,
					limit: this.limit
				}).then(res => {
					if (res.code == 0) {
						this.chatList = res.data.list
					}
				});
			},
			getMsgList() {
				this.$Request.get("/app/message/selectMessageByUserIdLimit1").then(res => {
					if (res.code == 0) {
						this.msgList = res.data.list
					}
				});
			},
			goIM(e) {
				let userId = this.$queue.getData('userId');
				if (e.userId == userId) {
					userId = e.byUserId
				} else {
					userId = e.userId
				}
				uni.navigateTo({
					url: '/pages/msg/im?chatConversationId=' + e.chatConversationId + '&byUserId=' + userId
				})
			},
			goMsg() {
				uni.navigateTo({
					url: '/pages/msg/message'
				})
			}
		}
	}
</script>

<style>
	page {
		background-color: #F7F7F7;
	}

	.bg {
		background: #FFFFFF;
	}
</style>
