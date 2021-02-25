<template>
	<view class="content">
		<scroll-view scroll-y="true"  class="scrollView"><!-- @scrolltolower="getScrollmsg" -->
		<!-- nav -->
		<view class="navArea">
			<image class="back" @click="goBackPages" src="/static/imgs/back.png" mode=""></image>
			<text class="text">{{this.Language.language[language].language36}}</text>
			<!-- <image class="more" src="/static/imgs/more.png" mode=""></image> -->
			<text class="more"></text>
		</view>
		<view class="navAreaKong"></view>
		<view class="titleArea">
			<text>{{this.Language.language[language].language37}}</text>
			<text>{{this.Language.language[language].language38}}</text>
			<text>{{this.Language.language[language].language39}}</text>
		</view>
		<view class="ListArea">
			<view class="per" v-for="(item,index) in 20" :key="index">
				<text class="indexNum">1</text>
				<text class="name">哈哈哈</text>
				<text class="leval">88级</text>
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
				language:0,//0 简 1繁 2 en 3 ms
				userIdx:null,//userIdx
				pageId:'',//0 宝宝 1 流水 2 时长
				params:null,
				
				token:null,//token----no
				swichBarStatus:0,//当前激活的swichbar
				dateText:['日期','','',''],
				numText:['数量','','',''],
				tootleText:['总计','','',''],
				
			}
		},
		onLoad() {
			this.GetQueryString('token')
			this.getInitLunguage()
			
			this.getInit()
		},
		methods: {
			goBackPages:function(){//0--ios 1--android
				console.log('back')
				console.log(productType())
				if(productType()==0){
					// GoBackAPP()
					window.location.href='GoBackAPP'
				}else if(productType()==1){
					android.goBackAPP()
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
			changeSwichBar:function(id){
				this.swichBarStatus=id;
				if(id==1){
					// this.getControlMsg()
				}
			},
			getInit:function(){
				// var str = token=NdQjSQPrQxN/oQjHqamwYTR/Sz5xcJNb11Pz+wQCBzmLTXAbzr1ArUD4ABrvap20kNqNkALRxGyTVNShZ82LbC+3AIskfSR4TId38EEw/hM=
			}
		}
	}
</script>

<style lang="scss">
page{
	width: 100%;
	height: 100%;
}
.content {
	width: 100%;
	height: 100%;
	background: #fff;
	.scrollView{
		width: 100%;
		height: 100%;
		.navArea{
			border-radius: 20rpx 20rpx 0px 0px;
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
				padding: 5rpx;
			}
		}
		.navAreaKong{
			width: 100%;
			height: 88rpx;
		}
		.titleArea{
			width: 650rpx;
			padding: 0rpx 50rpx;
			display: flex;
			justify-content: space-between;
			align-items: center;
			height: 32rpx;
			background: #F6F6F6;
			font-size: 20rpx;
			font-family: PingFang SC;
			font-weight: 500;
			color: #A6A6A6;
		}
		.ListArea{
			width: 100%;
			overflow: scroll;
			.per{
				height: 80rpx;
				width: 100%;
				display: flex;
				align-items: center;
				justify-content: space-between;
				font-size: 26rpx;
				font-family: PingFang SC;
				font-weight: bold;
				color: #000000;
				.indexNum{
					margin-left: 75rpx;
				}
				.name{
					max-width: 250rpx;
					overflow: hidden;
					text-overflow:ellipsis;
					white-space: nowrap;
				}
				.leval{
					margin-right: 75rpx;
				}
			}
			
		}
	}
}
</style>
