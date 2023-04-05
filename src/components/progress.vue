<template>
  <div class="progress-bar" @click="clickProgress" ref="progressBar" :style="{background:props.bgColor}">
    <div class="progress" :style="{ width: progressWidth,background:props.activeColor }"></div>
    <div class="progress-handle" @mousedown="startDrag" :style="{ background:props.activeColor }">{{ progress }}</div>
  </div>
</template>

<script setup>
import {ref, computed, watch, getCurrentInstance} from 'vue';

const {proxy} = getCurrentInstance()
const props = defineProps({
  // 背景色
  bgColor: {
    type: String,
    default: '#f0f0f0'
  },
  // 前景色
  activeColor: {
    type: String,
    default: '#4caf50'
  },
  //当前进度
  percentage: {
    type: Number,
    default: 0
  }
})

const progress = ref(0);
const progressBar = ref(null);
let dragging = false;
let dragStartX = 0;


watch(
    () => [progress, props.percentage],
    ([progress, percentage]) => {
      if (percentage) {
        // progress.value = percentage.toFixed(2)
      }
    },
    {deep: true}
)

const updateProgress = (clientX) => {
  const progressBarRect = progressBar.value.getBoundingClientRect();
  const progressWidthPercentage = ((clientX - progressBarRect.left) / progressBarRect.width) * 100;
  progress.value = Math.max(0, Math.min(progressWidthPercentage, 100)).toFixed(2);
  proxy.$emit('valueChange', progress.value)
};

const startDrag = (event) => {
  dragging = true;
  dragStartX = event.clientX;
  document.addEventListener('mousemove', handleDrag);
  document.addEventListener('mouseup', endDrag);
};

const handleDrag = (event) => {
  if (dragging) {
    updateProgress(event.clientX);
  }
};

const endDrag = () => {
  dragging = false;
  document.removeEventListener('mousemove', handleDrag);
  document.removeEventListener('mouseup', endDrag);
};

const clickProgress = (event) => {
  updateProgress(event.clientX);
};

const progressWidth = computed(() => {
  return progress.value + '%';
});

</script>

<style scoped>
.progress-bar {
  width: 99%;
  height: 20px;
  border-radius: 10px;
  display: flex;
  justify-content: start;
  position: relative;
  cursor: pointer;
}

.progress {
  height: 100%;
  border-radius: 10px;
  transition: width 0.2s;
}

.progress-handle {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  margin-top: -10px;
  margin-left: -10px;
  line-height: 40px;
  color: #fff;
  text-align: center;
}
</style>
