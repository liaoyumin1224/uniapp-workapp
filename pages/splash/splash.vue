<template>
	<view class="splash">
		<image src="/common/images/logo/logo.png"></image>
	</view>
</template>

<script setup>
	import {onMounted} from 'vue';
	
	onMounted(()=>{
		setTimeout(()=>{
			uni.reLaunch({url:'/pages/login/login'});
		},2000);
	});
</script>

<style lang="scss">
.splash{
	height: 100vh;
	width: 100%;
	display:flex;
	align-items: center;
	justify-content: center;
	image{
		height: 200rpx;
		width: 200rpx;
		z-index:2;
	}
}
</style>
