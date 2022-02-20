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
