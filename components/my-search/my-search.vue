<template>
	<view  class="my-search-container"  :style="{'background-color':backgroundcolor}" @click="Search">
		<view class="my-search-box" :style="{'border-radius':radius+'px'}">
			<!-- 使用uni-ui提供的图标组件 -->
			<uni-icons type="search" size="17"></uni-icons>
			<text class="find">搜索</text>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
		  // 背景颜色
		  backgroundcolor: {
		    type: String,
		    default: ''
		  },
		  // 圆角尺寸
		  radius: {
		    type: Number,
		    // 单位是 px
		    default: 15
		  }
		},
		data() {
			return {
				
			};
		},
		methods: {
			Search() {
				this.$emit('click')
			}
		},
		
	}
</script>

<style lang="scss">
.my-search-container{
	height: 50px;
	// background-color: #00ffff;
	display: flex;
	padding: 0 10px;
	align-items: center;
	
	.my-search-box{
		height: 36px;
		background-color:#FFFFFF;
		// border-radius: 15px;
		display: flex;
		justify-content: center;
		align-items: center;
		width: 100%;
		.find{
			font-size: 15px;
			margin-left: 5px;
		}
	}
}
</style>