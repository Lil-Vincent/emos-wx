<template>
	<view>
		<camera device-position="front" flash="off" class="camera" @error="erro" v-if="showCamera"></camera>
		<image mode="widthFix" class="image" :src="photoPath" v-if="showImage"></image>
		<view class="operate-container">
			<button type="primary" class="btn" @tap="clickBtn" :disabled="canCheckin">{{btnText}}</button>
			<button type="warn" class="btn" @tap="afresh" :disabled="canCheckin">重拍</button>
		</view>
		
	</view>
</template>

<script>
	var QQMapWX = require('../../lib/qqmap-wx-jssdk.min.js');
	var qqmapsdk;

	export default {
		data() {
			return {
				canCheckin: false,
				photoPath: '',
				btnText: '拍照',
				showCamera: true,
				showImage: false
			}
		},
		onLoad: function() {
			qqmapsdk = new QQMapWX({
				key: 'LVBBZ-24PKR-TVHWW-W5IBP-GRBP7-ZYFR6'
			});
		},
		methods: {
			clickBtn: function() {
				let that = this;
				if (that.btnText == '拍照') {
					let ctx = wx.createCameraContext();
					ctx.takePhoto({
						quality: 'high',
						success: function(resp) {
							console.log(resp.tempImagePath);
							that.photoPath = resp.tempImagePath;
							that.showCamera = false;
							that.showImage = true;
							that.btnText = '签到';
						}
					});
				} 
				else {
					//这里是点击签到按钮
					uni.showLoading({
						title: '签到中请稍后'
					});
					
					setTimeout(function() {
						uni.hideLoading();  
					}, 30000);
					
					//获取地理定位
					uni.getLocation({
						type: 'wgs84',
						success: function(resp) {
							let latitude = resp.latitude;
							let longitude = resp.longitude;
							// console.log(latitude)
							// console.log(longitude)
							qqmapsdk.reverseGeocoder({
								location: {
									latitude: latitude,
									longitude: longitude
								},
								success: function(resp) {
									console.log(resp.result);
									let address = resp.result.address;
									let addressComponent = resp.result.address_component;
									let nation = addressComponent.nation;
									let province = addressComponent.province;
									let city = addressComponent.city;
									let district = addressComponent.district;
								}
							})

						}
					})

				}
			},
			afresh: function() {
				this.showCamera = true;
				this.showImage = false;
				this.btnText = '拍照';
			}

		}
	}
</script>

<style lang="less">
	@import url("checkin.less");
</style>
