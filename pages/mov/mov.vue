<template>
	<view class="content" :style="[{ height: (windowHeight - statusBarHeight) + 'px' }]">
		<image style="width: 100%;height: 100%;" src="../../static/LOGO.svg" mode="center"></image>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				statusBarHeight: 0,
				windowHeight: 0,
			}
		},
		onLoad() {
			setTimeout(() => {
				//获取状态栏高度，setTimeout后才能调用uni.
				uni.getSystemInfo({
					success: res => {
						this.statusBarHeight = res.statusBarHeight;
						this.windowHeight = res.windowHeight;
					}
				});
			}, 1);
		},
		onShow() {
			setTimeout(()=>{
				uni.navigateTo({
					url:'../index/index'
				})
			}, 3200);
		},
		methods: {

		}
	}
</script>

<style>
	.content {
		width: 750upx;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: space-around;
	}
</style>
