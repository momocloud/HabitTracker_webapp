<template>
	<view style="background-color: #f0f0f0;" :style="'height: ' + this.windowH + 'px'">
		<view style="background-color: #80d1e3; height: 100rpx; display: flex;">
			<image src='../../static/back.svg' style="filter: invert(100%);" class=titleBtn @click="backToHome"></image>
			<view class=title style="color: white;">EDIT</view>
			<image src='../../static/tick.svg' class=titleBtn style="margin-left: 78%; filter: invert(100%);" @click="addFindsh"></image>
		</view>
		
		<view style="text-align: center; font-size: 36rpx; padding-top: 5%; color: #808080; font-size: 30px;">
			{{item.name}}
		</view>
		
		<!-- Goal -->
		<view class="tintText">Goal :</view>		
		<view class=itemBkg style="margin-top: 5%; display: flex;">
			<input :placeholder="item.num" type="number" class="setIn" style="background-color: #ccedf4; width: 20%; border-radius: 27px; text-align: center; margin-left: 2%;" @input="onNumInput"/>
			<input :placeholder="item.unit" type="text" class="setIn" style="background-color: #ccedf4; width: 25%; border-radius: 27px; text-align: center; margin-left: 5%;" @input="onUnitInput"/>
			<view class="setIn" style="margin-left: 5%;">per</view>
			<view style="width: 25%; position: absolute; margin-left: 70%; padding-top: 7rpx;">
				<xfl-select-white 
					:list="unitList"
					:clearable="false"
					:showItemNum="5" 
					:listShow="false"
					:isCanInput="false"  
					:style_Container="'height: 80rpx; font-size: 20px; '"
					:placeholder = "'placeholder'"
					:initValue="unitList[item.cycle]"
					:selectHideType="'hideAll'"
					@change="cycleChange"
				>
				</xfl-select-white>
			</view>
		</view>
		
		<view class=itemBkg style="margin-top: 5%; display: flex;">
			<view class="setIn" style="margin-left: 20%;">total</view>
			<input :placeholder="item.cycleCounter" type="number" class="setIn" style="background-color: #ccedf4; width: 30%; border-radius: 27px; text-align: center; margin-left: 2%;" />
			<view class="setIn" style="margin-left: 5%;">{{cycleType}}</view>
		</view>

		<!-- Reminder -->
		<view class="tintText">Reminder :</view>
		<view class=itemBkg style="margin-top: 5%; display: flex;">
			<view class="setIn" style="margin-left: 5%;">Don't remind me</view>
			<switch @change="changeRe" style="margin-left: 30%; margin-top: 2%;" color="#ccedf4"></switch>
		</view>
		<view class=itemBkg style="margin-top: 5%; display: flex;">
			<view class="setIn" style="margin-left: 5%;">Remind me at</view>
			<date-time-picker ref='date-time' type='hour-minute' @change='dateTimeChange'></date-time-picker>
			<view class="setIn" style="margin-left: 2%; background-color: #ccedf4; border-radius: 27px; width: 17%; text-align: center; margin-bottom: 2.5%; color: #808080; font-size: 21px;" @click="clickChangeTime">{{remindTime}}</view>
			<view class="setIn" style="margin-left: 2%;">every</view>
			<view style="width: 20%; position: absolute; margin-left: 77%; padding-top: 7rpx;">
				<xfl-select-white 
					:list="remindTimeList"
					:clearable="false"
					:showItemNum="5" 
					:listShow="false"
					:isCanInput="false"  
					:style_Container="'height: 80rpx; font-size: 20px; '"
					:placeholder = "'placeholder'"
					:initValue="'day'"
					:selectHideType="'hideAll'"
				>
				</xfl-select-white>
			</view>
		</view>
		
		<!-- Tag -->
		<view class="tintText">Tag :</view>
		<view class=itemBkg style="margin-top: 5%;">
			<input style="text-align: center; font-size: 36rpx; padding-top: 3%;" :placeholder="item.tag" @input="onTagInput" />
		</view>
		
		<!-- Color -->
		<view class="tintText">Color :</view>
		<view :style="'background-color:' + colorValue " style= "height: 200rpx; ">
			<view style="display: flex;">
				<view style="background-color: #ee3f4d;" class="colorBall"@click="colorValue='#ee3f4d'"></view>
				<view style="background-color: #f07c82;" class="colorBall"@click="colorValue='#f07c82'"></view>
				<view style="background-color: #c35691;" class="colorBall"@click="colorValue='#c35691'"></view>
				<view style="background-color: #8076a3;" class="colorBall"@click="colorValue='#8076a3'"></view>
				<view style="background-color: #a7a8bd;" class="colorBall"@click="colorValue='#a7a8bd'"></view>
				<view style="background-color: #93b5cf;" class="colorBall"@click="colorValue='#93b5cf'"></view>
			</view>
			<view style="display: flex;">
				<view style="background-color: #22a2c3;" class="colorBall"@click="colorValue='#22a2c3'"></view>
				<view style="background-color: #2c9678;" class="colorBall"@click="colorValue='#2c9678'"></view>
				<view style="background-color: #bec936;" class="colorBall"@click="colorValue='#bec936'"></view>
				<view style="background-color: #eed045;" class="colorBall"@click="colorValue='#eed045'"></view>
				<view style="background-color: #ffa60f;" class="colorBall"@click="colorValue='#ffa60f'"></view>
				<view style="background-color: #de7622;" class="colorBall"@click="colorValue='#de7622'"></view>
			</view>
		</view>
		
	</view>
