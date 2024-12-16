<template>
	<view class="w-full flex flex-col gap-[4px] px-[16px] box-border bg-white">
		<button type="primary" @click="handleOpenCamera">打开系统相机拍照</button>
    <image v-if="obj.imgSrc" :src="obj.imgSrc" class="w-[200px] h-[200px]" @error="handleImgLoadErr"></image>
    <text v-else>暂无图片，请打开相机拍照</text>
    <view class="w-full flex flex-col gap-[4px] overflow-hidden">
      <text>imgSrc: </text>
      <text :selectable="true" class="my-text-wrap">{{obj.imgSrc}}</text>
      <button type="primary" @click="handleCopy">复制base64</button>
      <text>error: </text>
      <text class="my-text-wrap">{{errorRef || '暂无错误信息'}}</text>
      <!-- <text>success: </text> -->
      <!-- <text class="my-text-wrap">{{successRef || '暂无成功信息'}}</text> -->
      <text>img load err:</text>
      <text class="my-text-wrap">{{imgLoadErrRef || '暂无图片加载失败信息'}}</text>
    </view>
	</view>
  <uv-safe-bottom></uv-safe-bottom>
</template>

<script setup lang="uts">
import { reactive, ref } from 'vue';
import { openCamera } from '@/uni_modules/ikun-openCamera';

const obj = reactive({
  imgSrc: "",
  videoSrc: "",
});

const successRef = ref();
const errorRef = ref();
const imgLoadErrRef = ref();

export type TCameraReturnObj = {
  imgSrc?: string;
  videoSrc?: string;
}

export type TImgLoadErr = {
  detail?: object
}

const handleOpenCamera = async () => {
  try {
    await openCamera({
      success: async (r: TCameraReturnObj) => {
        successRef.value = JSON.stringify(r);
        // if(r.imgSrc && (r.imgSrc.startsWith("/9j/") || r.imgSrc.startsWith("/9i/"))) {
        if(r.imgSrc) {
          // obj.imgSrc = `data:image/jpeg;base64,${r.imgSrc.slice(4)}`
          obj.imgSrc = `data:image/jpeg;base64,${r.imgSrc}`
        } else {
          obj.imgSrc = r.imgSrc;
        }

        obj.videoSrc = r.videoSrc;
      },
      fail(e) {
        errorRef.value = JSON.stringify(e);
        console.error("openCamera error:", JSON.stringify(e));
      }
    });
  } catch (error) {
    console.error(error);
  }
}

const handleCopy = () => {
  uni.setClipboardData({
    data: obj.imgSrc,
    fail: () => {
      uni.showToast({
        title: "复制失败",
        icon: "fail"
      })
    },
    success: () => {
      uni.showToast({
        title: "复制成功",
        icon: "success"
      })
    }
  })
}

const handleImgLoadErr = (event:TImgLoadErr) => {
  imgLoadErrRef.value = JSON.stringify(event.detail);
}
</script>

<style>
  /* breal-all text-wrap whitespace-pre-wrap */
.my-text-wrap {
  white-space: pre-wrap; /* 允许自动换行 */
  word-break: break-all; /* 在单词或连字符处换行 */
}
</style>
