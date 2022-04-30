<template>
	<view>
		<view class="search-T">
			<uni-search-bar @confirm="search" @input="input" :radius="100" cancelButton="none"></uni-search-bar>
			<!-- 搜索建议列表项 -->
			<view class="suggest-item" v-if="searchList.length!==0">
			<view class="suggest-item-list" v-for="(item,i) in searchList":key="i" @click="gotoList(item)">
				<view class="goods_list" >{{item.goods_name}}</view>
			<uni-icons type="forward" size="16"></uni-icons>
			</view>	
			</view>
			<!-- 搜索历史 -->
			<view class="history-box" v-else>
				<!-- 上侧标题区域 -->
				<view class="history-title">
					<text>搜索历史</text>
    <uni-icons type="trash" size="17" @click="clean"></uni-icons>
				</view>
				<!-- 下侧文本区域 -->
				<view class="history-item" >
				<uni-tag :text="item" inverted="true" v-for="(item,i) in histories" :key="i" @click="gotoGoodslist(item)"></uni-tag>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				timer:null,
				kw:'',
				searchList:[],
				historyList:[]
			};
		},
		onLoad() {
  this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')	
		},
		methods:{
			//定义输入函数
			input(e){
		  // 清除 timer 对应的延时器
		  clearTimeout(this.timer)
		  // 重新启动一个延时器，并把 timerId 赋值给 this.timer
		  this.timer = setTimeout(() => {
		    // 如果 500 毫秒内，没有触发新的输入事件，则为搜索关键词赋值
		    this.kw = e
				this.getSearchList()
		  }, 500)
			},
	async		getSearchList(){
				if(this.kw===''){
					this.searchList=[]
					return
				}
			  const{data:res}   =  await	uni.$http.get('/api/public/v1/goods/qsearch',{query:this.kw})
				if (res.meta.status!==200) return uni.$showMsg()
					
				this.searchList=res.message
				
    // 1. 查询到搜索建议之后，调用 saveSearchHistory() 方法保存搜索关键词
				this.saveSearchHistroy()
			},
			gotoList(item){
				uni.navigateTo({
					url:'/subpackage/goods_detail/goods_detail?goods_id'+item.goods_id
				})
			},
			//将记录存储到数组中
			saveSearchHistroy(){
				// this.historyList.push(this.kw)
				//使用set数据的唯一性保存数据
				const set=new Set(this.historyList)
				set.delete(this.kw)
				set.add(this.kw)
				this.historyList=Array.from(set)
				//对搜索历史记录进行持久化的存储
				uni.setStorageSync('kw',JSON.stringify(this.historyList))
			},
			clean(){
				this.historyList=[]
				uni.setStorageSync('kw','[]')
			},
			gotoGoodslist(kw){
				uni.navigateTo({
					url:'/subpackage/goods_list/goods_list?query='+kw
				})
			}
		},
		computed:{
			histories(){
			return	[...this.historyList].reverse()
			}
				
			}
		}
	
</script>

<style lang="scss">
.search-T{
	position:sticky;
	top: 0;
	z-index: 999;
}
.suggest-item{
	padding: 0 5px;
	.suggest-item-list{
		display: flex;
		align-items: center;
	  justify-content: space-between;
		padding: 13px 0;
		border-bottom: 1px solid #efefef;
		.goods_list{
			white-space: nowrap;
			overflow: hidden;
			text-overflow: ellipsis;
			margin-right: 3px;
			font-size: 13px;
		}
	}
}
.history-box{
	padding: 0 5px;
	.history-title{
		display: flex;
		justify-content: space-between;
		font-size: 15px;
		align-items: center;
		border-bottom: 1px solid #efefef;
		height: 40px;
		}
		.history-item{
			display: flex;
			flex-wrap: wrap;
		.uni-tag{
			margin-top: 5px;
			margin-right: 5px;
		}
		}
}
</style>
