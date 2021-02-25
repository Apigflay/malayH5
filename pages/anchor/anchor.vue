<template>
	<view class="content">
		<scroll-view scroll-y="true" @scrolltolower="getScrollmsg" class="scrollView">
		<!-- nav -->
		<view class="navArea">
			<image class="back" @click="goBackPages" src="/static/imgs/back.png" mode=""></image>
			<text class="text">{{this.Language.language[language].language1}}</text>
			<image class="more" @click="goFandFamily" src="/static/imgs/more.png" mode=""></image>
		</view>
		<view class="navAreaKong">
			
		</view>
		<!-- 家族信息 -->
		<view class="familyArea">
			<view class="noFamily" v-if="isState==0">
				{{this.Language.language[language].language3}}
			</view>
			<view class="hadFamily" v-if="isState==1&&initData1.length!=0">
				<image class="logo" :src="initData1[0].smallpic" mode=""></image>
				<view class="textBox">
					<p class="name">{{initData1[0].familyName}}</p>
					<p class="id">ID:{{initData1[0].familyUserIdx}}</p>
				</view>
			</view>
		</view>
		<!-- tips -->
		<view class="Tip">{{this.Language.language[language].language2}}
		</view>
		<!-- 申请记录 -->
		<view class="recordArea" >
			<view class="per" v-for="(item,index) in initData2" :key="index">
				<image class="logo" :src="item.smallpic" mode=""></image>
				<view class="textBox">
					<p class="name">{{item.familyName}}</p>
					<p class="id">ID:{{item.familyUserIdx}}</p>
				</view>
				<view class="senson" v-if="item.state==0">
					{{shenhetext[0][language]}}
				</view>
				<view class="senson" v-if="item.state==1">
					{{shenhetext[1][language]}}
				</view>
				<view class="senson" v-if="item.state==2">
					{{shenhetext[2][language]}}
				</view>
			</view>
		</view>
		<!-- 遮罩层及其弹框 -->
		<view class="masker" v-if="marskStatus" @click="closePop"></view>
		<view class="pop" v-if="marskStatus">
			<p class="title">{{this.Language.language[language].language7}}</p>
			<view class="inputArea">
				<text class="text">ID</text>
				<input v-model.trim="toUserIdx" class="input" type="number" value="" :placeholder="this.Language.language[language].language8" />
			</view>
			<p class="redTip">{{tipText}}</p>
			<button @click="goSearchFamily" class="btnSure" type="primary">{{this.Language.language[language].language9}}</button>
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
				initData2:[],//初始数据 申请记录
				isState:2,//1-存在家族 0不存在家族
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
		onPullDownRefresh() {
		        console.log('refresh');
				console.log(this.initData2)
				this.$nextTick(function(){
					console.log('im')
					this.initData2=[];
				})
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
						console.log(this)
						if(res.data.data.isState==0){//不存在
							this.isState=0;
							this.initData2=res.data.data.list;
							this.totalNum=res.data.data.count;
						}else{//存在
							this.isState=1;
							this.initData1=res.data.data.list;
							this.initData2=[];
						}
						
					},
					complete: (com) => {
						uni.stopPullDownRefresh();
					}
				});
		        // setTimeout(function () {
		        //     uni.stopPullDownRefresh();
		        // }, 1000);
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
			GetQueryString:function(name){ //name为要获得的参数名字 
				var that = this;
				  var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)")
				  var r = window.location.hash.split('?')[1].match(reg)
				  if(r != null){
					  that.params=decodeURIComponent(r[2].replace(/%20/g,'%2B'));
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
			goFandFamily:function(){
				this.marskStatus=true;
			},
			closePop:function(){
				this.marskStatus=false;
				this.toUserIdx='';
				this.tipText='';
			},
			goSearchFamily:function(){
				this.tipText='';
				if(this.toUserIdx==''){
					uni.showToast({
						title: this.Language.language[this.language].language16,
						duration: 2000,
						icon:'none',
					});
				}else{
					var array=base64ToArrayBuffer(encrypt(JSON.stringify({
						userIdx:this.userIdx,
						familyUserIdx:this.toUserIdx,
					})))
					
					var res = JSON.parse(sendData('POST',this.GLOBAL.urlPoint+'family/SetFamilyApply',array));
					console.log(res)
					if(res.code==100){//成功
						uni.showToast({
							title: this.Language.language[this.language].language17,
							duration: 2000,
							icon:'success',
						});
						this.marskStatus=false;
						this.toUserIdx='';
						this.tipText='';
						this.getInitMsg()
					}else if(res.code==10002){//已存在家族
						this.tipText=this.Language.language[this.language].language18;
					}else if(res.code==10003){//该家族不存在
						this.tipText=this.Language.language[this.language].language19;
					}else if(res.code==10004){//请勿重复提交
						this.tipText=this.Language.language[this.language].language20;
					}else if(res.code==101){
						var testSt=['ID错误','ID錯誤','ID error','Ralat ID'];
						uni.showToast({
							title: testSt[this.language],
							duration: 2000,
							icon:'none',
						});
					}else{//失败
						uni.showToast({
							title: res.msg,
							duration: 2000,
							icon:'none',
						});
					}
				}
				
				
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
		.familyArea{
			height: 140rpx;
			width: 100%;
			background: #fff;
			.noFamily{
				height: 140rpx;
				font-size: 28rpx;
				font-weight: bold;
				color: #333333;
				text-align: center;
				line-height: 140rpx;
			}
			.hadFamily{
				height: 140rpx;
				width: 100%;
				display: flex;
				justify-content: center;
				align-items: center;
				.logo{
					width: 100rpx;
					height: 100rpx;
					border-radius: 50%;
					background: #f6f6f6;
				}
				.textBox{
					margin-left: 20rpx;
					width: 530rpx;
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
			}
		}
		.Tip{
			text-indent: 30rpx;
			height: 55rpx;
			background: #F6F6F6;
			line-height: 55rpx;	
			font-size: 24rpx;
			font-family: PingFang SC;
			font-weight: 500;
			color: #A6A6A6;
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
		.masker{
			position: fixed;
			width: 100%;
			height: 100%;
			background: rgba(0,0,0,0.5);
			z-index: 900;
			top: 0;
		}
		.pop{
			position: fixed;
			top: 0;
			left: 0;
			right: 0;
			bottom: 0;
			margin: auto;
			width: 550rpx;
			height: 402rpx;
			background: #FFFFFF;
			border-radius: 8rpx;
			z-index: 901;
			.title{
				font-size: 32rpx;
				font-family: PingFang SC;
				font-weight: bold;
				color: #000000;
				line-height: 58rpx;
				text-align: center;
				padding: 46rpx 0 32rpx 0;
			}
			.inputArea{
				display: flex;
				justify-content: center;
				align-items: center;
				.text{
					font-size: 32rpx;
					font-weight: bold;
					color: #000000;
					margin-right: 24rpx;
				}
				.input{
					width: 349rpx;
					height: 69rpx;
					background: #F0F0F0;
					border: 2rpx solid #BBBBBB;
					border-radius: 10rpx;
					font-size: 32rpx;
					text-indent: 10rpx;
				}
			}
			.redTip{
				height: 44rpx;
				font-size: 24rpx;
				line-height:44rpx;
				font-weight: 500;
				color: #FF0000;
				margin-bottom: 40rpx;
				text-align: center;
			}
			.btnSure{
				width: 440rpx;
				height: 80rpx;
				background: #FFCB25;
				box-shadow: 0px 6rpx 12rpx 0px rgba(0, 0, 0, 0.2);
				border-radius: 8rpx;
				font-size: 32rpx;
				font-family: PingFang SC;
				font-weight: 500;
				color: #000000;
				line-height: 80rpx;
			}
		}
	}
}
</style>
