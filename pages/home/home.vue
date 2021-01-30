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
			            <view><text>编辑</text></view>
						<view><text>删除</text></view>
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
				tagList: ['ALL', 'EXERCISE', 'STUDY'],
				cycleList: ['day', 'week', 'month'],
				itemList: [
					{name: 'RUN', tag: 'EXERCISE', num: 2, unit: 'km', cycle: 0, perc: 0}, 
					{name: 'READ', tag: 'STUDY', num: 10, unit: 'pages', cycle: 1, perc: 30}, 
					{name: 'SWIM', tag: 'EXERCISE', num: 2, unit: 'times', cycle: 2, perc: 50},
				],
			}
		},
		
		onLoad() {
			this.windowH = uni.getSystemInfoSync().windowHeight;
		},
		
		methods: {
			        moveHandle(){
			            return;
			            },
			tagChange(mes) {
				this.currentTag = mes.newVal;
			},
			addNewAct() {
				console.log('按下添加按钮');
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
</style>
