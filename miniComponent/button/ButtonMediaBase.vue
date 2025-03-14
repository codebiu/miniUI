<template>
    <!-- 外部透明圆形区域，用于拖动 -->
    <div class="drag-area" style="-webkit-app-region: drag;">
      <div class="circle" style="-webkit-app-region:drag;">
        <span class="circle__btn" :class="{ shadow: isPlaying }" @click="togglePlay">
          <span class="play" v-if="isPlaying" style="-webkit-app-region:no-drag;">
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
          <span class="play" style="margin-left: 8px;-webkit-app-region:no-drag;" v-else>
            ▶
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