<template>
	<view>
		
		<my-search @gotoSearch="gotoSearch" :bgcolor="'#C00000'" :radius="7"></my-search>
		
		<view class="scroll-view-container">
			<!-- 左侧的滑动区域 -->
			<scroll-view class="left-scroll-view" scroll-y="true" :style="{height: wh + 'px'}">
				<block v-for="(item, index) in cateList" :key="index" >
					<view :class="['left-scroll-view-item', index === active ? 'active' : '']" @click="activeChange(index)">
						{{ item.cat_name }}
					</view>
				</block>
			</scroll-view>
			<!-- 右侧的滑动区域 -->
			<scroll-view scroll-y="true" :style="{height: wh + 'px'}" :scroll-top="scrollTop">
				<view class="cate-lv2" v-for="(item2, index2) in cateLevel2" :key="index2">
					<view class="cate-lv2-title">
						/ {{ item2.cat_name }} /
					</view>
					<!-- 动态渲染三级分类的列表数据 -->
					<view class="cate-lv3-list">
						<!-- 三级分类Item项 -->
						<view class="cate-lv3-item" v-for="(item3, index3) in item2.children" :key="index3" @click="gotoGoodsList(item3)">
							<!-- 图片 -->
							<image :src="item3.cat_icon.replace('dev','web')"></image>
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
	import badgeMix from '@/mixins/tabbar-badge.js'
	
	export default {
		mixins: [badgeMix],
		data() {
			return {
				// 当前设备可用高度
				wh: 0,
				cateList:[],
				active: 0,
				// 二级分类的列表
				cateLevel2:[],
				scrollTop: 0
			};
		},
		onLoad() {
			// 获取品牌设备 getSystemInfoSync
			const sysInfo = uni.getSystemInfoSync()
			// console.log(sysInfo)
			this.wh = sysInfo.windowHeight - 50
			
			this.getCateList()
		},
		methods: {
			async getCateList() {
				const { data: res } = await uni.$http.get('/api/public/v1/categories')
				if(res.meta.status !== 200) {
					return uni.$showMsg()
				}
				this.cateList = res.message
				this.cateLevel2 = res.message[0].children
				uni.$showMsg('数据加载成功！')
			},
			activeChange(index) {
				this.active = index
				
				// 重新为 二级分类赋值。
				this.cateLevel2 = this.cateList[index].children
				// 赋值相同， 不会重置滚动条的位置。
				this.scrollTop = this.scrollTop === 0 ? 1 : 0
			},
			// 跳转到商品列表页面
			gotoGoodsList(item) {
				uni.navigateTo({
					url: '/subpkg/goods_list/goods_list?cid=' + item.cat_id
				})
			},
			gotoSearch() {
				uni.navigateTo({
					url: '/subpkg/search/search'
				})
			}
		}
	}
</script>

<style lang="scss">
.scroll-view-container{
	display: flex;
	.left-scroll-view {
		width: 120px;
	}
	.left-scroll-view-item {
		background-color: #F7F7F7;
		line-height: 60px;
		text-align: center;
		font-size: 12px;
		
		// 如果元素即包含 left-scroll-view-item  又包含 active
		&.active {
			background-color: #FFFFFF;
			position: relative;
			
			&::before {
				content: ' ';
				display: block;
				width: 3px;
				height: 30px;
				background-color: #C00000;
				position: absolute;
				top: 50% ;
				left: 0%;
				transform: translateY(-50%);
			}
		}
	}
	.cate-lv2-title {
		font-size: 12px;
		font-weight: bold;
		text-align: center;
		padding: 15px 0;
	}
	
	.cate-lv3-list {
		display: flex;
		flex-wrap: wrap;
		.cate-lv3-item {
			width: 33.33%;
			display: flex;
			flex-direction: column;
			justify-content: center;
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
}
</style>
