<template>
  <!-- 外部透明圆形区域，用于拖动 -->
  <div class="drag-area" style="-webkit-app-region: drag;">
    <div class="circle" style="-webkit-app-region:drag;">
      <span class="circle__btn" :class="{ shadow: isPlaying }" @click="togglePlay">
        <span v-if="isPlaying" style="-webkit-app-region:no-drag;">
          <template v-if="voiceIng">
            <div class="input-indicator">
              <span class="bar"></span>
              <span class="bar"></span>
              <span class="bar"></span>
              <span class="bar"></span>
            </div>
          </template>
          <template v-else>❚❚</template>
        </span>
        <span style="-webkit-app-region:no-drag;" v-else>
          <svg t="1741957978006" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="1545" width="38" height="38"><path d="M497.2544 634.4704c106.1376 0 193.3824-86.0672 193.3824-190.72V215.808c0-104.6528-87.2448-190.6688-193.3824-190.6688-106.1376 0-193.3824 86.016-193.3824 190.6688v227.9424c0 105.8304 87.2448 190.72 193.3824 190.72z" fill="#63cef5" p-id="1546"></path><path d="M840.3456 497.2544a36.6592 36.6592 0 0 0-31.7952-43.008c-21.248-3.5328-41.2672 11.5712-43.6224 31.3856-21.248 127.8976-135.6288 225.5872-267.6736 225.5872-132.096 0-247.6032-96.512-267.6736-225.5872a38.1952 38.1952 0 0 0-43.6224-31.4368c-21.1968 3.4816-35.328 22.1184-31.7952 43.008 24.7296 155.8528 149.7088 271.0016 305.3568 288.4096v116.2752H343.9616c-21.1968 0-38.912 17.4592-38.912 38.4s17.7152 38.4 38.912 38.4h308.9408c21.1968 0 38.912-17.4592 38.912-38.4a39.0144 39.0144 0 0 0-38.912-38.4H537.344v-116.224c153.2928-17.4592 279.4496-132.608 303.0016-288.4096z" fill="#63cef5" p-id="1547"></path></svg>
        </span>
      </span>
      <span class="circle__back-1" :class="{ paused: !isPlaying }"></span>
      <span class="circle__back-2" :class="{ paused: !isPlaying }"></span>
      <!-- asr监听 -->
      <span class="circle__back_asrstart" v-if="isvoice"></span>
      <span class="circle__back_voiceing" v-if="voiceIng"></span>
    </div>
  </div>
</template>

<script setup lang="ts">
// 使用 defineModel 实现双向绑定
const isPlaying = defineModel<boolean>('isPlaying', { required: true });
const isvoice = defineModel<boolean>('isvoice', { required: true });
const voiceIng = defineModel<boolean>('voiceIng', { required: true });

// 切换播放/暂停状态
const togglePlay = () => {
  isPlaying.value = !isPlaying.value;
  console.log(isPlaying.value ? '暂停' : '播放');

  if (isPlaying.value) {
    // 模拟开启语音识别
    isvoice.value = true;
    voiceIng.value = false;
  } else {
    // 模拟关闭语音识别
    isvoice.value = false;
    voiceIng.value = false;
  }
};
</script>

<style>
/* 定义 CSS 变量 */
:root {
  --primary: #aed3fd;
  --greyLight-1: #f1f1f1;
  --greyLight-2: #d3d3d3;
  --white: #ffffff;
}

/* 外部透明圆形区域，用于拖动 */
.drag-area {
  width: 8rem;
  /* 扩大拖动区域 */
  height: 8rem;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  background: transparent;
  cursor: move;
  /* 透明背景 */
}


/*  PLAY BUTTON  */
.circle {
  user-select: none;
  width: 4rem;
  height: 100%;
  justify-self: center;
  border-radius: 1rem;
  display: grid;
  grid-template-rows: 1fr;
  justify-items: center;
  align-items: center;
}

.circle__btn {
  grid-row: 1/2;
  grid-column: 1/2;
  width: 4rem;
  height: 4rem;
  display: flex;
  margin: 0.6rem;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  font-size: 2.2rem;
  color: var(--primary);
  z-index: 300;
  background: var(--greyLight-1);
  box-shadow: 0.3rem 0.3rem 0.6rem var(--greyLight-2), -0.2rem -0.2rem 0.5rem var(--white);
  /* cursor: pointer; */
  position: relative;
}

