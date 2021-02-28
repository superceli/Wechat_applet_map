<template>
	<view class="">
		<map
		id="myMap"
		:latitude="latitude" 
		:longitude="longitude" 
		:markers="markers"
		show-location
		@updated="lodingmap"
		>
		</map>
		<view class="tobus">
			<image @click="loding" class="dw" src="../../static/dw.png" mode=""></image>
			<view class="tobus_box">
				<text class="tobus_box_title">懂我不言（卫生打扫）</text>
				<view class="txt_list">
					<text>庐阳区</text>
					<text>万达广场</text>
					<text>某某路</text>
				</view>
				<button type="default" @click="tobus">到这去</button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 经纬度是自己的
				latitude: 0,
				longitude:0,
				// 这里经纬度是到店铺的位置
				markers: [
					{
						id:1,
						width: 50,
						height: 50,
						iconPath: '../../static/shop_weizhi.png',
						latitude: 0,
						longitude: 0,
					}
				],
				// 1是开启导航 0是不开启
				num:1,
				// 标记点的地址
				city: '',
				flag: false,
				key:''
			}
		},
		// 页面初次渲染完成
		onReady() {
			// 获取地图实例，绑定到id叫myMap上面
		    this.mapCtx = wx.createMapContext('myMap')
		},
		onLoad(option) {
			// 这两个是标记点坐标
			this.markers[0].latitude = parseFloat(option.lat)
			this.markers[0].longitude = parseFloat(option.log)
			// 把标记坐标转换成地址（逆解析）
			uni.request({
				// 此处的key需要填写
				url: 'https://apis.map.qq.com/ws/geocoder/v1/?location='
				+this.markers[0].latitude+','+this.markers[0].longitude+
				'&key='+this.key+'&get_poi=1',
				success: res=>{
					this.city = res.data.result.formatted_addresses.recommend
					// console.log(res.data.result.formatted_addresses.recommend);
				}
			})
			//进入地图页面后获取自己的坐标
			uni.getLocation({
			    type: 'gcj02',
			    success: res=> {
					// 获取到坐标后存起来
					this.latitude = res.latitude
					this.longitude = res.longitude
					
					// 使用地图实例中的方法，让标记和我的位置都在可视区域里显示
					this.mapCtx.includePoints({
					      padding: [140,50,140,50],
					      points: [
							  // 第一个是我的位置数据
							 {
					          latitude: this.latitude,
					          longitude: this.longitude
					        },
							// 第二个是店铺位置数据
					        {
					          latitude: this.markers[0].latitude,
					          longitude: this.markers[0].longitude
					        }
					      ]
					    })
			    }
			});
		},
		methods:{
			// 当地图加载完成后触发
			lodingmap() {
				this.flag = true
			},
			// 点击回到自己的位置
			loding() {
				if(this.flag){
					this.mapCtx.moveToLocation()
				}
			},
			tobus() {
				if(this.flag){
					let plugin = requirePlugin('routePlan');
					// 这里的k自己在腾讯地图api上申请
					let key = this.key;  //使用在腾讯位置服务申请的key
					let referer = '定位';   //调用插件的app的名称
					let endPoint = JSON.stringify({  //终点
					  'name': this.city,
					  'latitude': this.markers[0].latitude,
					  'longitude': this.markers[0].longitude
					});
					wx.navigateTo({
					  url: 'plugin://routePlan/index?key=' + key + '&referer=' + referer + '&endPoint=' + endPoint+'&navigation='+this.num
					});
				}
			}
		}
	}
</script>

<style>
	map{
		width: 100%;
		height: calc(100vh - 226rpx);
	}
	.tobus{
		height: 266rpx;
		box-sizing: border-box;
		padding: 20rpx 30rpx 30rpx 30rpx;
		background-color: #fff;
		margin: 0 0rpx;
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		border-radius: 10rpx 10rpx 0 0;
	}
	.tobus_box_title{
		font-size: 32rpx;
		font-weight: bold;
		color: #333333;
	}
	.txt_list{
		display: flex;
		margin: 30rpx 0;
	}
	.txt_list text{
		font-size: 24rpx;
		color: #333;
		margin-right: 14rpx;
	}
	.tobus .tobus_box button{
		height: 84rpx;
		background: #1588ED;
		border-radius: 10rpx;
		color: #fff;
		font-size: 32rpx;
	}
	.dw{
		transform: translateY(-100%);
		top: -30rpx;
		position: absolute;
		right: 20rpx;
		width: 70rpx;
		height: 70rpx;
		bottom: 420rpx;
	}
</style>
