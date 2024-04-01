<template>
	<view class="_center">
		<view :class="value?'choose_select radio_r':'_radio' " v-if="type=='radio'"></view>
		<view v-if="type=='multiple'" :class="value?'choose_select':'_multiple'"></view>
		<view v-if="type=='half'" :class="value?'half_select':'_multiple'"></view>
	</view>
</template>

<script>
	export default {
		props: {
			value: {
				type: [Boolean, String],
				default: false
			},
			/* 
			 radio 单选
			 multiple 多选
			 half 半选
			 */
			type: {
				type: String,
				default: 'multiple'
			}
		}
	}
</script>

<style scoped lang="scss">
	$them:#007AFF;
	._center{
		display: flex;
		align-items: center;
	}
	._radio {
		width: 30rpx;
		height: 30rpx;
		border-radius: 50%;
		border: $them 1rpx solid;
		
	}
	.radio_r{
		border-radius: 50%;
	}
	._multiple {
		width: 30rpx;
		height: 30rpx;
		border: $them 1rpx solid;
	}
	.choose_select {
		width: 30rpx;
		height: 30rpx;
		line-height: 30rpx;
		border: 1rpx solid $them;
		background-color: $them;
		position: relative;
		cursor: pointer;
		&::before {
			position: absolute;
			content: " ";
			width: 9rpx;
			height: 14rpx;
			transform: rotate(45deg);
			border-right: 4rpx solid #fff;
			border-bottom: 4rpx solid #fff;
			top: 4rpx;
			left: 10rpx;
		}
	}
	.half_select{
		width: 30rpx;
		height: 30rpx;
		line-height: 30rpx;
		border: 1rpx solid $them;
		background-color: $them;
		position: relative;
		cursor: pointer;
		&::before {
			position: absolute;
			content: " ";
			width: 20rpx;
			height: 4rpx;
			background-color: #fff;
			top: 13rpx;
			left: 5rpx;
		}
	}
</style>
