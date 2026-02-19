<template>
  <div class="box cards" @mouseenter="closeShow = true" @mouseleave="closeShow = false">
    <transition name="el-fade-in-linear">
      <close-one
        class="close"
        theme="filled"
        size="28"
        fill="#ffffff60"
        v-show="closeShow"
        @click="store.boxOpenState = false"
      />
    </transition>
    <!-- 删除或注释掉下面的设置按钮 -->
    <!-- <transition name="el-fade-in-linear">
      <setting-two
        class="setting"
        theme="filled"
        size="28"
        fill="#ffffff60"
        v-show="closeShow"
        @click="store.setOpenState = true"
      />
    </transition> -->
    <div class="content">
      <!-- 可在此处自定义任意内容 -->
      <TimeCapsule />
      <MoreContent />
    </div>
  </div>
</template>

<script setup>
import { CloseOne, SettingTwo } from "@icon-park/vue-next";  // 这里可以删掉 SettingTwo
import { mainStore } from "@/store";
import TimeCapsule from "@/components/TimeCapsule.vue";
import MoreContent from "@/components/MoreContent.vue";

const store = mainStore();
const closeShow = ref(false);
</script>

<style lang="scss" scoped>
.box {
  flex: 1 0 0%;
  margin-left: 0.75rem;
  height: 80%;
  max-width: 50%;
  position: relative;
  animation: fade 0.5s;

  &:hover {
    transform: scale(1);
  }

  .close,
  .setting {  // 如果删除了设置按钮，可以只保留 .close 的样式
    position: absolute;
    top: 14px;
    right: 14px;
    width: 28px;
    height: 28px;
    transition:
      transform 0.3s,
      opacity 0.3s;

    &:hover {
      transform: scale(1.2);
    }

    &:active {
      transform: scale(1);
    }
  }

  // 可以删除或保留 .setting 样式，不影响
  .setting {
    right: 56px;
  }

  .content {
    display: flex;
    flex-direction: column;
    padding: 30px;
    width: 100%;
    height: 100%;
  }
}
</style>
