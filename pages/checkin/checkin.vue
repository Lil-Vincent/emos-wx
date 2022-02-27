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
		onShow:function(){
			let that=this
			that.ajax(that.url.validCanCheckIn,"GET",null,function(resp){
				let msg=resp.data.msg
				if(msg!="可以考勤"){
					that.canCheckin=false
					setTimeout(function(){
						uni.showToast({
							title:msg,
							icon:"none"
						})
					},1000)
				}
			})
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
									// console.log(resp.result);
									let address = resp.result.address;
									let addressComponent = resp.result.address_component;
									let nation = addressComponent.nation;
									let province = addressComponent.province;
									let city = addressComponent.city;
									let district = addressComponent.district;
									uni.uploadFile({
										url: that.url.checkin,
										filePath: that.photoPath,
										name: 'photo',
										header: {
											token: uni.getStorageSync('token')
										},
										formData: {
											address: address,
											country: nation,
											province: province,
											city: city,
											district: district
										},
										success: function(resp) {
											// console.log(resp);
											if (resp.statusCode == 500 && resp.data == '不存在人脸模型') {
												uni.hideLoading();
												uni.showModal({
													title: '提示信息',
													content: 'EMOS系统中不存在你的人脸识别模型，是否用当前这张照片作为人脸识别模型？',
													success: function(res) {
														if (res.confirm) {
															//上传头像图片
															uni.uploadFile({
																url: that.url.createFaceModel,
																filePath: that.photoPath,
																name: 'photo',
																header: {
																	token: uni.getStorageSync('token')
																},
																success: function(resp) {
																	if (resp.statusCode == 500) {
																		uni.showToast({
																			title: resp.data,
																			icon: 'none'
																		});
																	} 
																	else if (resp.statusCode == 200) {
																		uni.showToast({
																			title: '人脸建模成功',
																			icon: 'none'
																		});
																	}
																}
															});
														}
													}
												});
											} 
											else if (resp.statusCode == 200) {
												let data = JSON.parse(resp.data);
												let code = data.code;
												let msg = data.msg;
												if (code == 200) {
													uni.hideLoading();
													//签到成功
													uni.showToast({
														title: '签到成功',
														complete: function() {
															//TODO 跳转到签到结果统计页面
															uni.navigateTo({
																url:"../checkin_result/checkin_result"
															})
														}
													});
												}
											} 
											else if (resp.statusCode == 500) {
												uni.showToast({
													title: resp.data,
													icon: 'none'
												});
											}
										}
									});
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
