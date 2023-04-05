<script setup>
import bf from '../assets/bf.png'
import zt from '../assets/zt.png'
import jingyin_icon from '../assets/jingyin_icon.png'
import yinliang_icon from '../assets/yinliang_icon.png'
import Progress from './progress.vue'
import {reactive, ref, onMounted, getCurrentInstance, onActivated, watch} from 'vue'

const dataVal = reactive({
  isListen: false,
  positionY: 0,
  voice: 100,
  total: 0.00,
})

const audio = ref()
const percentage = ref(0)


//播放还是暂停

let timer = null

const changePause = () => {
  if (dataVal.isListen) {
    audio.value.pause(); //暂停
  } else {
    audio.value.play(); //播放
    if (timer) {
      clearTimeout(timer)
    } else {
      setInterval(() => {
        percentage.value = (audio.value.currentTime / audio.value.duration * 100).toFixed(2)
      }, 100)
    }
  }
  dataVal.isListen = !dataVal.isListen
  dataVal.total = audio.value.duration.toFixed(2) //总时间
}
const progressChange = (val) => {
  dataVal.total = audio.value.duration.toFixed(2) //总时间
  audio.value.currentTime = audio.value.duration * val / 100
  audio.value.play(); //播放.
  dataVal.isListen = true
}
const voiceChange = (val) => {
  audio.value.volume = val / 100
}
const changeVoiceBtn = () => {
  dataVal.voice = dataVal.voice > 0 ? 0 : 100
  audio.value.volume = dataVal.voice / 100
}
</script>

<template>
  <audio controls ref="audio" style="display: none">
    <source src="../assets/music.mp3"/>
  </audio>

  <div class="container">
    <div class="control">
      <div>
        <img :src="bf" v-if="dataVal.isListen" @click="changePause"/> <img :src="zt" v-else @click="changePause"/>
      </div>
      <div style="height: 200px;display: flex;flex-direction: column;align-items: center">
        <div>{{ dataVal.voice }}%</div>
        <a-slider vertical v-model:value="dataVal.voice" :tip-formatter="null" @change="voiceChange"/>
        <img :src="jingyin_icon" v-if="dataVal.voice === 0" @click="changeVoiceBtn"/>
        <img :src="yinliang_icon" v-else @click="changeVoiceBtn"/>
      </div>
    </div>
    <a-slider v-model:value="percentage" :tip-formatter="null" @change="progressChange"/>
    <div>{{ (dataVal.total * percentage / 100).toFixed(2) }}s/{{ dataVal.total }}s</div>
  </div>
  <!--<Progress style="margin-top: 100px" @valueChange="progressChange" :percentage="percentage" activeColor="#3F50F2" bgColor="#77737d"/>-->
</template>

<style scoped>
.container {
  background: pink;
  width: 80%;
  height: 400px;
  margin: 0 auto;
  padding: 30px;
}

.control {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  height: 80%;
}
</style>
