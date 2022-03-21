<!-- <template>
	<view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			uni.login({
				provider: 'weixin',
				success: function(resp) {
					let code = resp.code;
					console.log(code)
					that.ajax(that.url.login,"POST",{"code":code},function(resp){
						let permission = resp.data.permission;
						uni.setStorageSync('permission', permission);
						console.log("success")
						//TODO 跳转到登陆页面
						uni.switchTab({
						    url: '../index/index'
						});
					})
				},
				fail:function(e){
					console.log(e)
					uni.showToast({
						icon:"none",
						title:"执行异常"
					})
				}
			});
		}
	}
</script>

<style>

</style>
<template>
	<view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			uni.login({
				provider: 'weixin',
				success: function(resp) {
					let code = resp.code;
					console.log(code)
					that.ajax(that.url.login,"POST",{"code":code},function(resp){
						let permission = resp.data.permission;
						uni.setStorageSync('permission', permission);
						console.log("success")
						//TODO 跳转到登陆页面
						uni.switchTab({
						    url: '../index/index'
						});
					})
				},
				fail:function(e){
					console.log(e)
					uni.showToast({
						icon:"none",
						title:"执行异常"
					})
				}
			});
		}
	}
</script>

<style>

</style>
 -->
 
 <!--  -->