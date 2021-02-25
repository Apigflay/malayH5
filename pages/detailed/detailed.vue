<template>
	<view class="content">
		<scroll-view scroll-y="true"  class="scrollView"><!-- @scrolltolower="getScrollmsg" -->
		<!-- nav -->
		<view class="navArea">
			<!-- <image class="back" src="/static/imgs/back.png" mode=""></image> -->
			<image class="back" src="/static/imgs/back.png" @click="goBackPages" mode=""></image>
			<text class="text" v-if="pageId==0">{{this.Language.language[language].language29}}</text>
			<text class="text" v-if="pageId==1">{{this.Language.language[language].language30}}</text>
			<text class="text" v-if="pageId==2">{{this.Language.language[language].language31}}</text>
			<text class="more"></text>
			<!-- <image class="more" src="/static/imgs/more.png" mode=""></image> -->
		</view>
		<view class="navAreaKong"></view>
		<!-- swich bar -->
		<view class="swichBarArea">
			<text @click="changeSwichBar(0)" class="ahchorB" :class="swichBarStatus==0?'activeBar':'nomalBar'">{{this.Language.language[language].language32}}</text>
			<text @click="changeSwichBar(1)" class="familyB" :class="swichBarStatus==1?'activeBar':'nomalBar'">{{this.Language.language[language].language33}}</text>
		</view>
		<!-- 统计信息 -->
		<view class="statisticsArea">
			<view class="per">
				<view class="title">
					<text>{{dateText[language]}}</text>
					<text>{{numText[language]}}</text>
				</view>
				<template v-if="weekData.length!=0">
				<view class="perList" v-for="(item,index) in weekData" :key="index">
					<template v-if='pageId==0'>
						<text>{{item.idates}}</text> <text>{{item.number}}</text>
					</template>
					<template v-if='pageId==1'>
						<text>{{item.idates}}</text> <text>{{item.allnums}}</text>
					</template>
					<template v-if='pageId==2'>
						<text>{{item.idates}}</text> <text>{{item.onlineTime}}</text>
					</template>
					<!-- <text>{{item.idates}}</text> <text>{{item.number}}</text> -->
				</view>
				</template>
				<template v-if="weekData.length==0">
				<view class="perListNoData">{{this.Language.language[language].language40}}</view>
				</template>
				<view class="tootle">
					{{tootleText[language]}}:<text class="yellow">{{totleDataNum}}</text>
				</view>
			</view>
		</view>

		</scroll-view>
	</view>
</template>

<script>
	import {encrypt,decrypt,encryptIos,decryptIos,base64ToArrayBuffer,sendData} from "../../lib/js/GlobalFunction.js"
	export default {
		data() {
			return {
				language:0,//0 简 1繁 2 en 3 ms
				userIdx:null,//userIdx
				pageId:'',//0 宝宝 1 流水 2 时长
				token:null,//token no
				params:null,
				swichBarStatus:0,//当前激活的swichbar 0 本周 1 上周   id 2 本周  3 上周
				dateText:['日期','日期','Date','Tarikh'],
				numText:['数量','數量','Amount','Amaun'],
				tootleText:['总计','總計','Total','Jumlah'],
				weekData:[],//本周上周数据
				totleDataNum:0,//总计默认0
				nowWeek:[],//本周数据  no
				lastWeek:[],//上周数据  no
				
			}
		},
		onLoad(option) {
			console.log(option)
			this.language=option.language;
			this.userIdx=option.userIdx;
			this.pageId=option.pageId;
			this.params=option.params;
			console.log(this.swichBarStatus)
			this.getInit(this.swichBarStatus+2)
		},
		methods: {
			goBackPages:function(){//0--ios 1--android
				console.log('back')

				uni.navigateBack({
				    delta: 1
				});
				
				
				
			},
			changeSwichBar:function(id){
				console.log(id)
				this.swichBarStatus=id;
				this.getInit(id+2)
				// if(id==1){
				// 	// this.getControlMsg()
				// 	
				// }
			},
			getInit:function(operId){
				console.log(operId)
				var that = this;
				uni.request({
					url: this.GLOBAL.urlPoint+'family/getProfitList', //仅为示例，并非真实接口地址。
					method:"GET",
					data: {
						userIdx: this.userIdx,
						oper:operId,//1-总流水 2本周 3-上周
						token:this.params,
					},
					success: (res) => {
						console.log(res.data);
						if(res.data.code==100){
							this.weekData=res.data.data;
							var arrayData=res.data.data;
							var tNum=0;
							// var tNum1=0;
							// var tNum2=0;
							arrayData.forEach(function(item,index){
								console.log(item)
								console.log(that.pageId)
								if(that.pageId==0){
									tNum=tNum+item.number;
								}else if(that.pageId==1){
									tNum=tNum+item.allnums;
								}else if(that.pageId==2){
									tNum=tNum+item.onlineTime;
								}
							})
							this.totleDataNum=tNum;
						}else{
							this.weekData=[];
							this.totleDataNum=0;
						}
					}
				});
			}
		}
	}
</script>

<style lang="scss">
page{
	width: 100%;
	height: 100%;
	// background: #F6F6F6;
	background: #fff;
}
.content {
	width: 100%;
	height: 100%;
	background: #F6F6F6;
	.scrollView{
		width: 100%;
		height: 100%;
		.navArea{
			z-index: 899;
			position: fixed;
			top: 0;
			left: 0;
			width: 100%;
			height: 88rpx;
			display: flex;
			align-items: center;
			justify-content: space-between;
			background: #fff;
			.back{
				margin-left: 33rpx;
				width: 36rpx;
				height: 36rpx;
				padding: 10rpx;
			}
			.text{
				color: #333333;
				font-size: 36rpx;
				font-family: PingFang SC;
				font-weight: bold;
			}
			.more{
				margin-right:33rpx;
				width: 34rpx;
				height: 34rpx;
				padding: 5rpx;
			}
		}
		.navAreaKong{
			width: 100%;
			height: 88rpx;
		}
		.swichBarArea{//swichBar
			width: 100%;
			height: 80rpx;
			background: #fff;
			display: flex;
			align-items: center;
			justify-content: center;
			font-size: 30rpx;
			font-family: PingFang SC;
			font-weight: bold;
			color: #666666;
			.ahchorB{
				padding:10rpx 5rpx;
				margin-right: 130rpx;
			}
			.familyB{
				padding:10rpx 0;
				margin-left: 130rpx;
			}
			.activeBar{
				color: #FFCB25;
				border-bottom: 6rpx solid ;
				// border-image:linear-gradient(90deg, #FD912A, #FFCB25) 3 3;
			}
			.nomalBar{
				border-bottom: 6rpx solid #fff;
			}
		}
		.statisticsArea{//统计
			width: 100%;
			.per{
				background: #fff;
				width: 600rpx;
				padding: 0rpx 75rpx;
				margin-bottom: 6rpx;
				.title{
					display: flex;
					align-items: center;
					justify-content: space-between;
					height: 78rpx;
					font-size: 32rpx;
					font-family: PingFang SC;
					font-weight: bold;
					color: #333333;
				}
				.perList{
					display: flex;
					align-items: center;
					justify-content: space-between;
					height: 44rpx;
					font-size: 30rpx;
					font-weight: 500;
					color: #333333;
				}
				.perListNoData{
					text-align: center;
					font-size: 30rpx;
					line-height: 50rpx;
				}
				.tootle{
					text-align: right;
					font-size: 32rpx;
					height: 70rpx;
					line-height: 70rpx;
					.yellow{
						color: #FFCB25;
					}
				}
			}
			
		}
	}
}
</style>
