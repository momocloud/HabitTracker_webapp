<template>
	<view style="display: flex; flex-direction: column;" @touchmove.stop.prevent="moveHandle">
		<view class="navHome" style="height: 20%; width: 100%; background-color: #80d1e3;">
			<view style="width: 40%;" class="tagSecBtn">
				<xfl-select 
					:list="tagList"
					:clearable="false"
					:showItemNum="5" 
					:listShow="false"
					:isCanInput="false"  
					:style_Container="'height: 80rpx; font-size: 30rpx;'"
					:placeholder = "'placeholder'"
					:initValue="'ALL'"
					:selectHideType="'hideAll'"
					@change="tagChange"
				>
				</xfl-select>
			</view>
			<image src="../../static/add.svg" mode="aspectFit" style="filter: invert(100%);" class="addBtnHome" @click="addNewAct"></image>
		</view>
		
		<view style="background-color: #f0f0f0; margin-top: 0%; flex: 0 0 auto; overflow-y: auto;" :style="'height: ' + this.windowH * 0.895 + 'px'" @touchmove.stop = "">
			<uni-swipe-action>
			    <uni-swipe-action-item v-for="(item, index) in itemList" :key="index" v-show="(currentTag == 'ALL') ? true : (currentTag == item.tag)">
			        <view class=cardBkg :style="'background: ' + itemList[index].color">
						<view class=cardName>{{item.name}}</view>
						<view class=cardDes>{{item.num}} {{item.unit}} per {{cycleList[item.cycle]}}</view>
						<view class=cardPerc>{{item.perc}}%</view>
						<view class=cardTag>{{item.tag}}</view>
						<image class=finishImg src="../../static/finish.svg" @click="finishCard(index)"></image>
					</view>
			        <template v-slot:right>
						<view style="flex-direction: column; width: 150rpx; padding-top: 20%;">
							<image src="../../static/edit.svg" class=cardBtn @click="editCard(index)"></image>
							<image src="../../static/delete.svg" class=cardBtn @click="deleteCard(index)"></image>
						</view>
			        </template>
					
			    </uni-swipe-action-item>
			</uni-swipe-action>

		</view>
		
		<!-- 下面是弹出的内容 -->
		<wyb-popup ref="popup" type="bottom" height="800" width="500" radius="10" :showCloseIcon="true" v-if="itemList.length != 0">
		    <view class="popup-content">
				<view style="text-align: center; font-size: 48px; padding-top: 10%;">FINISH TODAY!</view>
				<view class="popTitle" v-if="itemList[finishCardNum] != undefined">{{itemList[finishCardNum].name}}</view>
				<view style=" display: flex; justify-content: space-between; align-items: center;">
					<input class="popInput" type="number" @input="finishNumInput"/>
					<view style="text-align: center; font-size: 24px; margin-right: 17%; margin-top: 10%;">{{itemList[finishCardNum].unit}}</view>
				</view>
				<image src="../../static/finish.svg" class="popBtn" @click="finishCardComplete"></image>
		    </view>
		</wyb-popup>


	</view>
</template>

