<template>
	<view class="content">
		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<button type="default" @click="tonext">导航地址</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 地图key 这个k值是你自己申请的k
				key: '',
				// 任务的地址 这个是需要转换的目的地地址
				city: '安徽省合肥市巢湖市东塘路与太湖山路交叉口',
				lat:0,
				log:0,
			}
		},
		onLoad() {
			// 页面渲染后好给地址解析成坐标
			uni.request({
				url: 'https://apis.map.qq.com/ws/geocoder/v1/?address='+this.city+'&key='+this.key,
				success:res=>{
					this.lat = res.data.result.location.lat
					this.log = res.data.result.location.lng
				}
			})
		},
		methods: {
			tonext() {
				// 点击跳转携带坐标参数
				uni.navigateTo({
					url: '../mappage/mappage?lat='+ this.lat +'&log='+this.log
				})
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
