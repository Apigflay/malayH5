<template>
	<view class="content">
		<!-- nav -->
		<view class="navArea">
			<image class="back" src="/static/imgs/back.png" mode=""></image>
			<text class="text">{{this.Language.language[0].language1}}</text>
			<image class="more" src="/static/imgs/more.png" mode=""></image>
		</view>
		<!-- 家族信息 -->
		<view class="familyArea">
			
		</view>
		<view class="Tip">
			
		</view>
		<!-- 申请记录 -->
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onLoad() {

		},
		methods: {

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
	.familyArea{
		height: 140rpx;
		width: 100%;
		background: #fff;
	}
	
}
</style>