.circle__btn.shadow {
  box-shadow: inset 0.2rem 0.2rem 0.5rem var(--greyLight-2), inset -0.2rem -0.2rem 0.5rem var(--white);
}

.circle__btn .play {
  position: absolute;
  transition: opacity 0.2s linear;
  cursor: pointer;
}

.circle__back-1 {
  grid-row: 1/2;
  grid-column: 1/2;
  width: 4rem;
  height: 4rem;
  border-radius: 50%;
  filter: blur(1px);
  z-index: 100;
}


.circle__back-1 {
  box-shadow: 0.4rem 0.4rem 0.8rem var(--greyLight-2), -0.4rem -0.4rem 0.8rem var(--white);
  background: linear-gradient(to bottom right, var(--greyLight-2) 0%, var(--white) 100%);
  animation: waves 4s linear infinite;
}

.circle__back-1.paused {
  animation-play-state: paused;
}



.circle__back-2 {
  grid-row: 1/2;
  grid-column: 1/2;
  width: 4rem;
  height: 4rem;
  border-radius: 50%;
  filter: blur(1px);
  z-index: 100;
}

.circle__back-2 {
  box-shadow: 0.4rem 0.4rem 0.8rem var(--greyLight-2), -0.4rem -0.4rem 0.8rem var(--white);
  animation: waves 4s linear 2s infinite;
}

.circle__back-2.paused {
  animation-play-state: paused;
}

.circle__back_asrstart {
  grid-row: 1/2;
  grid-column: 1/2;
  width: 4rem;
  height: 4rem;
  border-radius: 50%;
  filter: blur(1px);
  z-index: 100;
  background-color: rgba(221, 191, 255, 0.562);
}


.circle__back_asrstart {
  box-shadow: 0.4rem 0.4rem 0.8rem rgba(221, 191, 255, 0.562), -0.4rem -0.4rem 0.8rem rgba(151, 60, 255, 0.562);
  animation: waves 4s linear infinite;
}

.circle__back_voiceing {
  grid-row: 1/2;
  grid-column: 1/2;
  width: 4rem;
  height: 4rem;
  border-radius: 50%;
  filter: blur(1px);
  z-index: 101;
  background-color: rgba(0, 132, 255, 0.562);
}


.circle__back_voiceing {
  box-shadow: 0.4rem 0.4rem 0.8rem rgba(0, 132, 255, 0.562), -0.4rem -0.4rem 0.8rem rgba(171, 214, 255, 0.562);
  animation: waves 4s linear infinite;
}

@keyframes waves {
  0% {
    transform: scale(1);
    opacity: 1;
  }

  50% {
    transform: scale(1.2);
    opacity: 0.5;
  }

  100% {
    transform: scale(1);
    opacity: 1;
  }
}

/* 声波容器 */
/* 容器样式，使用 Flexbox 布局并将子元素底部对齐 */
.input-indicator {
  display: flex;
  justify-content: center;
  align-items: flex-end;
  margin: 2px;
}

/* 竖线样式 */
.bar {
  width: 3px;
  background-color: rgba(61, 160, 252, 0.562);
  margin: 0 3px;
  height: 15px;
  transform-origin: bottom;
  animation: pulse 0.9s infinite ease-in-out alternate;
  box-shadow: 0.2rem 0.2rem 0.4rem rgba(0, 132, 255, 0.562), -0.1rem -0.1rem 0.1rem rgba(171, 214, 255, 0.562);
}

/* 为每个竖线设置不同的动画延迟 */
.bar:nth-child(2) {
  animation-delay: -0.3s;
}

.bar:nth-child(3) {
  animation-delay: -0.9s;
}

.bar:nth-child(4) {
  animation-delay: -0.6s;
}

/* 定义动画效果，竖线从底部向上伸展 */
@keyframes pulse {
  0% {
    transform: scaleY(0.3);
  }

  100% {
    transform: scaleY(1);
  }
}
</style>