<template>
	<view class="goods-item">
		<!-- 商品左侧图片区域 -->
		<view class="goods-item-left">
			<radio :checked="goods.goods_state" color="#C00000" v-if="showRadio" @click="radioClickHandler"></radio>
			<image :src="goods.goods_small_logo || defaultPic" class="goods-pic"></image>
		</view>
		<!-- 商品右侧图片区域 -->
		<view class="goods-item-right">
			<!--商品标题-->
			<view class="goods-name">
				{{ goods.goods_name }}
			</view>
			<view class="goods-info-box">
				<!-- 商品价格 -->
				<view class="goods-price">
					¥{{ goods.goods_price | tofixed }}
				</view>
				<uni-number-box :min="1" :value="goods.goods_count" v-if="showNum" @change="numChangeHandler"></uni-number-box>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"my-goods",
		props: {
			goods: {
				type : Object,
				default: {}
			},
			showRadio: {
				type: Boolean,
				//默认情况下，不会展示radio组件
				default: false
			},
			showNum: {
				type: Boolean,
				//默认情况下，不会展示radio组件
				default: false
			}
		},
		data() {
			return {
				defaultPic: 'http://image5.suning.cn/uimg/b2c/newcatentries/0000000000-000000000611256555_2_200x200.jpg'
			};
		},
		filters : {
			tofixed(num) {
				return Number(num).toFixed(2)
			}
		},
		methods: {
			radioClickHandler() {
				this.$emit('radio-change',{
					goods_id: this.goods.goods_id,
					goods_state: !this.goods.goods_state
				})
			},
			// 监听到了 numberbox 组件的值变化
			numChangeHandler(val) {
				this.$emit('num-change',{
					goods_id: this.goods.goods_id,
					goods_count: +val
				})
			}
		}
	}
</script>

<style lang="scss">
	.goods-item{
		width: 750rpx;
		box-sizing: border-box;
		display: flex;
		padding: 10px 5px;
		border-bottom: 1px solid #f0f0f0;
		.goods-item-left {
			margin-left: 5px;
			display: flex;
			justify-content: space-between;
			align-items: center;
			.goods-pic {
				width: 100px;
				height: 100px;
				display: block;
			}
		}
		.goods-item-right {
			display: flex;
			flex: 1;
			flex-direction: column;
			justify-content: space-between;
			align-items: center;
			.goods-name {
				font-size: 13px;
			}
			.goods-info-box {
				display: flex;
				justify-content: space-between;
				.goods-price {
					color: #C00000;
					font-size: 16px;
				}
			}
		}
	}
</style>