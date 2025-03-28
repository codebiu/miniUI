<template>
    <div
      ref="draggableButton"
      class="fixed z-50 p-4 rounded-full shadow-lg cursor-move select-none"
      :class="[
        buttonColor,
        {
          'transition-all duration-300': !dragging,
          'transition-none': dragging,
          'opacity-70': !isHovering && !dragging,
          'opacity-100': isHovering || dragging
        }
      ]"
      :style="buttonStyle"
      @mousedown="startDrag"
      @touchstart="startDrag"
      @mouseenter="isHovering = true"
      @mouseleave="isHovering = false"
    >
      <slot>Drag Me</slot>
    </div>
  </template>
  
  <script setup>
  import { ref, computed, onMounted, onBeforeUnmount } from 'vue';
  // 使用 defineModel 替代原来的 prop + emit
    const model = defineModel({
    default: 'right',
    validator: (value) => ['top', 'right', 'bottom', 'left'].includes(value)
    });
  const props = defineProps({
    buttonColor: {
      type: String,
      default: 'bg-blue-500 text-white hover:bg-blue-600'
    },
    // 默认嵌入屏幕外的比例 (0-1)
    embedRatio: {
      type: Number,
      default: 0.3,
      validator: (value) => value >= 0 && value <= 1
    }
  });
  
  const emit = defineEmits(['edge-changed']);
  
  const draggableButton = ref(null);
  const dragging = ref(false);
  const isHovering = ref(false);
  const edgePosition = ref(0.5); // 沿边缘的位置(0-1)
  
  // 计算按钮样式
  const buttonStyle = computed(() => {
    const edgeMargin = 0; // 距离边缘的像素
    const embedPixels = props.embedRatio * 100; // 嵌入屏幕外的像素值
    const pos = edgePosition.value * 100;
    
    // 默认状态下的偏移量（部分嵌入屏幕外）
    const defaultOffset = isHovering.value || dragging.value ? 0 : embedPixels;
    
    switch (model.value) {
      case 'top':
        return {
          top: `${edgeMargin}px`,
          left: `${pos}%`,
          transform: `translateX(-50%) translateY(${-defaultOffset}px)`
        };
      case 'right':
        return {
          right: `${edgeMargin}px`,
          top: `${pos}%`,
          transform: `translateY(-50%) translateX(${defaultOffset}px)`
        };
      case 'bottom':
        return {
          bottom: `${edgeMargin}px`,
          left: `${pos}%`,
          transform: `translateX(-50%) translateY(${defaultOffset}px)`
        };
      case 'left':
        return {
          left: `${edgeMargin}px`,
          top: `${pos}%`,
          transform: `translateY(-50%) translateX(${-defaultOffset}px)`
        };
    }
  });
  
  // 开始拖动
  const startDrag = (e) => {
    dragging.value = true;
    
    // 获取初始位置
    const clientX = e.type === 'mousedown' ? e.clientX : e.touches[0].clientX;
    const clientY = e.type === 'mousedown' ? e.clientY : e.touches[0].clientY;
    
    // 计算沿边缘的初始位置
    const windowWidth = window.innerWidth;
    const windowHeight = window.innerHeight;
    
    switch (model.value) {
      case 'top':
      case 'bottom':
        edgePosition.value = clientX / windowWidth;
        break;
      case 'left':
      case 'right':
        edgePosition.value = clientY / windowHeight;
        break;
    }
    
    e.preventDefault();
    e.stopPropagation();
    
    document.addEventListener('mousemove', handleDrag);
    document.addEventListener('touchmove', handleDrag, { passive: false });
    document.addEventListener('mouseup', stopDrag);
    document.addEventListener('touchend', stopDrag);
  };
  
  // 计算最近的边缘
  const getClosestEdge = (x, y) => {
    const windowWidth = window.innerWidth;
    const windowHeight = window.innerHeight;
    
    const distances = {
      top: y,
      right: windowWidth - x,
      bottom: windowHeight - y,
      left: x
    };
    
    return Object.entries(distances).reduce((min, [edge, dist]) => 
      dist < distances[min] ? edge : min, 'top');
  };
  
  // 处理拖动
  const handleDrag = (e) => {
    if (!dragging.value) return;
    
    e.preventDefault();
    e.stopPropagation();
    
    const clientX = e.type === 'mousemove' ? e.clientX : e.touches[0].clientX;
    const clientY = e.type === 'mousemove' ? e.clientY : e.touches[0].clientY;
    
    // 更新模型值
    model.value = getClosestEdge(clientX, clientY);
    
    // 计算沿边缘的位置
    const windowWidth = window.innerWidth;
    const windowHeight = window.innerHeight;
    
    switch (model.value) {
      case 'top':
      case 'bottom':
        edgePosition.value = Math.min(1, Math.max(0, clientX / windowWidth));
        break;
      case 'left':
      case 'right':
        edgePosition.value = Math.min(1, Math.max(0, clientY / windowHeight));
        break;
    }
  };
  
  // 停止拖动
  const stopDrag = () => {
    dragging.value = false;
    
    document.removeEventListener('mousemove', handleDrag);
    document.removeEventListener('touchmove', handleDrag);
    document.removeEventListener('mouseup', stopDrag);
    document.removeEventListener('touchend', stopDrag);
  };
  
  // 初始化
  onMounted(() => {
    window.addEventListener('resize', () => {
      edgePosition.value = Math.min(1, Math.max(0, edgePosition.value));
    });
  });
  
  // 清理
  onBeforeUnmount(() => {
    window.removeEventListener('resize', () => {});
    stopDrag();
  });
  </script>