<script>
	import xflSelect from '../../components/xfl-select/xfl-select.vue';
	import wybPopup from '@/components/wyb-popup/wyb-popup.vue'
	
	export default {
		components: {xflSelect, wybPopup},
		
		data() {
			return {
				windowH: undefined,
				lastMonday: undefined,
				currentTag: 'ALL',
				tagList: ['ALL'],
				cycleList: ['day', 'week', 'month'],
				itemList: [],
				finishCardNum: 0,
				finishItemNum: 0,
				todayDate: undefined,
			}
		},
		
		onLoad() {
			//获取页面高度
			this.windowH = uni.getSystemInfoSync().windowHeight;
			
			//获取今天日期
			this.todayDate = new Date();
			
			//判断存储数据是否合法
			let needWrite = false;
			let dataSaved = uni.getStorageSync('data');
			if (typeof(dataSaved[0]) != 'null' && typeof(dataSaved[0]) != 'undefined') {
				for (let index = 0; index < dataSaved.length; index++) {
					if (typeof(dataSaved[index].name) != 'string') {
						needWrite = true;
						break;
					}
				}
			} else {
				needWrite = true;
			}
			
			//根据存储数据是否合法判断是否需要写入示例数据
			let simpleValue = [{name: 'VUE', tag: 'STUDY', num: 2, unit: 'hour', cycle: 0, perc: 0, finished: 0, color: '#f07c82', finish: [0,0,0,0,0,0,0], createDate: new Date(this.todayDate.getTime() - 10*24*60*60*1000), cycleCounter: 5, totalCycle: 7},
					{name: 'RUN', tag: 'EXERCISE', num: 2, unit: 'km', cycle: 0, perc: 0, finished: 0, color: '#f07c82', finish: [0,0,0,0,0,0,0], createDate: new Date(this.todayDate.getTime() - 5*24*60*60*1000), cycleCounter: 1, totalCycle: 100}, 
					{name: 'READ', tag: 'STUDY', num: 10, unit: 'pages', cycle: 1, perc: 30, finished: 3, color: '#22a2c3', finish: [1,1,0,1,1,1,0], createDate: new Date(this.todayDate.getTime() - 10*24*60*60*1000), cycleCounter: 1, totalCycle: 10}, 
					{name: 'SWIM', tag: 'ALL', num: 2, unit: 'times', cycle: 2, perc: 50, finished: 1, color: '#b3c936', finish: [1,1,1,1,0,0,0], createDate: new Date(this.todayDate.getTime() - 15*24*60*60*1000), cycleCounter: 0,  totalCycle: 3}];
			if (needWrite) {
				uni.setStorageSync('data', simpleValue);
				console.log("WRITE SIMPLE DATA!");
				uni.setStorageSync('lastMonday', this.GetMonday());
				console.log("WRITE THIS MONDAY!");
			}
			
			//将本地存储数据读入配置文件中
			this.itemList = uni.getStorageSync('data');
			this.lastMonday = uni.getStorageSync('lastMonday');
			
			// 判断是否为新的一周,并决定是否重置report
			if (this.GetDateDiff(this.todayDate.getTime(), Date.parse(this.lastMonday)) >= 7) {
				for (let index = 0; index < this.itemList.length; index++) { this.itemList[index].finish = [0,0,0,0,0,0,0]; }
				uni.setStorageSync('data', this.itemList);
				uni.setStorageSync('lastMonday', this.GetMonday());
				console.log("RESET REPORT!")
			}
			
		},
		
		onShow() {			
			//将本地存储数据读入配置文件中
			this.itemList = uni.getStorageSync('data');
			
			//根据本地数据初始化tagList
			this.tagList = [];
			for (let index = 0; index < this.itemList.length; index++) {
				if (!this.tagList.includes(this.itemList[index].tag)) {
					this.tagList.push(this.itemList[index].tag);
				}
			}
			
			// 更新counter, finished以及perc
			for (let index = 0; index < this.itemList.length; index++) {
				let dayGap = this.GetDateDiff(this.todayDate.getTime(), Date.parse(this.itemList[index].createDate));
				console.log(this.itemList[index].name + ' dayGap: ' + dayGap)
				switch (this.itemList[index].cycle) {
					case 0: {
						let newCounter = dayGap;
						if (newCounter > this.itemList[index].cycleCounter) {
							this.itemList[index].finished = 0;
							this.itemList[index].cycleCounter = newCounter;
							console.log(this.itemList[index].name + ' REFRESH! ')
						}
					}
					case 1: {
						let newCounter = dayGap;
						if (parseInt(newCounter / 7) > this.itemList[index].cycleCounter) {
							this.itemList[index].finished = 0;
							this.itemList[index].cycleCounter = newCounter;
							console.log(this.itemList[index].name + ' REFRESH! ')
						}
					}
					case 2: {
						let newCounter = dayGap;
						if (parseInt(newCounter / 30) > this.itemList[index].cycleCounter) {
							this.itemList[index].finished = 0;
							this.itemList[index].cycleCounter = newCounter;
							console.log(this.itemList[index].name + ' REFRESH! ')
						}
					}
				}
				
				this.itemList[index].perc = this.itemList[index].finished * 100 / this.itemList[index].num;
				if (this.itemList[index].perc >= 100) {
					this.itemList[index].perc = 100;
				}
				this.itemList[index].perc = this.itemList[index].perc.toFixed(0);
			}
			
			// 删除过期的项目
			let toRemove = [];
			for (let index = 0; index < this.itemList.length; index++) {
				if (this.itemList[index].cycleCounter > this.itemList[index].totalCycle ) {
					toRemove.push(index);
				}
			}
			for (let index = 0; index < toRemove.length; index++) {
				console.log(this.itemList[toRemove[index]].name + " REMOVE! ");
				this.deleteCard(toRemove[index]);
			}
			
			uni.setStorageSync('data', this.itemList);
		},
		
		methods: {
			moveHandle() { return; },
			
			tagChange(mes) {
				this.currentTag = mes.newVal;
			},
			
			addNewAct() {
				console.log('按下添加按钮');
				//待添加
				uni.navigateTo({
					url: '../add/add',
					animationType:'pop-in',
					animationDuration: 200
				})
			},
			
			//执行删除时的动作
			deleteCard(index) {
				//将删除卡片的tag存储到临时变量，删除配置文件中itemList的数据
				let deletedTag = this.itemList[index].tag;
				this.itemList.splice(index, 1);
				
				//判断配置文件中itemList的其他数据是否还有这个tag
				let judgeExist = false;
				for (let index = 0; index < this.itemList.length; index++) {
					if (this.itemList[index].tag == deletedTag) {
						judgeExist = true;
						break;
					}
				}
				
				//如果不是ALL或者还有其它项目使用这个tag，就删除tagList中的这个tag
				if (!judgeExist && deletedTag != 'ALL') {
					for (let index = 0; index < this.tagList.length; index++) {
						if (this.tagList[index] == deletedTag) {
							this.tagList.splice(index, 1);
							break;
						}
					}
				}

				//将配置文件中的dataList写入存储内
				uni.setStorageSync('data', this.itemList);
			},
			
			//按下编辑卡片时的动作
			editCard(index) {
				uni.navigateTo({
					url: '../edit/edit?id=' + index,
					animationType:'pop-in',
					animationDuration: 200
				})
			},
			
			//按下tick图标的动作
			finishCard(index) {
				this.finishCardNum = index;
				this.$refs.popup.show();
			},
			
			// 完成界面内输入数字执行的动作
			finishNumInput(e) {
				this.finishItemNum = undefined;
				this.finishItemNum = Number(e.target.value);
			},
			
			finishCardComplete() {
				this.itemList[this.finishCardNum].finished += this.finishItemNum;
				this.itemList[this.finishCardNum].perc = this.itemList[this.finishCardNum].finished * 100 / this.itemList[this.finishCardNum].num;
				if (this.itemList[this.finishCardNum].perc >= 100) {
					this.itemList[this.finishCardNum].perc = 100;
				}
				this.itemList[this.finishCardNum].perc = this.itemList[this.finishCardNum].perc.toFixed(0);
				let dayIndex = this.todayDate.getDay();
				switch(dayIndex) {
					case 0: this.itemList[this.finishCardNum].finish[6] = 1; break;
					default: this.itemList[this.finishCardNum].finish[dayIndex-1] = 1; break;
				}
				this.$refs.popup.hide();
				uni.setStorageSync('data', this.itemList);
			},
		
			// util: 计算两个日期间隔
			GetDateDiff(startTime, endTime) {  
				// 用法例:  this.GetDateDiff(this.todayDate.getTime(), Date.parse(this.itemList[index].createDate))
			    let dates = parseInt((startTime-endTime)/(1000*60*60*24));
			    return dates;    
			},
			
			// util: 获取本周一Date
			GetMonday() {
				let nowTime = this.todayDate.getTime() ; 
				let day = this.todayDate.getDay();
				let oneDayTime = 24*60*60*1000 ; 
				let MondayTime = nowTime - (day-1)*oneDayTime ; 
				return new Date(MondayTime);
			}
			
		}
	}
