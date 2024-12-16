<template>
	<view class="w-full h-[95%] flex flex-col gap-[4px] overflow-hidden bg-white px-[16px] pt-[5px] box-border">
		<view class="w-full flex items-center justify-between gap-[10px]">
      <uv-input placeholder="请输入..." border="surround" v-model="inputVal"></uv-input>

      <uv-button text="添加" type="primary" @click="handleAdd"></uv-button>
    </view>

    <view class="w-full flex-1 overflow-hidden">
      <!-- <view class="w-full mb-[10px]" v-for="i in 100" :key="i">{{i}}</view> -->
      <scroll-view :scroll-y="true" class="w-full h-[100%]">
        <uv-empty v-if="list.length === 0" mode="data" text="暂无数据" marginTop="40"></uv-empty>
        <template v-else>
          <view
            class="w-full flex items-center justify-between gap-[8px] px-[10px] py-[20px] box-border bg-slate-100 mb-[8px] rounded-[4px] overflow-hidden"
            v-for="(item, key) in list"
            :key="key"
          >
            <text class="flex-[1_1_auto] text-[#333333] font-[500] text-[14px] break-all">{{key+1}}. {{item}}</text>

            <view class="flex-none flex items-center gap-[4px]">
              <uv-button text="编辑" plain :hairline="false" size="normal" type="info" @click="handleEdit(key)"></uv-button>
              <uv-button text="删除" plain :hairline="false" size="normal" type="error" @click="handleDelete(key)"></uv-button>
            </view>
          </view>
        </template>
      </scroll-view>
    </view>
	</view>
  <uv-modal
    ref="modalRef"
    title="修改"
    :closeOnClickOverlay="false"
    :showCancelButton="true"
    :asyncClose="true"
    @confirm="handleConfirmEdit"
    @cancel="handleCloseModal"
  >
    <view class="slot-content w-full">
      <uv-input placeholder="请输入..." border="surround" v-model="currentEditObj.val"></uv-input>
    </view>
  </uv-modal>

  <uv-safe-bottom></uv-safe-bottom>
</template>

<script setup>
import {ref, reactive} from "vue";

const modalRef = ref();
const inputVal = ref("");
const list = ref([]);
const currentEditObj = reactive({
  index: "",
  val: ""
});

const handleCloseModal = () => {
  modalRef.value.close();

  currentEditObj.val = "";
}

const handleAdd = () => {
  const newVal = inputVal.value.trim();
  if(newVal === "") {
    uni.showToast({
      title: "添加内容不可为空",
      icon: "none"
    })
    return;
  }

  list.value.push(newVal);
  inputVal.value = "";
  console.log("list:", JSON.stringify(list.value));
}

const handleEdit = (index) => {
  // uni.showModal({
  //   title: "修改",
  //   editable: true,
  //   placeholderText: "请输入...",
  //   success(res) {
  //     if(res.confirm) {
  //       const newVal = res.value;
  //       if(newVal.trim() === "") {
  //         uni.showToast({
  //           title: "修改内容不可为空",
  //           icon: "none"
  //         });
  //         return;
  //       }

  //       list.value.splice(index, 1, newVal);
  //     }
  //   }
  // });

  currentEditObj.index = index;
  currentEditObj.val = list.value[index];

  modalRef.value.open();
};

const handleConfirmEdit = () => {
  const newVal = currentEditObj.val.trim();
    if(newVal === "") {
      uni.showToast({
        title: "修改内容不可为空",
        icon: "none"
      });
      modalRef.value.closeLoading();
      return;
    }

  list.value.splice(currentEditObj.index, 1, newVal);
  handleCloseModal();
}

const handleDelete = (index) => {
  uni.showModal({
    title: "提示",
    content: "确定删除吗？",
    success(res) {
      if(res.confirm) {
        list.value.splice(index, 1);
      }
    }
  })
}
</script>
