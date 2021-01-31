<template>
	<view>
		<button @click="backToHome">BACK</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			backToHome() {
				uni.navigateBack({
					delta: 1,
					animationType:'pop-out',
					animationDuration: 200
				})
			}
		}
	}
</script>

<style>

</style>