</script>

<style>
	.navHome {
		display: flex;
		padding-bottom: 5%;
	}
	
	.tagSecBtn {
		margin-top: 5%;
		margin-left: 10%;
	}
	
	
	.addBtnHome {
		width: 70rpx;
		height: 70rpx;
		margin-top: 6%;
		margin-left: 30%;
		background-color: #c16f47;
		border-radius: 50%;
		opacity:0.8;
	}
	.carArea {
		background-color: #f0f0f0;
		margin-top: 20%;
	}
	
	.cardBkg {
		width: 90%;
		margin-left: 5%;
		margin-top: 4%;
		margin-bottom: 2%;
		height: 127px;
		border-radius: 7px;
		box-shadow: 1px 4px 4px 1px rgba(0, 0, 0, 0.25);
		background-color: #c4c4c4;
	}
	
	.cardName {
		position: absolute;
		margin-top: 3%;
		margin-left: 3%;
		font-size: 36px;
		color: #000000;
	}
	
	.cardDes {
		position: absolute;
		margin-top: 25%;
		margin-left: 3%;
		font-size: 15px;
		color: #000000;
	}
	
	.cardPerc {
		position: absolute;
		margin-left: 35%;
		margin-top: 8%;
		font-size: 40px;
		color: #000000;
	}
	
	.cardTag {
		position: absolute;
		width: 17%;
		margin-left: 62%;
		margin-top: 5%;
		padding: 3px 18px 3px 18px;
		border-radius: 5px;
		box-shadow: inset 0 4px 4px 0 rgba(0, 0, 0, 0.25);
		background-color: #ffffff;
		font-size: 12px;
		text-align: center;
	}
	
	.finishImg {
		position: absolute;
		margin-top: 20%;
		margin-left: 70%;
		width: 50px;
		height: 34px;
	}
	
	.cardBtn {
		 height: 45%; 
		 width: 55%;
		 margin-top: 2%;
	}
	
	.popTitle {
		text-align: center; 
		font-size: 24px; 
		margin-top: 5%;
		padding-top: 3%;
		padding-bottom: 3%;
		margin-left: 15%;
		margin-right: 15%;
		border-radius: 5px; 
		background-color: #bfe8f1;
	}
	
	.popInput {
		background-color: #bfe8f1;
		font-size: 24px;
		margin-top: 10%;
		margin-left: 10%;
		width: 40%;
		border-radius: 27px;
		text-align: center;
		padding-top: 2%;
		padding-bottom: 2%;
	}
	
	.popBtn {
		width: 30%;
		height: 100px;
		margin-left: 37%;
		margin-top: 7%;
	}
</style>
