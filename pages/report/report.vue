<template>
	<view style="background-color: #f0f0f0;" :style="'height: ' + this.windowH + 'px'">
		<view style="background-color: #80d1e3; height: 100rpx; display: flex;">
			<view class=title style="color: white;">REPORT</view>
		</view>

		<view style="padding-top: 20%; text-align: center;">DATA: {{mondayData}} ~ {{sundayData}}</view>
		<view style="display: flex; margin-left: 20%;">
			<view style="background-color: #22a2c3;" class="colorBall">M</view>
			<view style="background-color: #22a2c3;" class="colorBall">T</view>
			<view style="background-color: #22a2c3;" class="colorBall">W</view>
			<view style="background-color: #22a2c3;" class="colorBall">T</view>
			<view style="background-color: #22a2c3;" class="colorBall">F</view>
			<view style="background-color: #ee3f4d;" class="colorBall">S</view>
			<view style="background-color: #ee3f4d;" class="colorBall">S</view>
		</view>
		
		<view v-for="(item, index) in itemList" style="display: flex; height: 10%;">
			<view style="margin-left: 2%; margin-top: 5%;">{{item.name}}</view>
			<view style="background-color: #c4c4c4; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 20%;" v-if="item.finish[0] == 0"></view>
			<view style="background-color: #c4c4c4; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 31.5%;" v-if="item.finish[1] == 0"></view>
			<view style="background-color: #c4c4c4; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 43%;" v-if="item.finish[2] == 0"></view>
			<view style="background-color: #c4c4c4; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 54.5%;" v-if="item.finish[3] == 0"></view>
			<view style="background-color: #c4c4c4; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 66%;" v-if="item.finish[4] == 0"></view>
			<view style="background-color: #c4c4c4; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 77.5%;" v-if="item.finish[5] == 0"></view>
			<view style="background-color: #c4c4c4; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 89%;" v-if="item.finish[6] == 0"></view>
			
			<view style="background-color: #54d869; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 20%;" v-if="item.finish[0] == 1"></view>
			<view style="background-color: #54d869; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 31.5%;" v-if="item.finish[1] == 1"></view>
			<view style="background-color: #54d869; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 43%;" v-if="item.finish[2] == 1"></view>
			<view style="background-color: #54d869; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 54.5%;" v-if="item.finish[3] == 1"></view>
			<view style="background-color: #54d869; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 66%;" v-if="item.finish[4] == 1"></view>
			<view style="background-color: #54d869; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 77.5%;" v-if="item.finish[5] == 1"></view>
			<view style="background-color: #54d869; margin-top: 6%; width: 11%; height: 10px; position: absolute; margin-left: 89%;" v-if="item.finish[6] == 1"></view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				windowH: undefined,
				mondayData: undefined,
				sundayData: undefined,
				itemList: [],
			}
		},
		methods: {
			
		},
		onLoad() {
			this.windowH = uni.getSystemInfoSync().windowHeight;
		},
		
		onShow() {
			let now = new Date(); 
			let nowTime = now.getTime() ; 
			let day = now.getDay();
			let oneDayTime = 24*60*60*1000 ; 
			
			//显示周一
			let MondayTime = nowTime - (day-1)*oneDayTime ; 
			//显示周日
			let SundayTime =  nowTime + (7-day)*oneDayTime ; 
			
			//初始化日期时间
			let monday = new Date(MondayTime);
			let sunday = new Date(SundayTime);
			
			this.mondayData = monday.toDateString().substring(4);
			this.sundayData = sunday.toDateString().substring(4);
			
			this.itemList = uni.getStorageSync('data');
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
	
	.colorBall {
		width: 35px;
		height: 35px;
		border-radius: 50%;
		margin-left: 2%;
	    margin-right: 2%;
		margin-top: 2%;
		margin-bottom: 2%;
		text-align: center;
		font-size: 26px;
	}
</style>
