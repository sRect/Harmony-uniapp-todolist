<template>
	<view class="content">
		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
		<button @click="goto('todolist')">goto todolist</button>
		<button @click="goto('hmApi')">调用鸿蒙原生api</button>
	</view>
</template>

<script>
  const urlConf = {
    todolist: "/pages/todolist/todolist",
    hmApi: "/pages/hmApi/hmApi",
  };

	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		methods: {
			goto(key) {
				uni.navigateTo({
					url: urlConf[key]
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
    gap: 6px;
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
