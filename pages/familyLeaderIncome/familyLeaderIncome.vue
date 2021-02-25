<template>
	<view class="content">
		<scroll-view scroll-y="true" @scrolltolower="getScrollmsg" class="scrollView">
		<!-- nav -->
		<view class="navArea">
			<image class="back" @click="goBackPages" src="/static/imgs/back.png" mode=""></image>
			<text class="text">{{this.Language.language[language].language28}}</text>
			<text class="more"></text>
		</view>
		<view class="navAreaKong">
			
		</view>
		<!-- tips -->
		<!-- 申请记录 -->
		<view class="recordArea">
			<view class="per" v-for="(item,index) in initData2" :key="index">
				<image class="logo" :src="item.smallpic" @click="goRePage(item)" mode=""></image>
				<view class="textBox" @click="goRePage(item)">
					<p class="name">{{item.myname}}</p>
					<p class="id">ID:{{item.userIdx}}</p>
				</view>
				<view class="senson">
					
				</view>
				<!-- <view class="senson" v-if="item.state==0">
					{{shenhetext[0][language]}}
				</view>
				<view class="senson" v-if="item.state==1">
					{{shenhetext[1][language]}}
				</view>
				<view class="senson" v-if="item.state==2">
					{{shenhetext[2][language]}}
				</view> -->
			</view>
		</view>
		</scroll-view>
	</view>
</template>

<script>
	import {encrypt,decrypt,encryptIos,decryptIos,productType,base64ToArrayBuffer,sendData} from "../../lib/js/GlobalFunction.js"
	export default {
		data() {
			return {
				language:0,
				userIdx:null,//userIdx
				token:null,//token
				params:null,
				initData1:null,//初始数据 家族列表
				initData2:null,//初始数据 申请记录
				isState:0,//1-存在家族 0不存在家族
				page:1,//默认页数
				shenhetext:[['申请加入中','申請加入中','Applying','Sedang memohon'],['已通过审核','已通過審核','Verified','Diluluskan'],['未通过审核','未通過審核','Not verified','Belum dilulus']],
				marskStatus:false,//
				toUserIdx:'',
				tipText:'',
				totalNum:0,//初始主播数据
				
			}
		},
		onLoad(option) {
			// this.userIdx=option.userIdx;
			this.GetQueryString('token')
			this.getInitLunguage()
			this.getInitMsg()
		},
		methods: {
			goBackPages:function(){//0--ios 1--android
				console.log('back')
				console.log(productType())
				if(productType()==0){
					// GoBackAPP()
					window.location.href='GoBackAPP'
				}else if(productType()==1){
					// android.goBackAPP()
					window.android.goBackAPP()
				}
			},
			goRePage:function(item){
				console.log(item.userIdx)
				console.log(this.params)
				var urlStr='?token='+this.params+'&backid=1'+'&useridx='+item.userIdx;
				console.log(urlStr)
				uni.navigateTo({
					url: '/pages/revenueManagement/revenueManagement'+urlStr
				});	
			},
			GetQueryString:function(name){ //name为要获得的参数名字 
				var that = this;
				  var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)")
				  var r = window.location.hash.split('?')[1].match(reg)
				  if(r != null){
					  that.params=decodeURIComponent(r[2].replace(/%20/g,'%2B'));
					  console.log(that.params)
					  var str = decryptIos(decodeURIComponent(r[2].replace(/%20/g,'%2B')));
					  var obj = str.split('|')
					  console.log(obj)
					  obj.forEach(function(item,index){
						  var item1=item.split('=')
						  if(item1[0]=='useridx'){
							  that.userIdx=item1[1]
						  }else if(item1[0]=='token'){
							  that.token=item1[1]
						  }else{
							  
						  }
					  })
				  }else{
					  
				  }
			},
			getInitLunguage:function(){
              var lang = navigator.language||navigator.userLanguage;//常规浏览器语言和IE浏览器
			  console.log(lang)
              // lang = lang.substr(0, 2);//截取lang前2位字符
              if(lang == 'zh-CN'){
                this.language=0;
              }else if(lang == 'zh-TW'){
                this.language=1;
              }else if(lang == 'en'){
                this.language=2;
              }else if(lang == 'ms'){
                this.language=3;
              }else{
                this.language=0;
              }
            },
			getInitMsg:function(){
				uni.request({
					url: this.GLOBAL.urlPoint+'family/FamilyManage', //仅为示例，并非真实接口地址。
					method:"GET",
					data: {
						userIdx: this.userIdx,
						page:this.page,
						pagesize:20,
						token:this.params,
						oper:1,//oper=0 默认 oper=1 主播列表
					},
					success: (res) => {
						console.log(res.data);
						if(res.data.data.isState==0){//不存在
							this.isState=0;
							this.initData2=res.data.data.list;
							this.totalNum=res.data.data.count;
						}else{//存在
							this.isState=1;
							this.initData1=res.data.data.list;
						}
						
					}
				});
			},
			getMoreInitMsg:function(){
				uni.request({
					url: this.GLOBAL.urlPoint+'family/FamilyManage', //仅为示例，并非真实接口地址。
					method:"GET",
					data: {
						userIdx: this.userIdx,
						page:this.page,
						pagesize:20,
						token:this.params,
					},
					success: (res) => {
						console.log(res.data);
						if(res.data.data.isState==0){//不存在
							this.initData2.push.apply(this.initData2,res.data.data.list);
						}else{//存在
							this.isState=1;
							this.initData1=res.data.data.list;
						}
						
					}
				});
			},
			getScrollmsg:function(){//scrollView
				console.log('scroll bottom')
				if(this.initData2.length<this.totalNum){//未加载完毕
					this.page++;
					this.getMoreInitMsg()
				}else{
					
				}
			},
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
				padding: 5rpx;
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
				padding: 10rpx;
			}
		}
		.navAreaKong{
			width: 100%;
			height: 88rpx;
		}
		.recordArea{
			width: 100%;
			.per{
				background: #fff;
				height: 140rpx;
				width: 100%;
				display: flex;
				justify-content: space-between;
				align-items: center;
				.logo{
					width: 100rpx;
					height: 100rpx;
					border-radius: 50%;
					margin-left: 50rpx;
					background: #F6F6F6;
				}
				.textBox{
					margin-left: 20rpx;
					width: 300rpx;
					height: 100rpx;
					.name{
						height: 50rpx;
						font-size: 28rpx;
						font-family: PingFang SC;
						font-weight: bold;
						color: #333333;
						line-height: 50rpx;
						overflow: hidden;
						text-overflow:ellipsis;
						white-space: nowrap;
					}
					.id{
						height: 50rpx;
						font-size: 24rpx;
						font-family: PingFang SC;
						font-weight: 500;
						color: #A6A6A6;
						line-height: 50rpx;
					}
				}
				.senson{
					width: 220rpx;
					margin-right: 50rpx;
					font-size: 22rpx;
					font-family: PingFang SC;
					font-weight: 500;
					color: #A6A6A6;
					text-align: right;
				}
			}
		}
	}
}
</style>
