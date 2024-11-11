<template>
    <div ref="resizableDiv" class="resizable-div">
        <div class="content">
            <slot></slot>
        </div>
        <div class="resizer" @mousedown="startResizing"></div>
    </div>
</template>
  
<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

const resizableDiv = ref(null);
const isResizing = ref(false);
let initialWidth = 0;
let initialX = 0;

const startResizing = (e) => {
    isResizing.value = true;
    initialX = e.clientX;
    initialWidth = resizableDiv.value.offsetWidth;
    document.addEventListener('mousemove', resize);
    document.addEventListener('mouseup', stopResizing);
};

const resize = (e) => {
    if (isResizing.value) {
        const newWidth = initialWidth + (e.clientX - initialX);
        resizableDiv.value.style.width = `${newWidth}px`;
    }
};

const stopResizing = () => {
    isResizing.value = false;
    document.removeEventListener('mousemove', resize);
    document.removeEventListener('mouseup', stopResizing);
};

onMounted(() => {
    // 初始化宽度
    resizableDiv.value.style.width = '300px'; // 初始宽度
    // 防止鼠标松开元素仍然可以拖动;
    document.ondragstart = document.ondragend = (ev) => ev.preventDefault()
});

onUnmounted(() => {
    // 清理事件监听器
    document.removeEventListener('mousemove', resize);
    document.removeEventListener('mouseup', stopResizing);
    // 清除防止默认拖动行为的监听器
    document.ondragstart = document.ondragend = null;
});
</script>
  
<style scoped>
.resizable-div {

    min-width: 100px;
    height: 200px;
    border: 1px solid #ccc;
    overflow: hidden;
}

.content {
    padding: 3px;
    box-sizing: border-box;
}

.resizer {
    position: absolute;
    top: 0;
    bottom: 0;
    right: 0;
    width: 4px;
    cursor: ew-resize;
    background-color: #ccc;
}
</style>