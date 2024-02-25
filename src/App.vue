<!--
 * @Autor: simon
 * @Date: 2024-02-23 18:13:52
 * @LastEditors: simon
 * @Description: 
-->
<script setup>
import { ref } from 'vue';
import WxSend from './components/WxSend.vue';

const touchCode = ref('wait');//状态 noAuth-未获取到录音权限 noRtc-浏览器不支持rtc error-初始化异常 wait-等待 start-录音开始分三种(send,remove,edit) end-录音结束分两种（end-edit,end-over） load-已发送等待回传
const text = ref('');
const isListen = ref(false);
function handleTouchStart(e) {
  if (touchCode.value === 'wait') {
    touchCode.value = 'send';
    window.addEventListener('touchend', handleTouchEnd);
    window.addEventListener('touchmove', handleTouchMove);
    window.addEventListener('touchcancel', handleTouchEnd);
    //****其他start处理****//

  }
}
function handleTouchMove(e) {
  let touch = e.touches[0] || e.changedTouches[0];
  let element = document.elementFromPoint(touch.clientX, touch.clientY);
  touchCode.value = element.getAttribute('behavior') || 'send';//设置
}
function handleTouchEnd(e) {
  window.removeEventListener('touchend', handleTouchEnd);
  window.removeEventListener('touchmove', handleTouchMove);
  window.removeEventListener('touchcancel', handleTouchEnd);
  if (touchCode.value === 'edit') {
    touchCode.value = 'end-edit';
    setTimeout(() => {
      touchCode.value = 'wait';
    }, 100000);
  }
  if (touchCode.value === 'remove') {
    touchCode.value = 'wait';
    /** 移除处理 **/

  }
  if (touchCode.value === 'send') {
    touchCode.value = 'end-over';
    setTimeout(() => {
      touchCode.value = 'wait';
    }, 100000);
  }
}

//编辑后发送文本
function handleEditSend(){
  touchCode.value = 'wait';
}
//直接取消
function handleEditCancel(){
  touchCode.value = 'wait';
}
//发送后的处理
function initChat(){
  touchCode.value = 'wait';
}
//修改识别后文本
function handleChangeText(val) {
  text.value = val;
}

</script>

<template>
  <WxSend :touchCode="touchCode" :text="text" :isListen="isListen" @handleTouchStart="handleTouchStart"
      @handleChangeText="handleChangeText" @handleEditSend="handleEditSend" @handleEditCancel="handleEditCancel"
      @initChat="initChat"/>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
