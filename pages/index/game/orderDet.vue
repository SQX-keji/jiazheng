<template>
	<view>
		<view class="padding">
			<view class=" bg radius u-skeleton-fillet" style="padding: 25rpx;">
				<view class=" u-flex" @click="getAddressList()">
					<view class="u-flex-1 text-white margin-left-xs">
						<view v-if="detailaddress ==''" class="padding-tb">请选择地址</view>
						<view v-else>
							<view class="u-font-16  text-bold">{{detailaddress}}</view>
							<view class="u-font-14 margin-top-sm u-tips-color flex justify-between">
								<view style="color: #333333;">{{name}}<text class="margin-left-sm">{{mobile}}</text>
								</view>
							</view>
						</view>
					</view>
					<u-icon name="arrow-right" color="#999999"></u-icon>
				</view>
			</view>
			<view class=" bg radius u-skeleton-fillet margin-top " style="padding: 25rpx;">
				<view class=" u-flex" style="position: relative;">
					<view class="u-m-r-10">
						<u-avatar :src="order.homepageImg" mode="square" size="100"></u-avatar>
					</view>
					<view class="u-flex-1 text-white margin-left-xs">
						<view class="u-font-16  text-bold u-line-1" style="width: 480rpx;">{{order.myLevel}}</view>
						<view class="u-font-14 margin-top-sm u-tips-color flex justify-between" style="color: #999999;">
							<view><text style="color: #999999;margin-right: 5rpx;"
									v-for="(item,index) in order.gameName">{{item}}</text></view>
						</view>
					</view>
					<!-- <view style="position: absolute;top: 0;right: 0;">
						<view class="text-blue">{{isVip? order.memberMoney :order.money}}元/{{order.unit}}</view>
					</view> -->
				</view>
			</view>
			<view class="padding bg margin-top radius u-skeleton-fillet">
				<view class="flex justify-between margin-top-sm">
					<view class="text-lg" style="color: #1E1F31;width: 140upx;">上门时间</view>
					<view class="flex justify-between padding-bottom-sm" @click="show = true">
						<!-- <view>上门时间</view> -->
						<view>
							<text class="margin-right-xs" v-if="startTime == ''">请选择上门时间</text>
							<text class="margin-right-xs">{{startTime}}</text>
							<u-icon name="arrow-right" color="#999999"></u-icon>
						</view>

					</view>
					<!-- <view class="">
						{{startTime}}
					</view> -->
				</view>
				<view class="flex justify-between  margin-top-lg">
					<view class="text-lg" style="color: #1E1F31;width: 140upx;">购买数量</view>
					<u-number-box v-model="value" :min='minNum' bg-color="#E6E6E6" color="#fff" @change="valChange"
						@minus="minus()">
					</u-number-box>
				</view>
				<view class="flex justify-between  margin-top-lg">
					<view class="text-lg" style="color: #1E1F31;																																									: 140upx;">单价
					</view>
					<view class="text-white">{{isVip? order.memberMoney :order.money}}元/{{order.unit}}</view>
				</view>
			</view>
			<view class="padding bg margin-top radius u-skeleton-fillet">
				<view class="flex justify-between">
					<view class="text-lg" style="color: #1E1F31;margin-right: 20upx;">备注说明</view>
					<textarea v-model="remark" class="text-white text-df flex-sub" placeholder="简单描述下您的要求"
						placeholder-style="text-align: right;" style="height: 200upx;text-align: right;"></textarea>
				</view>
			</view>
		</view>
		<view class="text-white flex justify-between cu-bar foot bg padding-lr" style="color: #557EFD;">
			<view>
				实付：<text>￥</text><text style="font-size: 38upx;margin-top: 8upx;">{{price}}</text>
			</view>
			<view class="">
				<u-button :custom-style="customStyle" @click="pay()" shape="circle" :hair-line="false">立即支付</u-button>
			</view>
		</view>
		<u-skeleton :loading="loading" :animation="true" elColor='#FFFFFF' bgColor='#FFFFFF'></u-skeleton>
		<!-- <u-skeleton :loading="loading" :animation="true" elColor='#1E1F31' bgColor='#ffffff'></u-skeleton> -->

		<u-picker v-model="show" mode="time" :params="params" @confirm="statusChange"></u-picker>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loading: true, // 是否显示骨架屏组件
				customStyle: {
					width: '340upx',
					color: '#FFFFFF',
					background: "#557EFD",
					border: 0
				},
				value: '',
				remark: '',
				id: '',
				order: {},
				isVip: false,
				mobile: '',
				name: '',
				cityaddress: '',
				detailaddress: '',
				latitude: '',
				longitude: '',
				province: '',
				city: '',
				district: '',
				startTime: '',
				show: false,
				params: {
					year: false,
					month: true,
					day: true,
					hour: true,
					minute: false,
					second: false
				},
				minNum: '',
				disabled: false,
				addressId: ''
			}
		},
		onLoad(option) {
			console.log(option)
			this.id = option.id
			this.startTime = option.startTime
			this.isVip = uni.getStorageSync('isVIP')
			this.getDet()

		},
		destryed(){
			uni.removeStorageSync('EditAddress')
		},
		onShow() {
			this.addressId = this.$queue.getData('EditAddress');
			if (this.addressId) {
				this.getAddressList(this.addressId);
			} else {
				this.addressMy()
			}
		},
		methods: {
			minus(e) {
				console.log(e)
				console.log(this.minNum)
				if (e.value < this.minNum) {
					// this.disabled = true
					this.value = this.minNum
				}
				
			},
			// 根据地址id查询地址
			getAddressList(addressId) {
				
				if (addressId) {
					this.$Request.getT('/app/address/selectAddressByAddressId?addressId=' + this.addressId).then(res => {
						console.log(res)
						if (res.code == 0) {
							this.name = res.data.name;
							this.mobile = res.data.phone;
							this.cityaddress = res.data.province + res.data.city + res.data.district;
							this.detailaddress = res.data.detailsAddress;
							this.isDefault = res.data.isDefault;
							this.userId = res.data.userId;
							this.latitude = res.data.latitude;
							this.longitude = res.data.longitude;
							this.province = res.data.province
							this.city = res.data.city
							this.district = res.data.district
						}
						// uni.hideLoading();
					});
				} else {
					uni.navigateTo({
						url: '../../../my/address/address?id=' + 1
					})
				}

			},
			// 获取默认地址
			addressMy() {
				this.$Request.getT('/app/address/selectAddressById').then(res => {
					console.log(res)
					if (res.code == 0) {
						this.name = res.data.name;
						this.mobile = res.data.phone;
						this.cityaddress = res.data.province + res.data.city + res.data.district;
						this.detailaddress = res.data.detailsAddress;
						this.isDefault = res.data.isDefault;
						this.userId = res.data.userId;
						this.latitude = res.data.latitude;
						this.longitude = res.data.longitude;
						this.province = res.data.province
						this.city = res.data.city
						this.district = res.data.district
						this.addressId = res.data.addressId
						// this.$queue.setData('EditAddress', res.data.addressId);
					}
				});
			},
			// 选择上门时间
			statusChange(e) {
				console.log(e.month + '-' + e.day + ' ' + e.hour)
				var myDate = new Date();
				if (e.month > myDate.getMonth() + 1) {
					console.log(e.month >= myDate.getMonth() + 1, 1111, e.month)
					this.startTime = e.month + '-' + e.day + ' ' + e.hour
				} else {
					if (e.day > myDate.getDate()) {
						console.log(e.day > myDate.getDate(), 22222)
						this.startTime = e.month + '-' + e.day + ' ' + e
							.hour
					} else {
						if (e.hour - myDate.getHours() >= 1) {
							console.log(e.hour - myDate.getHours() >= 1, 3333)
							this.startTime = e.month + '-' + e.day + ' ' +
								e.hour
						} else {
							uni.showToast({
								title: '选择时间大于当前一小时',
								icon: 'none',
								duration: 1000
							})
						}
					}
				}
			},
			// // 添加地址
			// bindToAdd(){
			// 	uni.navigateTo({
			// 		url:'../../../my/address/address'
			// 	})
			// },
			// 详情
			getDet() {
				this.$Request.get("/app/orderTaking/queryTakingDetails", {
					id: this.id
				}).then(res => {
					if (res.code == 0) {
						this.order = res.data
						this.value = res.data.minNum
						this.minNum = res.data.minNum
						this.order.gameName = res.data.gameName.split(',')
						this.loading = false;
					} else {
						uni.showToast({
							title: res.msg,
							duration: 1000,
							icon: 'none'
						});
					}
				});
			},
			valChange() {
				// this.price = this.order.money * this.value
			},
			pay() {
				// this.addressMy()
				// if (this.addressId == '') {
				// 	this.getAddressList();
				// }

				if (!this.detailaddress) {
					uni.showToast({
						title: '请添加地址',
						icon: 'none',
						duration: 1000
					})
					return
				}
				if (!this.startTime) {
					uni.showToast({
						title: '请选择上门时间',
						icon: 'none',
						duration: 1000
					})
					return
				}
				// let addressId = this.$queue.getData('EditAddress');
				let that = this
				that.$Request.get("/app/orders/generateOrder", {
					id: that.order.id,
					type: 1,
					orderNumber: that.value,
					remarks: that.remark,
					addressId: that.addressId,
					startTime: that.startTime
				}).then(res => {
					if (res.code == 0) {
						uni.showModal({
							title: '付款提示',
							content: '确认支付' + that.price + '元吗?',
							success: function(re) {
								if (re.confirm) {
									console.log('用户点击确定');
									that.$Request.post("/app/orders/payMoney", {
										ordersId: res.data.ordersId,
									}).then(res => {
										if (res.code == 0) {
											uni.showToast({
												title: '支付成功'
											})
											setTimeout(function() {
												uni.redirectTo({
													url: '/my/order/index'
												})

											}, 1000)
											
											uni.removeStorageSync('EditAddress')
										} else {
											uni.showToast({
												title: res.msg,
												icon: 'none'
											})
										}
									});
								} else if (re.cancel) {
									console.log('用户点击取消');
									// uni.removeStorageSync('EditAddress')
								}
							}

						})

					}

				});
			}
		},
		computed: {
			price() {
				let price = this.isVip ? this.order.memberMoney : this.order.money
				console.log(price * 1 * this.value)
				return (price * this.value).toFixed(2)
			}
		}
	}
</script>

<style>
	textarea::-webkit-input-placeholder {
		color: red;

	}

	page {
		background-color: #F5F5F5;
	}

	.bg {
		background-color: #FFFFFF;
	}
</style>
