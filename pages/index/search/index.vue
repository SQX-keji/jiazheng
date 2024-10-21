<template>
	<view class="content">
		<view class="search-box">
			<!-- mSearch组件 如果使用原样式，删除组件元素-->
			<!-- <mSearch class="mSearch-input-box" :mode="2" button="inside" :placeholder="defaultKeyword"
				@search="doSearch(false)" @input="inputChange" @confirm="doSearch(false)" v-model="keyword"></mSearch> -->
			<!-- 原样式 如果使用原样式，恢复下方注销代码 -->

			<!-- <view class="input-box">
				<input type="text" :adjust-position="true" :placeholder="defaultKeyword" @input="inputChange" v-model="keyword" @confirm="doSearch(false)"
				 placeholder-class="placeholder-class" confirm-type="search">
			</view>
			<view class="search-btn" @tap="doSearch(false)">搜索</view> -->
			<u-search style="width: 100%;" placeholder="输入搜索内容" :focus="true" v-model="keyword" :show-action="true"
				:animation="true" shape="square" bg-color="#F7F7F7" color="#1A1A1A" action-text="取消" @custom="goBack()"
				@search="doSearch(false)"></u-search>
			<!-- 原样式 end -->
			
		</view>
		<!-- #ifdef MP-WEIXIN -->
		<view style="position: relative;top: 90upx;left: 0;right: 0;z-index:1;">
		<!-- #endif -->
		<!-- #ifndef MP-WEIXIN -->
		<view style="position: relative;top: 46px;left: 0;right: 0;z-index:1;">
		<!-- #endif -->
			<view v-show="isShowKeywordList">
				<ren-dropdown-filter :filterData='filterData' :border="false" :defaultIndex='defaultIndex'
					@onSelected='change' class="u-skeleton-rect">
				</ren-dropdown-filter>
			</view>
			<view class="search-keyword">
				<scroll-view class="keyword-list-box" v-show="isShowKeywordList" scroll-y>
					<view class="margin-lr-sm padding-top-16 ">
						<view class="flex justify-between padding-sm radius margin-top-xs" v-for="(item, index) in keywordList"
							:key="index" @click="goOrder(item)" style="background-color: #FFFFFF;">
							<image :src="item.homepageImg?item.homepageImg: '../../../static/logo.png'"
								style="width: 200rpx;height: 200rpx;border-radius: 10rpx;"></image>
							<view class="flex-sub margin-left text-white flex flex-direction justify-between">
								<view class="flex justify-between align-center">
									<view class="flex ">
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
								<view class="flex radius flex-wrap" style="line-height: 34upx;">
									<!-- <image :src="item.gameImg" style="width: 34rpx;height: 32rpx;" ></image> -->
									<text class="margin-right-xs margin-top-xs" v-for="(item,index) in item.gameName"
										:key="index">{{item}}</text>
									<!-- <text>{{item.myLevel}}</text> -->
								</view>
								<view class="flex" style="align-items: center;font-size: 24rpx;" v-if="item.count">
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
					<!-- <view v-if="keywordList.length == 0">
						暂无数据
					</view> -->
					<empty v-if="keywordList.length == 0"></empty>
				</scroll-view>
				<scroll-view class="keyword-box" v-show="!isShowKeywordList" scroll-y>
					<view class="keyword-block">
						<view class="keyword-list-header">
							<view>热门搜索</view>
							<view>
								<image @tap="hotToggle" :src="'/static/images/index/attention'+forbid+'.png'"></image>
							</view>
						</view>
						<view class="keyword" v-if="forbid==''">
							<view v-for="(keyword,index) in hotKeywordList" @tap="doSearch(keyword)" :key="index">
								{{keyword}}
							</view>
						</view>
						<view class="hide-hot-tis" v-else>
							<view>当前搜热已隐藏</view>
						</view>
					</view>
					<view class="keyword-block" v-if="oldKeywordList.length>0">
						<view class="keyword-list-header">
							<view>历史记录</view>
							<view>
								<image @tap="oldDelete" src="/static/images/index/delete.png"></image>
							</view>
						</view>
						<view class="keyword">
							<view v-for="(keyword,index) in oldKeywordList" @tap="doSearch(keyword)" :key="index">
								{{keyword}}
							</view>
						</view>
					</view>
				</scroll-view>
			</view>
		</view>
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
				defaultKeyword: "",
				keyword: "",
				oldKeywordList: [], //历史记录
				hotKeywordList: [], //热搜
				keywordList: [], //搜索列表
				forbid: '',
				isShowKeywordList: false,
				limit: 10,
				page: 1,
				userId: '',
				isVip: false,
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
			}
		},
		onLoad() {
			this.userId = uni.getStorageSync('userId')
			if (this.userId) {
				this.getSearchList()
			}
			this.isVip = uni.getStorageSync('isVIP')

		},
		methods: {
			// 筛选
			change(e) {

				this.value1 = e[0][0].value
				this.value2 = e[1][0].value
				this.value3 = e[2][0].value
				this.doSearch(this.keyword)
				// this.mescroll.resetUpScroll()
			},
			// 获取搜索历史
			getSearchList() {
				this.$Request.get("/app/Search/selectAppSearchNum").then(res => {
					console.log(res)
					if (res.code == 0) {
						this.oldKeywordList = res.data.userSearchName
						// for (let i = 0; i < this.oldKeywordList.length; i++) {
						// 	this.oldKeywordList[i].gameName = this.oldKeywordList[i].gameName.split(",");
						// }
						this.hotKeywordList = res.data.allSerchName


					}
				});
			},
			//清除历史搜索
			oldDelete() {
				uni.showModal({
					content: '确定清除历史搜索记录？',
					success: (res) => {
						if (res.confirm) {
							console.log('用户点击确定');
							this.$Request.get("/app/Search/deleteAppSearch").then(res => {
								if (res.code == 0) {
									this.getSearchList()
								}
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			//执行搜索
			doSearch(keyword) {
				this.keyword = keyword === false ? this.keyword : keyword;
				this.isShowKeywordList = true;
				if (!this.keyword) {
					uni.showToast({
						title: '请输入内容',
						icon: 'none',
						duration: 1000
					})
					return
				}

				let data = {
					like: this.keyword,
					limit: this.limit,
					page: this.page,
					condition: this.value1, //智能优选
					salesNum: this.value2, //不限男女
					by: this.value3, //价格
					city: uni.getStorageSync('city'),
					latitude: uni.getStorageSync('latitude'),
					longitude: uni.getStorageSync('longitude')
				}
				if (this.userId) {
					this.$Request.get("/app/orderTaking/queryTaking", data).then(res => {
						if (res.code == 0) {
							if (this.page == 1) this.keywordList = [];
							this.keywordList = [...this.keywordList, ...res.data.list]
							for (let i = 0; i < this.keywordList.length; i++) {
								this.keywordList[i].gameName = this.keywordList[i].gameName.split(",");
							}
						}
					});
				} else {
					this.$Request.get("/app/orderTaking/queryTakings", data).then(res => {
						if (res.code == 0) {
							if (this.page == 1) this.keywordList = [];
							this.keywordList = [...this.keywordList, ...res.data.list]
							for (let i = 0; i < this.keywordList.length; i++) {
								this.keywordList[i].gameName = this.keywordList[i].gameName.split(",");
							}
						}
					});
				}
			},
			// 点击取消返回首页
			goBack() {
				uni.navigateBack()
			},
			//热门搜索开关
			hotToggle() {
				this.forbid = this.forbid ? '' : '_forbid';
			},
			// 跳转订单
			goOrder(e) {
				if (this.userId) {
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
			this.doSearch(false);
		},
		onPullDownRefresh: function() {
			this.page = 1;
			this.doSearch(false);
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

	.search-box {
		width: 100%;
		/* background-color: rgb(242, 242, 242); */
		padding: 15upx 2.5%;
		display: flex;
		justify-content: space-between;
		position: sticky;
		top: 0;
		background-color: #FFFFFF;
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		z-index: 9;
		/* #ifdef H5 */
		position: fixed;
		top: 45px;
		left: 0;
		right: 0;
		/* #endif */
	}

	.search-box .mSearch-input-box {
		width: 100%;
	}

	.search-box .input-box {
		width: 85%;
		flex-shrink: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.search-box .search-btn {
		width: 15%;
		margin: 0 0 0 2%;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-shrink: 0;
		font-size: 28upx;
		color: #fff;
		background: linear-gradient(to right, #ff9801, #ff570a);
		border-radius: 60upx;
	}

	.search-box .input-box>input {
		width: 100%;
		height: 60upx;
		font-size: 32upx;
		border: 0;
		border-radius: 60upx;
		-webkit-appearance: none;
		-moz-appearance: none;
		appearance: none;
		padding: 0 3%;
		margin: 0;
		background-color: #ffffff;
	}

	.placeholder-class {
		color: #9e9e9e;
	}

	.search-keyword {
		width: 100%;
		
	}

	.keyword-list-box {
		height: calc(100vh - 110upx);
		padding-top: 10upx;
		/* border-radius: 20upx 20upx 0 0; */
		/* background-color: #fff; */
	}

	.keyword-entry-tap {
		background-color: #eee;
	}

	.keyword-entry {
		width: 94%;
		height: 80upx;
		margin: 0 3%;
		font-size: 30upx;
		color: #333;
		display: flex;
		justify-content: space-between;
		align-items: center;
		border-bottom: solid 1upx #e7e7e7;
	}

	.keyword-entry image {
		width: 60upx;
		height: 60upx;
	}

	.keyword-entry .keyword-text,
	.keyword-entry .keyword-img {
		height: 80upx;
		display: flex;
		align-items: center;
	}

	.keyword-entry .keyword-text {
		width: 90%;
	}

	.keyword-entry .keyword-img {
		width: 10%;
		justify-content: center;
	}

	.keyword-box {
		height: calc(100vh - 110upx);
		/* border-radius: 20upx 20upx 0 0; */
		background-color: #F7F7F7;
	}

	.keyword-box .keyword-block {
		padding: 10upx 0;
	}

	.keyword-box .keyword-block .keyword-list-header {
		width: 94%;
		padding: 10upx 3%;
		font-size: 27upx;
		font-weight: 700;
		/* color: #FFFFFF; */
		display: flex;
		justify-content: space-between;
	}

	.keyword-box .keyword-block .keyword-list-header image {
		width: 40upx;
		height: 40upx;
	}

	.keyword-box .keyword-block .keyword {
		width: 94%;
		padding: 3px 3%;
		display: flex;
		flex-flow: wrap;
		justify-content: flex-start;
	}

	.keyword-box .keyword-block .hide-hot-tis {
		display: flex;
		justify-content: center;
		font-size: 28upx;
		color: #FFFFFF;
	}

	.keyword-box .keyword-block .keyword>view {
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 10upx;
		padding: 0 20upx;
		margin: 10upx 20upx 10upx 0;
		height: 60upx;
		font-size: 28upx;
		background-color: #FFFFFF;
		color: #343546;
	}
</style>