</template>

<script>
	import DateTimePicker from '../../components/bory-dateTimePicker/bory-dateTimePicker.vue'
	export default {
		
		components:{DateTimePicker},
		data() {
			return {
				windowH: undefined,
				nameValue: undefined,
				numValue: undefined,
				unitValue: undefined,
				cycleValue: undefined,
				cycleType: 'day',
				remindValue: false,
				remindTime: '00:00',
				remindType: 'day',
				tagValue: undefined,
				colorValue: '#fff',
				
				unitList: ['day', 'week', 'month'],
				remindTimeList: ['day', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
				
				index:undefined,
				itemList: [],
				item: undefined,
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
				if (this.cycleValue == '') {
					this.cycleValue = 999;
				}
				
				switch (this.cycleType) {
					case 'day': this.item.cycle = 0; break;
					case 'week': this.item.cycle = 1; break;
					case 'month': this.item.cycle = 2; break;
					default: this.item.cycle = 0;
				}

				
				this.item.name = this.nameValue;
				this.item.tag = this.tagValue;
				this.item.num = this.numValue;
				this.item.unit = this.unitValue;
				this.item.color = this.colorValue;
				this.item.totalCycle = this.cycleValue;
				

				this.itemList[this.index] = this.item;

				uni.setStorageSync('data',this.itemList);
				uni.navigateBack({
					delta: 1,
					animationType:'pop-out',
					animationDuration: 200
				});
			},
			
			//键入名字时将数据保存到配置文件
			onNameInput(e) {
				this.nameValue = e.target.value;
				console.log(this.nameValue);
			},
			onTagInput(e) {
				this.tagValue = e.target.value;
				console.log(e.target.value);
			},
			onNumInput(e){
				this.numValue = e.target.value;
				console.log(this.numValue)
			},
			onUnitInput(e){
				this.unitValue = e.target.value;
				console.log(this.unitValue)
			},
			onCycleInput(e){
				this.cycleValue = e.target.value;
				console.log(this.cycleValue);
			},
			
			changeRe(e) {
				console.log(e.target.value)
			},
			
			dateTimeChange(e) {
				this.remindTime = e;
			},
			
			clickChangeTime() {
				this.$refs['date-time'].show();
			},

			cycleChange(e) {
				this.cycleType = e.newVal;
				console.log(this.cycleType);
			}
			
		},
		
		
		
		onLoad: function(parm) {
			this.windowH = uni.getSystemInfoSync().windowHeight;
			this.itemList = uni.getStorageSync('data');
			this.item = this.itemList[parm.id];
			this.index = parm.id;
			
			this.nameValue = this.item.name;
			this.tagValue = this.item.tag;
			this.numValue = this.item.num;
			this.unitValue = this.item.unit;
			this.cycleValue = this.item.cycleCounter;
			this.colorValue = this.item.color;
			this.cycleType = this.unitList[this.item.cycle];
		},
		

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
	
	.setIn {
		margin-top: 21rpx;
		font-size: 20px;
		color: #000000;
	}
	
	.itemBkgHalf {
		height: 50px;
		background-color: white;
		width: 48%;
	}
	
	.tintText {
		font-size: 20px;
		margin-left: 2%;
		margin-top: 10%;
	}
	
	.colorBall {
		width: 40px;
		height: 40px;
		border-radius: 50%;
		margin-left: 3%;
	    margin-right: 3%;
		margin-top: 2%;
		margin-bottom: 2%;
	}
</style>
