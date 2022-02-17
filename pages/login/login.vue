<template>
	<view>
		<image src="../../static/logo.png" class="logo" mode="widthFix"></image>
		<view class="logo-title">疫情在线办公系统</view>
		<view class="logo-subtitle">V 2.0.0</view>
		<button class="login-btn" open-type="getUserInfo" @tap="login()">登录</button>
		<view class="register-container">
			无账号
			<text class="register" @tap="toRegister()">立即注册</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			toRegister: function() {
				uni.navigateTo({
					url: '../register/register',
				});
			}

		}
	}
</script>

<style lang="less">
	@import url("login.less");
</style>
