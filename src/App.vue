<!--
 * @Autor: simon
 * @Date: 2024-02-23 18:13:52
 * @LastEditors: simon
 * @Description: 
-->
<script setup>
import { ref,onMounted } from 'vue';
import WxSend from './components/WxSend.vue';
import WebRecorder from "./webrecorder.js";

const tXLinkUrl = ref(''); 
function handleGetAsrLink(){
  //调用后端接口获取txLink
}

const recorder = ref(null);//录音器对象
const recordArr = ref([]);
function handleInitAsr(){
  //初始化并将录音数据存储到recordArr数组里并将isListen置为true
  recorder.value = new WebRecorder();
  recorder.value.OnReceivedData = function (res) {
    if (!isListen.value) isListen.value = true;
    recordArr.value.push(res);
  };
  recorder.value.OnError = (err) => {
    console.log(err, 'recode');
    if (!((err + '').indexOf('Cannot read properties of null') > -1)) {
      showToast('未检测到输入!');
    }
    recorder.value.stop();
  };
}

onMounted(() => {
  handleGetAsrLink();//初始化获取腾讯的链接
  handleInitAsr();//初始化录音实例对象
})

const txSocket = ref(null);
const recordInter = ref(null);
function initTXSocket() {
  if (txSocket.value == null) {
    //asr
    txSocket.value = new WebSocket(tXLinkUrl.value);
    let wordArr = [];
    txSocket.value.onmessage = (e) => {
      const data = JSON.parse(e.data);
      if (data.code > 4000) {
        showToast('语音识别失败，请重试!');
        touchCode.value = 'wait';
      }
      if (data.code === 0) {
        if (data.result) {
          wordArr[data.result.index] = data.result.voice_text_str;
          wordArr[data.result.index] = data.result.voice_text_str;
          text.value = wordArr.join('。');
        }
      }
    }
    txSocket.value.onopen = (e) => {
      if (txSocket.value && txSocket.value.readyState === 1) {
        recordInter.value = setInterval(asrLoad, 200);//调用录音数据发送功能
      }
    };
    txSocket.value.onerror = (e) => {
      txSocket.value = null;
      clearTX();
    };
    txSocket.value.onclose = (e) => {
      console.log(e)
      txSocket.value = null;
      if (touchCode.value === 'end-over') {
        if (text.value === '') {
          touchCode.value = 'wait';
          return showToast('未识别到文字！');
        }
        // handleSendQuestion(text.value); //这里跟文本对话的ws进行交互
      }
    };
  }
}

//将录音到的数据发送给wss
const isIos = /iPhone|iPad|iPod/i.test(navigator.userAgent);//判断是不是ios
function asrLoad(){
  if (recordArr.value.length > 0) {
    txSocket.value.send(recordArr.value.shift());
  }
  if (recordArr.value.length === 0 && touchCode.value.includes('end-')) {
    clearInterval(recordInter.value);
    txSocket.value.send(new Int8Array(6450));//置白，避免识别错误
    setTimeout(() => {
      txSocket.value.send(new Int8Array(6450));
    }, 200);
    setTimeout(() => {
      if (isIos) txSocket.value.close();//ios需主动关闭才不会报错
      else txSocket.value.send(JSON.stringify({ type: "end" }));
    }, 400);
  }
}

function clearTx(){
  txSocket.value && txSocket.value.close();
  recordArr.value = [];
  clearInterval(recordInter.value);
  text.value = "";
  touchCode.value = 'wait';
}

function handleStartRecording(){
  //启动录音
}

function handleStartRecording(){
  //关闭录音
}

const touchCode = ref('wait');//状态 noAuth-未获取到录音权限 noRtc-浏览器不支持rtc error-初始化异常 wait-等待 start-录音开始分三种(send,remove,edit) end-录音结束分两种（end-edit,end-over） load-已发送等待回传
const text = ref('');
const isListen = ref(false);
function handleTouchStart(e) {
  if (touchCode.value === 'wait') {
    touchCode.value = 'send';
    handleStartRecording();//启动录音
    initTXSocket();//启动腾讯ws
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
  handleStopRecording();//关闭录音
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
    clearTx();
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
  //这里是对接到文本对话的事件
}
//直接取消
function handleEditCancel(){
  touchCode.value = 'wait';
}
//发送后的处理
function initChat(){
  touchCode.value = 'wait';
  //这里是对接到文本对话的事件
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
