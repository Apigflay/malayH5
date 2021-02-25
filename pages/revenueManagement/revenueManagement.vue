<template>
	<view class="content">
		<!-- nav -->
		<view class="navArea">
			<image class="back" src="/static/imgs/back.png" @click="goBackPages" mode=""></image>
			<text class="text">{{this.Language.language[language].language28}}</text>
			<text class="more"></text>
			<!-- <image class="more" src="/static/imgs/more.png" mode=""></image> -->
		</view>
		<!-- 收益管理 -->
		<view class="revenueArea">
			<view class="per">
				<text class="name">{{this.Language.language[language].language29}}</text>
				<view class="info" @click="goInfoPage(0)">
					<text class="num">{{babyNum}}</text>
					<image class="img" src="/static/imgs/more1.png" mode=""></image>
				</view>
			</view>
			<view class="per">
				<text class="name">{{this.Language.language[language].language30}}</text>
				<view class="info" @click="goInfoPage(1)">
					<text class="num">{{flowingWaterNum}}</text>
					<image class="img" src="/static/imgs/more1.png" mode=""></image>
				</view>
			</view>
			<view class="per">
				<text class="name">{{this.Language.language[language].language31}}</text>
				<view class="info" @click="goInfoPage(2)">
					<text class="num">{{durationNum}}</text>
					<image class="img" src="/static/imgs/more1.png" mode=""></image>
				</view>
			</view>
		</view>
		
	</view>
</template>

<script>
	import {encrypt,decrypt,encryptIos,decryptIos,productType,base64ToArrayBuffer,sendData} from "../../lib/js/GlobalFunction.js"
	export default {
		data() {
			return {
				language:0,//
				userIdx:null,//主播使用
				userIdx1:null,//家族长页面跳转过来
				token:null,
				params:null,
				babyNum:0,//宝宝
				flowingWaterNum:0,//流水
				durationNum:0,//时长
				backId:0,//默认0 表示直接打开的页面 返回点击 关闭 1从其他跳过来的
			}
		},
		onLoad(option) {
			this.GetQueryString('token')
			console.log(option.backid)
			console.log(option.useridx)
			if(option.backid==1){
				this.backId=option.backid;
				this.userIdx1=option.useridx;
			}
			// this.userId=option.userIdx;
			this.getInitLunguage()
			this.getInitMsg()
			
		},
		methods: {
			goBackPages:function(){//0--ios 1--android
				console.log('back')
				console.log(productType())
				if(this.backId==0){
					if(productType()==0){
						// GoBackAPP()
						window.location.href='GoBackAPP'
					}else if(productType()==1){
						// android.goBackAPP()
						window.android.goBackAPP()
					}
				}else if(this.backId==1){
					uni.navigateBack({
					    delta: 1
					});
				}
				
				
				
			},
			GetQueryString:function(name) { //name为要获得的参数名字 
			// var a1 = 'useridx=76658373|token=0c5934b1e14fdbc8314e90e499873181|from=ioshotad|index=1';
			// var a2 = 'useridx=60037961|token=0c5934b1e14fdbc8314e90e499873181|from=ioshotad|index=1';
			// console.log(encryptIos(a1))
			// console.log(encryptIos(a2))
			
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
			goInfoPage:function(pageId){//pageId 0 宝宝 1 流水 2 时长 
				if(this.backId==0){
					var urlStr='?pageId='+pageId+'&language='+this.language+'&userIdx='+this.userIdx+'&params='+encodeURIComponent(this.params);
					// console.log(urlStr)
					uni.navigateTo({
						url: '/pages/detailed/detailed'+urlStr
					});	
				}else if(this.backId==1){
					var urlStr='?pageId='+pageId+'&language='+this.language+'&userIdx='+this.userIdx1+'&params='+encodeURIComponent(this.params);
					// console.log(urlStr)
					uni.navigateTo({
						url: '/pages/detailed/detailed'+urlStr
					});	
				}
				
			},
			getInitMsg:function(){
				if(this.backId==0){
					uni.request({
						url: this.GLOBAL.urlPoint+'family/getProfitList', //仅为示例，并非真实接口地址。
						method:"GET",
						data: {
							userIdx: this.userIdx,
							oper:1,//1-总流水 2本周 3-上周
							token:this.params,
						},
						success: (res) => {
							// console.log(res.data);
							this.babyNum=res.data.data[0].number;//宝宝
							this.flowingWaterNum=res.data.data[0].allnums;//流水
							this.durationNum=res.data.data[0].onlineTime;//时长
						}
					});
				}else if(this.backId==1){
					uni.request({
						url: this.GLOBAL.urlPoint+'family/getProfitList', //仅为示例，并非真实接口地址。
						method:"GET",
						data: {
							userIdx: this.userIdx1,
							oper:1,//1-总流水 2本周 3-上周
							token:this.params,
						},
						success: (res) => {
							// console.log(res.data);
							this.babyNum=res.data.data[0].number;//宝宝
							this.flowingWaterNum=res.data.data[0].allnums;//流水
							this.durationNum=res.data.data[0].onlineTime;//时长
						}
					});
				}
				
			},
		}
	}
</script>

<style lang="scss">
page{
	width: 100%;
	height: 100%;
	background: #F6F6F6;
}
.content {
	width: 100%;
	height: 100%;
	.navArea{
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
	.revenueArea{
		width: 100%;
		background: #fff;
		.per{
			height: 100rpx;
			display: flex;
			align-items: center;
			justify-content: space-between;
			.name{
				margin-left: 54rpx;
				font-size: 36rpx;
				font-family: PingFang SC;
				font-weight: 600;
				color: #333333;
			}
			.info{
				display: flex;
				align-items: center;
				justify-content: center;
				margin-right: 41rpx;
				.num{
					color: #767676;
					font-size: 36rpx;
				}
				.img{
					height: 36rpx;
					width: 36rpx;
				}
			}
		}
	}
	
}
</style>
