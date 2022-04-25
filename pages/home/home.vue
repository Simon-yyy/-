<template>
  <view>
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <!-- 循环渲染轮播图的 item 项 -->
      <swiper-item v-for="(item, i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpackage/goods_detail/goods_detail?goods_id'+item.goods_id">
          <!-- 动态绑定图片的 src 属性 -->
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
		<!--分类导航区-->
		<view class="nav-list">
			<view class="nav-item" v-for="(item,i) in navList":key="i" @click="navClickerHander(item)">
			<image :src="item.image_src" class="nav-img"></image>	
			</view>
		</view>
		<!--楼层的容器-->
		<view class="floor-list">
			<!--楼层样式-->
			<view class="floor-item" v-for="(item,i) in floorList":key="i">
				<!--楼层标题-->
				<image :src="item.floor_title.image_src "class="floor-title"></image>
				<!--楼层的图片-->
				<view class="floor-img-box">
					<!--左侧的盒子-->
					<navigator class="left-img-box":url="item.product_list[0].url">
					<image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
					</navigator>
					<!--右侧的盒子-->
					<view class="right-img-box">
					    <navigator class="right-img-item" v-for="(item2, i2) in item.product_list" :key="i2" v-if="i2 !== 0":url="item2.url">
					      <image :src="item2.image_src" mode="widthFix" :style="{width: item2.image_width + 'rpx'}"></image>
					    </navigator>
					  </view>
					</view>
				</view>
			</view>
		</view>
  </view>
</template>
<script>
  export default {
    data() {
      return {
       //定义一个数据列表
        swiperList:[],
				//分类导航的数据
				navList:[],
				//楼层的数据
				floorList:[]
      };
    },
    onLoad(){
      //在小程序刚加载时调用swiperList
      this.getSwiperList();
			this.getNavList();
			this.getFloorList();
    },
    methods: {
  //获取轮播图的方法
  async getSwiperList(){
    const {data:res}=	await		uni	.$http.get('/api/public/v1/home/swiperdata')
    //请求失败
    if(res.meta.status!==200)
     
    return uni.$showMsg()
    this.swiperList=res.message
  },
	async getNavList(){
		const {data:res}=	await	uni	.$http.get('/api/public/v1/home/catitems')
		//请求失败
		if(res.meta.status!==200)
		return uni.$showMsg()
		this.navList=res.message
	},
	//
	navClickerHander(item){
		if(item.name==='分类'){
			uni.switchTab({
				url:'/pages/cate/cate'
			})
		}
	},
async	getFloorList(){
		const {data:res}=await uni.$http.get('/api/public/v1/home/floordata')
		if(res.meta.status!==200) return uni.$showMsg()
		res.message.forEach(floor=>{
			floor.product_list.forEach(prod=>{
				prod.url='/subpackage/goods_list/goods_list?'+prod.navigator_url.split('?')[1]
			})
		})
		
		this.floorList=res.message
	},
    },
	
  }
  
</script>

<style lang="scss">
swiper{
	height: 330rpx;
.swiper-item,
	image{
		width: 100%;
		height: 100%;
	}
}
.nav-list{
	display: flex;
	justify-content: space-around;
		margin: 15px 0;
		
	.nav-img{
		height: 140rpx;
		width:128rpx
	}
}
//楼层标题
.floor-title{
		 height: 60rpx;
		  width: 100%;
		  display: flex;
	}
	//右侧四张小图片的盒子
	.right-img-box {
	  display: flex;
	  flex-wrap: wrap;
	  justify-content: space-around;
	}
	//整个大盒子
	.floor-img-box {
	  display: flex;
	  padding-left: 10rpx;
	}
</style>
