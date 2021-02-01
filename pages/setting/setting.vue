<template>
	<view style="background-color: #f0f0f0;" :style="'height: ' + this.windowH + 'px'">
		<view style="background-color: white; height: 100rpx;">
			<view class=title>SETTINGS</view>
		</view>
		
		<view class=litText style="margin-top: 50rpx;">General</view>
		<view class=setBkg style="margin-top: 25rpx;">
			<view class=setLft>Language</view>
			<view style="width: 35%; position: absolute; margin-left: 60%; padding-top: 7rpx;">
				<xfl-select-white 
					:list="langList"
					:clearable="false"
					:showItemNum="5" 
					:listShow="false"
					:isCanInput="false"  
					:style_Container="'height: 80rpx; font-size: 20px;'"
					:placeholder = "'placeholder'"
					:initValue="'English'"
					:selectHideType="'hideAll'"
				>
				</xfl-select-white>
			</view>
		</view>
		
		<view class=litText style="margin-top: 50rpx;">Community</view>
		<view class=setBkg style="margin-top: 25rpx;">
			<view class=setLft>Twitter</view>
			<view class=setRht>@HabitTracker</view>
		</view>
		<view class=setBkg style="margin-top: 25rpx;">
			<view class=setLft>Telegram</view>
			<view class=setRht>@HabitTracker</view>
		</view>
		<view class=setBkg style="margin-top: 25rpx;">
			<view class=setLft>GitHub</view>
			<view class=setRht>@HabitTracker</view>
		</view>
		
		<view class=litText style="margin-top: 50rpx;">About</view>
		<view class=setBkg style="margin-top: 25rpx;">
			<view class=setCen>About us</view>
		</view>
		<view class=setBkg style="margin-top: 25rpx;">
			<view class=setCen>Privacy Policy</view>
		</view>
		
		<view style="margin-top: 50rpx; text-align: center; font-size: 12px; color: #818181;">Version beta 0.1</view>
		
	</view>

</template>

<script>
	export default {
		data() {
			return {
				windowH: undefined,
				langList: ['English']
			}
		},
		methods: {
			
		},
		onLoad() {
			this.windowH = uni.getSystemInfoSync().windowHeight;
		},
	}
</script>

<style>
	.title {
		  padding-top: 22rpx;
		  text-align: center;
		  font-size: 24px;
		  color: #000000;
	}
	
	.litText {
		  width: 100rpx;
		  height: 25rpx;
		  margin-left: 20rpx;
		  font-size: 12px;
		  text-align: left;
		  color: #818181;
	}
	
	.setBkg {
		background-color: white;
		height: 50px;
		width: 100%;
		display: flex;
	}
	
	.setLft {
		margin-left: 20rpx;
		padding-top: 21rpx;
		font-size: 20px;
		color: #000000;
	}
	
	.setRht {
		position: absolute;
		margin-left: 55%;
		padding-top: 21rpx;
		font-size: 20px;
		color: #000000;
	}
	
	.setCen {
		width: 100%;
		padding-top: 21rpx;
		font-size: 20px;
		text-align: center;
		color: #000000;
	}
	
</style>
