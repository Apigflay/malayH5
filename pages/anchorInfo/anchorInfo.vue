<template>
	<view class="content">
		<view class="anchorInfoArea">
			<view class="nameArea">
				<view class="photo">
					<image class="pho" src="" mode=""></image>
					<text class="na">撒旦发射点发生</text>
				</view>
				<view class="intro">撒旦发射点发射点发生</view>
			</view>
			<view class="fansArea">
				{{this.Language.language[language].language45}}
			</view>
		</view>
		<!--  -->
		<view class="listArea">
			<view class="per per1">
				<image class="img" src="../../static/imgs/gift1.png" mode=""></image>
				<view class="p">{{this.Language.language[language].language42}}</view>
			</view>
			<view class="per">
				<image class="img"  src="../../static/imgs/gift2.png" mode=""></image>
				<view class="p">{{this.Language.language[language].language43}}</view>
			</view>
			<view class="per per3">
				<image class="img"  src="../../static/imgs/gift3.png" mode=""></image>
				<view class="p">{{this.Language.language[language].language44}}</view>
			</view>
		</view>
		<!-- btn -->
		<view class="fansBtn">{{this.Language.language[language].language41}}</view>

	</view>
</template>

<script>
	import {encrypt,decrypt,encryptIos,decryptIos,base64ToArrayBuffer,sendData} from "../../lib/js/GlobalFunction.js"
	export default {
		data() {
			return {
				language:0,//0 简 1繁 2 en 3 ms
				userIdx:null,//userIdx
				token:null,//token no
				params:null,
				
			}
		},
		onLoad(option) {
			
			this.GetQueryString('token')
			this.getInitLunguage()
			
			// this.getInitMsg()
		},
		methods: {
			GetQueryString:function(name) { //name为要获得的参数名字 
				var that = this;
				  var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)")
				  var r = window.location.hash.split('?')[1].match(reg)
				  if(r != null){
					  that.params=decodeURIComponent(r[2].replace(/%20/g,'%2B'));
					  var str = decryptIos(decodeURIComponent(r[2].replace(/%20/g,'%2B')));
					  // console.log(str)
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
			
			
		}
	}
</script>

<style lang="scss">
page{
	width: 100%;
	height: 100%;
	// background: #F6F6F6;
	// background: #fff;
}
.content {
	width: 100%;
	height: 100%;
	// background: #F6F6F6;
	.anchorInfoArea{
		height: 160rpx;
		width: 100%;
		border-radius: 20rpx 20rpx 0px 0px;
		display: flex;
		justify-content: space-between;
		align-items: center;
		.nameArea{
			max-width: 400rpx;
			height: 80rpx;
			margin-left: 40rpx;
			.photo{
				height: 48rpx;
				max-width: 400rpx;
				display: flex;
				align-items: center;
				.pho{
					height: 48rpx;
					width: 48rpx;
					border-radius: 50%;
					background: #cecece;
				}
				.na{
					max-width: 344rpx;
					font-size: 30rpx;
					font-family: PingFang SC;
					font-weight: bold;
					overflow: hidden;
					text-overflow:ellipsis;
					white-space: nowrap;
					margin-left: 8rpx;
				}
			}
			.intro{
				margin-top: 12rpx;
				height: 32rpx;
				line-height: 24rpx;
				font-size: 24rpx;
				font-family: PingFang SC;
				font-weight: 500;
				color: #7D7D7D;
				overflow: hidden;
				text-overflow:ellipsis;
				white-space: nowrap;
			}
		}
		.fansArea{
			background: #FFCB25;
			font-size: 28rpx;
			padding: 10rpx 20rpx;
			border-radius: 6rpx;
			margin-right: 40rpx;
		}
	}
	.listArea{
		display: flex;
		align-items: center;
		justify-content: space-between;
		margin-bottom: 40rpx;
		.per{
			width: 148rpx;
			.img{
				width: 148rpx;
				height: 148rpx;
			}
			.p{	
				height: 92rpx;
				font-size: 20rpx;
				line-height: 46rpx;
				text-align: center;
			}
		}
		.per1{
			margin-left: 80rpx;
		}
		.per3{
			margin-right: 80rpx;
		}
	}
	.fansBtn{
		width: 600rpx;
		height: 80rpx;
		background: #FFCB25;
		box-shadow: 0px 6rpx 12rpx 0px rgba(0, 0, 0, 0.2);
		border-radius: 8rpx;
		margin: auto;
		font-size: 30rpx;
		line-height: 80rpx;
		text-align: center;
	}

}
</style>
