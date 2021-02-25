<template>
	<view class="content">
		<scroll-view scroll-y="true" @scrolltolower="getScrollmsg" class="scrollView">
		<!-- nav -->
		<view class="navArea">
			<image class="back" @click="goBackPages" src="/static/imgs/back.png" mode=""></image>
			<text class="text">{{this.Language.language[language].language1}}</text>
			<image v-if="swichBarStatus==1" class="more" @click="goFandFamily" src="/static/imgs/more.png" mode=""></image>
			<text class="more" v-else></text>
		</view>
		<view class="navAreaKong">
			
		</view>
		<!-- swich bar -->
		<view class="swichBarArea">
			<text @click="changeSwichBar(0)" class="ahchorB" :class="swichBarStatus==0?'activeBar':'nomalBar'">{{this.Language.language[language].language10}}</text>
			<text @click="changeSwichBar(1)" class="familyB" :class="swichBarStatus==1?'activeBar':'nomalBar'">{{this.Language.language[language].language11}}</text>
		</view>
		<!-- 申请记录 -->
		<view class="recordArea" v-if="swichBarStatus==0">
			<view class="per" v-for="(item,index) in initData2" :key="index">
				<image class="logo" :src="item.smallpic" mode=""></image>
				<view class="textBox">
					<p class="name">{{item.myname}}</p>
					<p class="id">ID:{{item.userIdx}}</p>
				</view>
				<view class="btnArea">
					<button @click="processingUserRequests(0,item.userIdx)" v-if="item.typeId==0" class="sureBtn" type="primary">{{btnText[0][language]}}</button>
					<button @click="processingUserRequests(1,item.userIdx)" v-if="item.typeId==0" class="noBtn" type="primary">{{btnText[1][language]}}</button>
					<button @click="processingUserRequests(2,item.userIdx)" v-if="item.typeId==1" class="delBtn" type="primary">{{btnText[2][language]}}</button>
				</view>
			</view>
		</view>
		<!-- 场控列表 -->
		<view class="fieldControlArea" v-if="swichBarStatus==1">
			<view class="per" v-for="(item,index) in initData1" :key="index">
				<image class="logo" :src="item.smallpic" mode=""></image>
				<view class="textBox">
					<p class="name">{{item.myname}}</p>
					<p class="id">ID:{{item.userIdx}}</p>
				</view>
				<view class="btnArea">
					<button @click="delFileControl(3,item.userIdx)" class="delBtn" type="primary">{{btnText[2][language]}}</button>
				</view>
			</view>
		</view>
		<!-- 遮罩层及其弹框 -->
		<view class="masker" v-if="marskStatus" @click="closePop"></view>
		<view class="pop" v-if="marskStatus">
			<p class="title">{{this.Language.language[language].language15}}</p>
			<view class="inputArea">
				<text class="text">ID</text>
				<input v-model="toUserIdx" class="input" type="number" value="" :placeholder="this.Language.language[language].language16" />
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
				initData1:null,//初始数据 场控列表
				initData2:null,//初始数据 申请记录
				isState:0,//1-存在家族 0不存在家族
				page:1,//默认页数
				shenhetext:[['申请加入中','申請加入中','Applying','Sedang memohon'],['已通过审核','已通過審核','Verified','Diluluskan'],['未通过审核','未通過審核','Not verified','Belum dilulus']],
				marskStatus:false,//
				toUserIdx:'',
				swichBarStatus:0,//当前激活的swichbar
				AchorBtnStatus:0,//0待审核   1已通过   
				btnText:[['同意','同意','Agree','Setuju'],['拒绝','拒絕','Reject','Tolak'],['移除','移除','Remove','Keluarkan']],
				tipText:'',//tips
				totalNum:0,//初始主播数据
				
			}
		},
		onLoad(option) {
			// this.userIdx=option.userIdx;
			this.GetQueryString('token')
			this.getInitLunguage()
			this.getInitMsg()
			// uni.startPullDownRefresh();
		},
		onPullDownRefresh() {
		        console.log('refresh');
				console.log(this.swichBarStatus)
				if(this.swichBarStatus==0){
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
							this.initData2=res.data.data.list;
							this.totalNum=res.data.data.count;
							
						},
						complete: (com) => {
							uni.stopPullDownRefresh();
						}
					});
				}else if(this.swichBarStatus==1){
					uni.request({
						url: this.GLOBAL.urlPoint+'family/GetFieldControlList', //仅为示例，并非真实接口地址。
						method:"GET",
						data: {
							familyUserIdx:this.userIdx,
							token:this.params,
						},
						success: (res) => {
							// console.log(res.data.data);
							this.initData1=res.data.data;
						},
						complete: (com) => {
							uni.stopPullDownRefresh();
						}
					});
				}
				
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
			  // console.log(lang)
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
			changeSwichBar:function(id){
				this.swichBarStatus=id;
				if(id==1){
					this.getControlMsg()
				}
			},
			getControlMsg:function(){//获取场控列表
				uni.request({
					url: this.GLOBAL.urlPoint+'family/GetFieldControlList', //仅为示例，并非真实接口地址。
					method:"GET",
					data: {
						familyUserIdx:this.userIdx,
						token:this.params,
					},
					success: (res) => {
						// console.log(res.data.data);
						this.initData1=res.data.data;
					}
				});
			},
			processingUserRequests:function(typeId,userIdx){//0 同意 1-拒绝 2 移除  
				// console.log(typeId,userIdx,this.userIdx)
				var that = this;
				var tipText1=['提示','提示','Tips','Tip'];
				var tipText2=['移除此人','移除此人','Remove this person','Buang orang ini'];
				if(typeId==2){
					uni.showModal({
					    title: tipText1[that.language],
					    content: tipText2[that.language],
					    success: function (res) {
					        if (res.confirm) {
					            var array=base64ToArrayBuffer(encrypt(JSON.stringify({
					            	type:typeId,
					            	userIdx:userIdx,
					            	familyUserIdx:Number(that.userIdx),
					            })))
					            var res = JSON.parse(sendData('POST',that.GLOBAL.urlPoint+'family/SetFamilyOpertion',array));
					            // console.log(res)
					            if(res.code==100){
					            	that.page=1;
					            	that.getInitMsg()
					            	uni.showToast({
					            		title: that.Language.language[that.language].language22,
					            		duration: 2000,
					            		icon:'success',
					            	});
					            }else if(res.code==10002){
					            	uni.showToast({
					            		title: that.Language.language[that.language].language23,
					            		duration: 2000,
					            		icon:'none',
					            	});
					            	that.page=1;
					            	that.getInitMsg()
					            }else{
					            	uni.showToast({
					            		title: that.Language.language[that.language].language24,
					            		duration: 2000,
					            		icon:'none',
					            	});
					            }
					        } else if (res.cancel) {
					            console.log('用户点击取消');
					        }
					    }
					});
				}else{
					var array=base64ToArrayBuffer(encrypt(JSON.stringify({
						type:typeId,
						userIdx:userIdx,
						familyUserIdx:Number(this.userIdx),
					})))
					var res = JSON.parse(sendData('POST',this.GLOBAL.urlPoint+'family/SetFamilyOpertion',array));
					// console.log(res)
					if(res.code==100){
						this.page=1;
						this.getInitMsg()
						uni.showToast({
							title: this.Language.language[this.language].language22,
							duration: 2000,
							icon:'success',
						});
					}else if(res.code==10002){
						uni.showToast({
							title: this.Language.language[this.language].language23,
							duration: 2000,
							icon:'none',
						});
						this.page=1;
						this.getInitMsg()
					}else{
						uni.showToast({
							title: this.Language.language[this.language].language24,
							duration: 2000,
							icon:'none',
						});
					}
				}
			},
			delFileControl:function(id,userIdx){//3 场控移除 
				var that = this;
				var tipText1=['提示','提示','Tips','Tip'];
				var tipText2=['移除此人','移除此人','Remove this person','Buang orang ini'];
				uni.showModal({
				    title: tipText1[that.language],
				    content: tipText2[that.language],
				    success: function (res) {
				        if (res.confirm) {
							console.log(that)
				            console.log(id,userIdx)
				            var array=base64ToArrayBuffer(encrypt(JSON.stringify({
				            	type:1,//移除
				            	userIdx:userIdx,
				            	familyUserIdx:Number(that.userIdx),
				            })))
				            console.log(array)
				            var res = JSON.parse(sendData('POST',that.GLOBAL.urlPoint+'family/FieldControlListManage',array));
				            console.log(res)
				            if(res.code==100){//成功
				            	that.getControlMsg()
				            	uni.showToast({
				            		title: that.Language.language[that.language].language22,
				            		duration: 2000,
				            		icon:'success',
				            	});
				            }else if(res.code==10004){//该ID已是场控
				            	uni.showToast({
				            		title: that.Language.language[that.language].language25,
				            		duration: 2000,
				            		icon:'none',
				            	});
				            }else if(res.code==10003){//当前ID不是本家族
				            	uni.showToast({
				            		title: that.Language.language[that.language].language26,
				            		duration: 2000,
				            		icon:'none',
				            	});
				            }else if(res.code==10002){//场控最多4个!
				            	uni.showToast({
				            		title: that.Language.language[that.language].language27,
				            		duration: 2000,
				            		icon:'none',
				            	});
								
				            }else if(res.code==101){
								var testSt=['ID错误','ID錯誤','ID error','Ralat ID'];
								uni.showToast({
									title: testSt[this.language],
									duration: 2000,
									icon:'none',
								});
							}else if(res.code==10005){
								var testSt11=['已是家族长','已是家族長','He is already the head of the family','Dia sudah ketua keluarga'];
								uni.showToast({
									title: testSt11[this.language],
									duration: 2000,
									icon:'none',
								});
							}else{//操作失败
								uni.showToast({
									title: res.msg,
									duration: 2000,
									icon:'none',
								});
				            }
				        } else if (res.cancel) {
				            console.log('用户点击取消');
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
						this.initData2=res.data.data.list;
						this.totalNum=res.data.data.count;
						
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
						// console.log(res.data);
						this.initData2.push.apply(this.initData2,res.data.data.list);	
					}
				});
			},
			goFandFamily:function(){
				if(this.swichBarStatus==0){//为主播不做处理
					
				}else{
					this.marskStatus=true;
				}
				
			},
			closePop:function(){
				this.marskStatus=false;
				this.toUserIdx='';
				this.tipText='';
			},
			goSearchFamily:function(){//添加场控
				this.tipText='';
				var array=base64ToArrayBuffer(encrypt(JSON.stringify({
					type:0,//0添加 1 移除
					userIdx:this.toUserIdx,//用户id 即输入id
					familyUserIdx: this.userIdx,
				})))
				var res = JSON.parse(sendData('POST',this.GLOBAL.urlPoint+'family/FieldControlListManage',array));
				console.log(res)
				if(res.code==100){//成功
					uni.showToast({
						title: this.Language.language[this.language].language22,
						duration: 2000,
						icon:'success',
					});
					this.marskStatus=false;
					this.getControlMsg()
				}else if(res.code==10004){//该ID已是场控
					this.tipText=this.Language.language[this.language].language25;
				}else if(res.code==10003){//当前ID不是本家族
					this.tipText=this.Language.language[this.language].language26;
				}else if(res.code==10002){//场控最多4个!
					this.tipText=this.Language.language[this.language].language27;
				}else if(res.code==101){
					var testSt=['ID错误','ID錯誤','ID error','Ralat ID'];
					uni.showToast({
						title: testSt[this.language],
						duration: 2000,
						icon:'none',
					});
				}else if(res.code==10005){
					var testSt11=['已是家族长','已是家族長','He is already the head of the family','Dia sudah ketua keluarga'];
					uni.showToast({
						title: testSt11[this.language],
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
		.navArea{//navbar
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
		.navAreaKong{//空 头
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
		.recordArea{//申请记录
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
					width: 270rpx;
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
				.btnArea{
					width: 240rpx;
					height: 52rpx;
					margin-right: 46rpx;
					display: flex;
					align-items: center;
					justify-content: flex-end;
					.sureBtn{
						width: 110rpx;
						height: 52rpx;
						background: #FFCB25;
						border-radius: 10rpx;
						font-size: 24rpx;
						font-family: PingFang SC;
						font-weight: 500;
						color: #000000;
						text-align: center;
						line-height: 52rpx;
					}
					.noBtn{
						width: 110rpx;
						height: 52rpx;
						background: #DFDFDF;
						border-radius: 10rpx;
						font-size: 24rpx;
						font-family: PingFang SC;
						font-weight: 500;
						color: #000000;
						text-align: center;
						line-height: 52rpx;
					}
					.delBtn{
						width: 110rpx;
						height: 52rpx;
						background: #DFDFDF;
						border-radius: 10rpx;
						font-size: 24rpx;
						font-family: PingFang SC;
						font-weight: 500;
						color: #000000;
						text-align: center;
						line-height: 52rpx;
					}
				}
			}
		}
		.fieldControlArea{//场控
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
					width: 270rpx;
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
				.btnArea{
					width: 240rpx;
					height: 52rpx;
					margin-right: 46rpx;
					display: flex;
					align-items: center;
					justify-content: flex-end;
					.sureBtn{
						width: 110rpx;
						height: 52rpx;
						background: #FFCB25;
						border-radius: 10rpx;
						font-size: 24rpx;
						font-family: PingFang SC;
						font-weight: 500;
						color: #000000;
						text-align: center;
						line-height: 52rpx;
					}
					.noBtn{
						width: 110rpx;
						height: 52rpx;
						background: #DFDFDF;
						border-radius: 10rpx;
						font-size: 24rpx;
						font-family: PingFang SC;
						font-weight: 500;
						color: #000000;
						text-align: center;
						line-height: 52rpx;
					}
					.delBtn{
						width: 110rpx;
						height: 52rpx;
						background: #DFDFDF;
						border-radius: 10rpx;
						font-size: 24rpx;
						font-family: PingFang SC;
						font-weight: 500;
						color: #000000;
						text-align: center;
						line-height: 52rpx;
					}
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
