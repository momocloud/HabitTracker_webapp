<template>
	<view style="display: flex; flex-direction: column;" @touchmove.stop.prevent="moveHandle">
		<view class="navHome" style="height: 20%; width: 100%; background-color: #ffffff;">
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
			<image src="../../static/add.svg" mode="aspectFit" class="addBtnHome" @click="addNewAct"></image>
		</view>
		
		<view style="background-color: #f0f0f0; margin-top: 0%; flex: 0 0 auto; overflow-y: auto;" :style="'height: ' + this.windowH * 0.895 + 'px'" @touchmove.stop = "">
			<uni-swipe-action>
			    <uni-swipe-action-item v-for="(item, index) in itemList" :key="index" v-show="(currentTag == 'ALL') ? true : (currentTag == item.tag)">
			        <view class=cardBkg>
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
				<image src="../../static/finish.svg" class="popBtn"></image>
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
				currentTag: 'ALL',
				tagList: ['ALL'],
				cycleList: ['day', 'week', 'month'],
				itemList: [],
				finishCardNum: 0,
				finishItemNum: 0,
			}
		},
		
		onLoad() {
			//获取页面高度
			this.windowH = uni.getSystemInfoSync().windowHeight;
			
			//判断存储数据是否合法
			let needWrite = false;
			let dataSaved = uni.getStorageSync('data');
			if (typeof(dataSaved[0]) != 'null' && typeof(dataSaved[0]) != 'undefined') {
				for (let index = 0; index < dataSaved.length; index++) {
					if (typeof(dataSaved[index].name) != 'string' || typeof(dataSaved[index].tag) != 'string' || typeof(dataSaved[index].num) != 'number' || typeof(dataSaved[index].unit) != 'string' || typeof(dataSaved[index].cycle) != 'number' || typeof(dataSaved[index].perc) != 'number') {
						needWrite = true;
						break;
					}
				}
			} else {
				needWrite = true;
			}
			
			//根据存储数据是否合法判断是否需要写入示例数据
			let simpleValue = [{name: 'RUN', tag: 'EXERCISE', num: 2, unit: 'km', cycle: 0, perc: 0}, 
					{name: 'READ', tag: 'STUDY', num: 10, unit: 'pages', cycle: 1, perc: 30}, 
					{name: 'SWIM', tag: 'ALL', num: 2, unit: 'times', cycle: 2, perc: 50}];
			if (needWrite) {
				uni.setStorageSync('data', simpleValue);
			}
			
			//将本地存储数据读入配置文件中
			this.itemList = uni.getStorageSync('data');
			
			//根据本地数据初始化tagList
			for ( let index = 0; index < this.itemList.length; index++) {
				if (!this.tagList.includes(this.itemList[index].tag)) {
					this.tagList.push(this.itemList[index].tag);
				}
			}
		},
		
		onShow() {
			//将本地存储数据读入配置文件中
			this.itemList = uni.getStorageSync('data');
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
			
			//按下删除时的动作
			deleteCard(index) {
				console.log('按下删除按钮');
				
				//将删除卡片的tag存储到临时变量，删除配置文件中itemList的数据
				let deletedTag = this.itemList[index].tag;
				this.itemList.splice(index, 1);
				console.log(this.itemList.length)
				
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
			
			finishCard(index) {
				this.finishCardNum = index;
				this.$refs.popup.show();
			},
			
			finishNumInput(e) {
				this.finishItemNum = undefined;
				this.finishItemNum = Number(e.target.value);
			},
		}
	}
</script>

<style>
	.navHome {
		display: flex;
		padding-bottom: 5%;
		line-height: 20%;
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
		background-color: #c4c4c4;
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
		background-color: #9d9d9d;
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
		background-color: rgba(196, 196, 196, 0.5);
	}
	
	.popInput {
		background-color: #c4c4c4;
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
