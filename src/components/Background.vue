<template>
  <div :class="store.backgroundShow ? 'cover show' : 'cover'">
    <!-- 视频背景 -->
    <video
      v-if="useVideo && videoUrl"
      ref="videoPlayer"
      class="bg-video"
      autoplay
      loop
      muted
      playsinline
      :src="videoUrl"
      @loadeddata="videoLoadComplete"
      @error="videoLoadError"
    ></video>
    
    <!-- 图片背景 -->
    <img
      v-else
      v-show="store.imgLoadStatus"
      :src="bgUrl"
      class="bg"
      alt="cover"
      @load="imgLoadComplete"
      @error.once="imgLoadError"
      @animationend="imgAnimationEnd"
    />
    <div :class="store.backgroundShow ? 'gray hidden' : 'gray'" />
  </div>
</template>

<script setup>
import { mainStore } from "@/store";
import { Error } from "@icon-park/vue-next";

const store = mainStore();
const bgUrl = ref(null);
const videoUrl = ref(null);
const imgTimeout = ref(null);
const useVideo = ref(false);
const emit = defineEmits(["loadComplete"]);

// 壁纸随机数
const bgRandom = Math.floor(Math.random() * 10 + 1);

// 更换壁纸链接
const changeBg = (type) => {
  console.log("切换壁纸类型:", type); // 添加日志方便调试
  useVideo.value = false;
  
  if (type == 0) {
    // 默认壁纸选项 -> 视频
    useVideo.value = true;
    videoUrl.value = "https://djkl.qzz.io/file/FrHYvLiA.mp4";
  } else if (type == 1) {
    bgUrl.value = "https://api.dujin.org/bing/1920.php";
  } else if (type == 2) {
    bgUrl.value = "https://wp.upx8.com/api.php";
  } else if (type == 3) {
    bgUrl.value = "https://api.yppp.net/api.php"; // 动漫API
  }
};

// 图片加载完成
const imgLoadComplete = () => {
  imgTimeout.value = setTimeout(() => {
    store.setImgLoadStatus(true);
  }, Math.floor(Math.random() * (600 - 300 + 1)) + 300);
};

// 视频加载完成
const videoLoadComplete = () => {
  console.log("视频加载完成");
  store.setImgLoadStatus(true);
  emit("loadComplete");
};

// 图片动画完成
const imgAnimationEnd = () => {
  emit("loadComplete");
};

// 图片显示失败
const imgLoadError = () => {
  console.error("壁纸加载失败：", bgUrl.value);
  ElMessage({
    message: "壁纸加载失败，已临时切换回默认",
    icon: h(Error, { theme: "filled", fill: "#efefef" }),
  });
  bgUrl.value = `/images/background${bgRandom}.jpg`;
};

// 视频加载失败
const videoLoadError = () => {
  console.error("视频加载失败，切换回动漫图片");
  useVideo.value = false;
  bgUrl.value = "https://api.yppp.net/api.php";
  ElMessage({
    message: "视频加载失败，已切换为动漫壁纸",
    grouping: true,
    type: "warning",
  });
};

// 监听壁纸切换
watch(
  () => store.coverType,
  (value) => {
    changeBg(value);
  },
  { immediate: true } // 加上这个确保首次加载就执行
);

onMounted(() => {
  // 关键：这里强制设置为3（动漫API）
  if (!store.coverType) {
    store.coverType = "3"; // 如果store里没有，设置为3
  }
  changeBg("3"); // 直接强制加载动漫API
});

onBeforeUnmount(() => {
  clearTimeout(imgTimeout.value);
});
</script>

<style lang="scss" scoped>
.cover {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  transition: 0.25s;
  z-index: -1;

  &.show {
    z-index: 1;
  }

  .bg, .bg-video {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
    backface-visibility: hidden;
    filter: blur(20px) brightness(0.3);
    animation: fade-blur-in 0.8s cubic-bezier(0.25, 0.46, 0.45, 0.94) forwards;
    animation-delay: 0.45s;
  }
  
  .gray {
    opacity: 1;
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-image: radial-gradient(rgba(0, 0, 0, 0) 0, rgba(0, 0, 0, 0.5) 100%),
      radial-gradient(rgba(0, 0, 0, 0) 33%, rgba(0, 0, 0, 0.3) 166%);
    transition: 1.5s;
    
    &.hidden {
      opacity: 0;
      transition: 1.5s;
    }
  }
}
</style>
