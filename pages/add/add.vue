<template>
	<view style="background-color: #f0f0f0;" :style="'height: ' + this.windowH + 'px'">
		<view style="background-color: white; height: 100rpx; display: flex;">
			<image src='../../static/back.svg' class=titleBtn @click="backToHome"></image>
			<view class=title>ADD</view>
			<image src='../../static/tick.svg' class=titleBtn style="margin-left: 78%;" @click="addFindsh"></image>
		</view>
		
		<view class=itemBkg style="margin-top: 5%;">
			<input style="text-align: center; font-size: 36rpx; padding-top: 3%;" placeholder="Tap to enter its name!" @input="onNameInput" />
		</view>
		
		<view class=itemBkg style="margin-top: 5%; display: flex;">
			<view class="setLft">Start Data:</view>
			
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				windowH: undefined,
				nameValue: undefined,
				
			}
		},
		methods: {
			//返回上一层
			backToHome() {
				uni.navigateBack({
					delta: 1,
					animationType:'pop-out',
					animationDuration: 200
				});
			},
			
			//完成
			addFindsh() {
				console.log(this.nameValue);
				uni.navigateBack({
					delta: 1,
					animationType:'pop-out',
					animationDuration: 200
				});
			},
			
			//键入名字时将数据保存到配置文件
			onNameInput(e) {
				this.nameValue = e.target.value;
			}
		},
		onLoad() {
			this.windowH = uni.getSystemInfoSync().windowHeight;
		}
	}
</script>

<style>
	.title {
		margin-top: 3%;
		text-align: center;
		font-size: 24px;
		color: #000000;
		width: 150rpx;
		position: absolute;
		margin-left: 40%;
	}
	
	.titleBtn {
		width: 10%;
		height: 70%;
		margin-top: 2%;
	}
	
	.setBkg {
		background-color: white;
		height: 50px;
		width: 100%;
		display: flex;
	}
	
	.itemBkg {
		height: 50px;
		background-color: white;
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
</style>
