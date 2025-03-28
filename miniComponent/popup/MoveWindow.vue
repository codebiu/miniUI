<template>
  <!-- 可拖拽的固定定位容器 -->
  <div fixed ref="moveDom">
    <!-- 拖拽标题区域 -->
    <div 
      class="con1_title" 
      @mousedown="startDrag($event)" 
      @touchstart.passive="startDrag($event)"
    >
      <!-- 默认插槽，用于放置标题内容 -->
      <slot></slot>
    </div>
    <!-- 内容插槽 -->
    <slot name="content"></slot>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue'

// 定义组件属性
const props = defineProps<{
  isCenter?: boolean // 是否居中显示
}>()

// 获取DOM引用
const moveDom = ref<HTMLElement | null>(null)
// 存储清理函数的数组
let cleanupFunctions: (() => void)[] = []

/**
 * 将元素居中显示
 */
const centerElement = () => {
  if (!moveDom.value) return
  
  // 获取窗口尺寸
  const { innerWidth, innerHeight } = window
  // 获取元素尺寸
  const { offsetWidth, offsetHeight } = moveDom.value
  
  // 计算并设置居中位置
  moveDom.value.style.right = `${(innerWidth - offsetWidth) / 2}px`
  moveDom.value.style.bottom = `${(innerHeight - offsetHeight) / 2}px`
}

/**
 * 获取事件坐标（兼容鼠标和触摸事件）
 * @param e 鼠标或触摸事件
 * @returns 坐标对象 {x, y}
 */
const getCoordinates = (e: MouseEvent | TouchEvent) => {
  if (e instanceof MouseEvent) {
    return { x: e.clientX, y: e.clientY }
  }
  return { 
    x: e.targetTouches[0].pageX, 
    y: e.targetTouches[0].pageY 
  }
}

/**
 * 开始拖拽处理函数
 * @param e 鼠标或触摸事件
 */
const startDrag = (e: MouseEvent | TouchEvent) => {
  if (!moveDom.value) return
  
  const el: any = moveDom.value
  // 初始化拖拽标记
  el.isMoving = false
  
  // 获取元素尺寸
  const { offsetWidth: domW, offsetHeight: domH } = el
  // 获取窗口尺寸
  const { innerWidth: width, innerHeight: height } = window
  // 获取起始坐标
  const { x: startX, y: startY } = getCoordinates(e)
  
  // 计算鼠标/手指相对于元素左上角的偏移量
  const offsetX = startX - el.offsetLeft
  const offsetY = startY - el.offsetTop
  
  /**
   * 处理拖拽移动
   * @param e 鼠标或触摸事件
   */
  const handleMove = (e: MouseEvent | TouchEvent) => {
    if (!el) return
    
    // 设置拖拽标记
    el.isMoving = true
    // 获取当前坐标
    const { x, y } = getCoordinates(e)
    
    // 计算新位置
    let newRight = width - (x - offsetX) - domW
    let newBottom = height - (y - offsetY) - domH
    
    // 限制在窗口范围内
    newRight = Math.max(0, Math.min(newRight, width - domW))
    newBottom = Math.max(0, Math.min(newBottom, height - domH))
    
    // 应用新位置
    el.style.right = `${newRight}px`
    el.style.bottom = `${newBottom}px`
  }
  
  /**
   * 清理鼠标事件
   */
  const cleanupMouse = () => {
    document.removeEventListener('mousemove', handleMove)
    document.removeEventListener('mouseup', cleanupMouse)
  }
  
  /**
   * 清理触摸事件
   */
  const cleanupTouch = () => {
    document.removeEventListener('touchmove', handleMove)
    document.removeEventListener('touchend', cleanupTouch)
    el.isMoving = false
  }
  
  // 阻止默认拖拽行为
  const preventDefaultDrag = (e: Event) => e.preventDefault()
  
  // 根据事件类型添加相应监听器
  if (e instanceof MouseEvent) {
    document.addEventListener('mousemove', handleMove)
    document.addEventListener('mouseup', cleanupMouse)
    cleanupFunctions.push(cleanupMouse)
  } else {
    // 触摸事件使用passive:false确保可以阻止默认行为
    document.addEventListener('touchmove', handleMove, { passive: false })
    document.addEventListener('touchend', cleanupTouch)
    cleanupFunctions.push(() => {
      document.removeEventListener('touchmove', handleMove)
      document.removeEventListener('touchend', cleanupTouch)
    })
  }
  
  // 添加阻止默认拖拽行为的监听器
  document.addEventListener('dragstart', preventDefaultDrag)
  document.addEventListener('dragend', preventDefaultDrag)
  cleanupFunctions.push(() => {
    document.removeEventListener('dragstart', preventDefaultDrag)
    document.removeEventListener('dragend', preventDefaultDrag)
  })
}

// 组件挂载后执行
onMounted(() => {
  // 如果需要居中显示
  if (props.isCenter) {
    centerElement()
  }
})

// 组件卸载前执行
onBeforeUnmount(() => {
  // 执行所有清理函数
  cleanupFunctions.forEach(fn => fn())
  cleanupFunctions = []
})
</script>

<style scoped>
/* 拖拽标题区域样式 */
.con1_title {
  cursor: move; /* 鼠标指针样式 */
  user-select: none; /* 禁止文本选择 */
  touch-action: none; /* 禁用触摸默认行为 */
}
</style>