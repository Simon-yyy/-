<template>
  <view>
 <view class="scroll-view-container">
	 <!--左侧的滑动区域-->
	 <scroll-view 	class="left-scroll-view"	scroll-y="true" :style="{height:wh+'px'}">
		<block v-for="(item,i) in cateList":key="i">
			<view :class="['left-scroll-view-item',i===active ? 'active':'']" @click="activeChange(i)">{{item.cat_name}}</view>
		</block>
	 </scroll-view>
	 <!--右侧的滑动区域-->
	 <scroll-view class="right-scroll-view" scroll-y="true":style="{height: wh + 'px'}" :scroll-top="scrollTap">
	   <view class="cate-lev2" v-for="(item2, i2) in cateLevel2" :key="i2">
	     <view class="cate-lev2-title">/ {{item2.cat_name}} /</view>
	     <!-- 动态渲染三级分类的列表数据 -->
	     <view class="cate-lev3-list">
	       <!-- 三级分类 Item 项 -->
	       <view class="cate-lev3-item" v-for="(item3, i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
	         <!-- 图片 -->
	         <image :src="item3.cat_icon"></image>
	         <!-- 文本 -->
	         <text>{{item3.cat_name}}</text>
	       </view>
	     </view>
	   </view>
	 </scroll-view>
 </view>
</view>
</template>

<script>
  export default {
    data() {
      return {
        //设置屏幕可用初始高度
				wh:0,
				cateList:[],
				active:0,
				cateLevel2:[],
				scrollTap:0
      };
    },
		onLoad() {
			//uni里面的API，可以动态获取屏幕可用高度
			const sycInfo=uni.getSystemInfoSync()
			this.wh=sycInfo.windowHeight
			this.getCateList()
		},
		methods:{
			async getCateList(){
				const{data:res}= await  uni.$http.get('/api/public/v1/categories')
				if(res.meta.status!==200) return uni.$showMsg()
				this.cateList=res.message
				this.cateLevel2=res.message[0].children
			},
			activeChange(i){
				this.active=i
				this.cateLevel2=this.cateList[i].children
				this.scrollTap=this.scrollTap===0?1:0
			},
			gotoGoodsList(item){
				uni.navigateTo({
					url:'/subpackage/goods_list/goods_list?cid'+item.cat_id
				})
			}
		}
		
  }

</script>

<style lang="scss">
.scroll-view-container{
	display: flex;
.left-scroll-view{
	width: 120px;
	
	.left-scroll-view-item{
		font-size: 12px;
		line-height: 60px;
		text-align: center;
		background-color: #f7f7f7;

		//激活项的样式
		&.active{
			background-color:#ffffff;
			position: relative;
			&::before{
				  content: ' ';
				          display: block;
				          width: 3px;
				          height: 30px;
				          background-color: #c00000;
				          position: absolute;
				          left: 0;
				          top: 50%;
									//！！！！！
				          transform: translateY(-50%);
			}
		}
	}
}
}
.cate-lev2-title{
	font-size: 12px;
	font-weight: bold;
	text-align: center;
	padding: 15px 0;
}



.cate-lev3-list {
  display: flex;
  flex-wrap: wrap;

  .cate-lev3-item {
    width: 33.33%;
    margin-bottom: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;

    image {
      width: 60px;
      height: 60px;
    }

    text {
      font-size: 12px;
    }
  }
}
</style>
