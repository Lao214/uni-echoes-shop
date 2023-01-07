<template>
	<view>
		<view class="goods-list">
			<view v-for="(item, index) in goodsList" :key="index" @click="gotoDetail(item)">
				<my-goods :goods="item"></my-goods>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 请求参数对象
				queryObj: {
					query : '',
					cid : '',
					pagenum: 1,
					pagesize: 10
				},
				goodsList: [],
				total: 0,
				isLoading: false
			};
		},
		onLoad(options) {
			// console.log(options) 
			this.queryObj.query = options.query || ''
			this.queryObj.cid = options.cid || ''
			// console.log(this.queryObj)
			this.getGoodsList()
		},
		methods: {
			// 获取商品列表数据的方法
			async getGoodsList(cb) {
				// 打开节流阀
				this.isLoading = true
				const { data: res } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
				// 关闭节流阀
				this.isLoading = false
				// 只要数据请求完毕，就立即按需调用 cb 回调函数
				cb && cb()
				if(res.meta.status !== 200 ) return uni.$showMsg()
				//数组的拼接
				this.goodsList = [...this.goodsList, ...res.message.goods]
				this.total = res.message.total
			},
			gotoDetail(goods) {
				uni.navigateTo({
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id
				})
			}
		},
		onReachBottom() {
			// 判断是否还有下一页
			if(this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('已无下一页数据')
			// 如果节流阀打开 将该方法return出去
			if(this.isLoading) return
			// 让页码值自增 + 1
			this.queryObj.pagenum++
			this.getGoodsList()
		},
		onPullDownRefresh() {
			//重置关键数据
			this.queryObj.pagenum = 1
			this.total = 0
			this.isLoading = false
			this.goodsList = []
			//重新发起数据请求
			this.getGoodsList(() => uni.stopPullDownRefresh())
		}
	}
</script>

<style lang="scss">
	
</style>
