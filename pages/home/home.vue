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
						<image class=finishImg src="../../static/finish.svg"></image>
					</view>
			        <template v-slot:right>
						<view style="flex-direction: column; width: 150rpx; padding-top: 20%;">
							<image src="../../static/edit.svg" class=cardBtn></image>
							<image src="../../static/delete.svg" class=cardBtn @click="deleteCard(index)"></image>
						</view>
			        </template>
			    </uni-swipe-action-item>
			</uni-swipe-action>

		</view>
		

	</view>
</template>

<script>
	import xflSelect from '../../components/xfl-select/xfl-select.vue';
	export default {
		components: {xflSelect},
		
		data() {
			return {
				windowH: undefined,
				currentTag: 'ALL',
				tagList: ['ALL'],
				cycleList: ['day', 'week', 'month'],
				itemList: [],
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
			}
			
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
		 height: 120rpx; 
		 width: 120rpx;
	}
</style>
