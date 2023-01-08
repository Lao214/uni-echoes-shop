<template>
	<view class="login-container">
		<!--  -->
		<uni-icons type="contact-filled" size="100" color="#AFAFAF"></uni-icons>
		<button type="primary" class="btn-login" open-type="getUserInfo" @getuserinfo="getUserInfo">一键登录</button>
		<!-- <button type="primary" class="btn-login" @click="getUserProfile">一键登录</button> -->
		<text class="tips-text">登录后尽享更多权益</text>
	</view>
</template>

<script>
	import { mapMutations,mapState } from 'vuex'
	
	export default {
		name:"my-login",
		data() {
			return {
				
			};
		},
		methods: {
			...mapMutations('m_user',['updateUserInfo', 'updateToken']),
			// 用户授权后 获取用户的基本信息
			getUserInfo(e) {
				// console.log(e)
				if(e.detail.errMsg === 'getUserInfo:fai auth deny') return uni.$showMsg('您取消了登录授权！')
				
				// console.log(e.detail.userInfo)
				this.updateUserInfo(e.detail.userInfo)
				
				this.getToken(e.detail)
			},
			//由于已经无法再拿到 那个授权 ，故改为getUserProfile
			// getUserProfile() {
			//     uni.getUserProfile({
			//         desc: '你的授权信息',
			//         success: (res) => {
			// 			// 将信息存到 vuex 中
			//             this.updateUserInfo(res.userInfo)
			//             this.getToken(res)
			//         },
			//         fail: (res) => {
			//             return uni.$showMsg('您取消了登录授权')
			//         }
			//     })
			// },
			async getToken(info) {
				// 获取code 对应的值
				const [err, res] = await uni.login().catch(err => err)
				
				if(err || res.errMsg !== 'login:ok') return uni.$showMsg('登录失败1！')
				
				
				// 准备参数
				const query = {
					code: res.code,
					encryptedData: info.encryptedData,
					iv: info.iv,
					rawData: info.rawData,
					signature: info.signature
				}
				
				const {data: loginResult} = await uni.$http.post('/api/public/v1/users/wxlogin',query)
				console.log(loginResult)
				if(loginResult.meta.status !== 200) {
					// return uni.$showMsg('登录失败2！')
					// 已经拿不到token了
					uni.$showMsg('登录失败2！')
					this.updateToken('tokenbymyself')
				} else {
					//直接把token 保存到 vuex中
					this.updateToken(loginResult.message.token)
					this.navigateBack()
				},
				navigateBack() {
					if(this.redirectInfo && this.redirectInfo.opentype === 'switchTab') {
						uni.switchTab({
							url: this.redirectInfo.from,
							complete: () => {
								this.updateRedirectInfo(null)
							}
						})
					}
				}
			}
		},
		computed: {
			...mapState('m_user',['redirectInfo'])
		}
	}
</script>

<style lang="scss">
	.login-container{
		height: 750rpx;
		background-color: #F8F8F8;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		position: relative;
		overflow: hidden;
		
		// &::after {
		// 	content: '';
		// 	display: block;
		// 	width: 100%;
		// 	height: 40px;
		// 	background-color: white;
		// 	position: absolute;
		// 	bottom: 0;
		// 	left: 0;
		// 	border-radius: 100%;
		// 	transform: translateY(50%);
		// }
		.btn-login {
			width: 90%;
			border-radius: 100px;
			margin: 15px;
			background-color: #C00000;
		}
		.tips-text {
			font-size: 12px;
			color: gray;
		}
	}
</style>