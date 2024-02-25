<!--
 * @Autor: simon
 * @Date: 2024-01-15 15:37:44
 * @LastEditors: simon
 * @Description: 
 *  props
  *  touchCode 标识整体状态   string  状态 noAuth-未获取到录音权限 noRtc-浏览器不支持rtc error-初始化异常 wait-等待 start-录音开始分三种(send,remove,edit) end-录音结束分两种（end-edit,end-over） load-已发送等待回传
  *  isListen  麦克风状态     bool 
  *  text      识别到的文本   string 
 *  emit
  *  handleTouchStart  长按启动事件                父组件变更touchCode值
  *  initChat          输入框发送文本事件           @params text 输入框文本
  *  handleChangeText  识别文本后的编辑识别后文本    @params value 
  *  handleEditSend    识别文本并编辑后在进行发送
  *  handleEditCancel  识别文本并编辑然后取消
 *
 *父组件中需加入下面代码

  //父组件handleTouchStart
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
    }
    if (touchCode.value === 'remove') {
      /** 移除处理 **/
      
    }
    if (touchCode.value === 'send') {
      touchCode.value = 'end-over';
    }
  }
-->
<template>
  <div :style="{ visibility: touchCode === 'end-edit' ? 'hidden' : '' }"
    class="absolute bottom-0 left-0 w-[100%] min-h-[52px] bg-[linear-gradient(180deg,#FCE6D1_0%,#FBD7B5_100%)] flex items-end justify-center"
    :class="{ 'disabled-all': touchCode === 'load' || touchCode === 'end-over' || touchCode === 'error' || touchCode === 'noRtc' }">
    <div v-if="touchCode === 'noRtc'" class="absolute top-[0] left-[0] z-[100] w-[100%] h-[100%] flex items-center justify-center text-[red]">您当前浏览器不支持Rtc播放对话</div>
    <div class="w-[52px] h-[52px] flex items-center justify-center" @click="handleChangeDiaType()">
      <svg v-if="diaType" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="28"
        height="28" viewBox="0 0 28 28">
        <defs>
          <linearGradient id="a" x1="0.5" x2="0.5" y2="1" gradientUnits="objectBoundingBox">
            <stop offset="0" stop-color="#fc462f" />
            <stop offset="1" stop-color="#d53629" />
          </linearGradient>
        </defs>
        <g transform="translate(-13 -700)">
          <path style="fill:url(#a);"
            d="M2.892,26.415a1.137,1.137,0,0,1,0-2.272H7.921V22.652A9.026,9.026,0,0,1,0,13.7V10.282A1.109,1.109,0,0,1,1.08,9.146a1.109,1.109,0,0,1,1.08,1.136V13.7a6.841,6.841,0,0,0,13.681,0V10.282a1.081,1.081,0,1,1,2.159,0V13.7a9.025,9.025,0,0,1-7.92,8.954v1.491h5.029a1.137,1.137,0,0,1,0,2.272ZM3.4,12.889v-7A5.756,5.756,0,0,1,9,0a5.756,5.756,0,0,1,5.6,5.891v7A5.756,5.756,0,0,1,9,18.779,5.756,5.756,0,0,1,3.4,12.889Z"
            transform="translate(18 701)" />
          <g style="fill:none;stroke:#707070;opacity:0;" transform="translate(13 700)">
            <rect style="stroke:none;" width="28" height="28" />
            <rect style="fill:none;" x="0.5" y="0.5" width="27" height="27" />
          </g>
        </g>
      </svg>
      <svg v-else xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="28" height="28"
        viewBox="0 0 28 28">
        <defs>
          <linearGradient id="b" x1="0.5" x2="0.5" y2="1" gradientUnits="objectBoundingBox">
            <stop offset="0" stop-color="#fc462f" />
            <stop offset="1" stop-color="#d53629" />
          </linearGradient>
        </defs>
        <g transform="translate(-13 -700)">
          <g style="fill:none;stroke:#707070;opacity:0;" transform="translate(13 700)">
            <rect style="stroke:none;" width="28" height="28" />
            <rect style="fill:none;" x="0.5" y="0.5" width="27" height="27" />
          </g>
          <path style="fill:url(#b);"
            d="M-9567.931-7267.613a3.558,3.558,0,0,1-3.48-3.622v-12.873a3.558,3.558,0,0,1,3.48-3.622h20.413a3.558,3.558,0,0,1,3.48,3.622v12.873a3.558,3.558,0,0,1-3.48,3.622Zm-2.03-16.5v12.873a2.109,2.109,0,0,0,2.03,2.171h20.413a2.107,2.107,0,0,0,2.029-2.171v-12.873a2.1,2.1,0,0,0-2.029-2.169h-20.413A2.107,2.107,0,0,0-9569.961-7284.108Zm6.678,12.3v-1.588h11.117v1.588Zm10.685-4.093v-2.637h2.641v2.637Zm-4.295,0v-2.637h2.638v2.637Zm-4.3,0v-2.637h2.641v2.637Zm-4.295,0v-2.637h2.638v2.637Zm15.04-4.653v-2.637h2.638v2.637Zm-4.3,0v-2.637h2.638v2.637Zm-4.3,0v-2.637h2.638v2.637Zm-4.3,0v-2.637h2.638v2.637Zm-4.3,0v-2.637h2.64v2.637Z"
            transform="translate(9584.723 7991.671)" />
        </g>
      </svg>
    </div>
    <div class="w-[100%] min-h-[52px] h-[auto] px-[6px] pt-[6px] pb-[6px] flex items-end">
      <template v-if="diaType">
        <div
          class="w-[calc(100%_-_40px)] relative text-[18px] min-h-[40px] bg-[#fefefe] rounded-[6px] rounded-r-none overflow-y-scroll p-[6px] text-wrap"
          @touchmove.stop>
          <span style="visibility:hidden">{{ inputText }}</span>
          <textarea ref="textarea" v-model="inputText"
            class="absolute left-0 top-0 w-[100%] h-[100%] p-[6px] resize-none bg-transparent"
            @keydown.enter.prevent="handleSendInputText" @blur="handleBlur"></textarea>
        </div>
        <div
          class="absolute top-[6px] right-[6px] w-[40px] h-[calc(100%_-_12px)] min-h-[40px] bg-[#FFFAF3] rounded-r-[6px] flex items-center justify-center"
          @click="handleSendInputText">
          <svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" viewBox="0 0 22 22">
            <path :style="{fill:inputText.length>0?'#D73A36':'#ffafa5'}"
              d="M571.023,249.726l-20.477,6.99a.761.761,0,0,0-.292,1.258l5.518,5.518,8.118-5.684-5.675,8.127,5.526,5.526a.761.761,0,0,0,1.258-.292l6.99-20.477A.761.761,0,0,0,571.023,249.726Z"
              transform="translate(-550.031 -249.683)" />
          </svg>
        </div>
      </template>
      <div v-else class="w-[100%] h-[40px] bg-white rounded-[6px] flex justify-center items-center text-[#D73A36]"
        @touchstart="touchCode !== 'noAuth' && handleTouchStart()">
        <span v-if="touchCode === 'noAuth'">
          无录音权限
        </span>
        <template v-else>
          <span class="mr-[6px] pointer-events-none">按住</span>
          <span class="pointer-events-none">说话</span>
        </template>
      </div>
    </div>
  </div>
  <van-overlay :show="['send', 'remove', 'edit', 'end-edit'].includes(touchCode)">
    <div class="w-[100%] h-[100%] relative no-page">
      <div class="w-[100%] h-[calc(100%_-_100px)] flex relative">
        <div class="w-[50%] h-[100%] flex items-end justify-center pb-[26px]"
          :class="{ 'pr-[6vw]': touchCode !== 'end-edit' }" behavior="remove">
          <div class="pointer-events-none z-[-1] w-[100px] h-[90px] flex justify-center items-center relative"
            :class="{ 'flex-col': touchCode === 'end-edit', 'z-[1]': touchCode === 'end-edit', 'pointer-events-auto': touchCode === 'end-edit' }"
            @click="handleEditCancel">
            <template v-if="touchCode !== 'end-edit'">
              <div v-show="touchCode === 'remove'"
                class="absolute top-[-32px] text-[#FFDCAD] w-[100%] flex items-center text-[14px] justify-center pointer-events-none select-none">
                <span class="mr-[4px]">松开</span>
                <span>取消</span>
              </div>
              <div
                class="w-[56px] h-[56px] rounded-[50%] bg-[rgba(254,242,230,0.15)] transition-all flex items-center justify-center"
                :class="{ 'scale-[1.2]': touchCode === 'remove', 'bg-[#FEF2E6_!important]': touchCode === 'remove' }">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 15.999">
                  <path :style="{ fill: touchCode === 'remove' ? '#6C3600' : '#ffdcad' }"
                    d="M195.309,117.267l4.433-4.433a2.09,2.09,0,1,0-2.956-2.955l-4.432,4.433-4.433-4.433a2.09,2.09,0,0,0-2.956,2.955l4.433,4.433-4.433,4.433a2.09,2.09,0,0,0,2.956,2.955l4.433-4.432,4.432,4.432a2.09,2.09,0,1,0,2.956-2.955Z"
                    transform="translate(-184.354 -109.267)" />
                </svg>
              </div>
            </template>
            <template v-else>
              <svg t="1705031481730" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
                p-id="12824" width="28" height="28">
                <path style="fill:#FFDCAD"
                  d="M465.547636 528.337455a38.912 38.912 0 0 1-54.970181 0L242.176 360.215273a18.618182 18.618182 0 0 1 0-26.344728L410.530909 165.515636a38.912 38.912 0 0 1 55.016727 54.923637L387.490909 298.496h237.568c149.410909 0 270.894545 119.016727 274.850909 267.310545l0.093091 7.447273a274.850909 274.850909 0 0 1-274.897454 274.757818H182.877091a38.865455 38.865455 0 0 1 0-77.730909h442.181818a197.026909 197.026909 0 0 0 197.12-197.026909 197.026909 197.026909 0 0 0-197.073454-196.980363H368.314182l97.233454 97.093818a38.865455 38.865455 0 0 1 0 54.970182z"
                  fill="#333333" p-id="12825"></path>
              </svg>
              <div class="text-[16px] text-[#FFDCAD] mt-[4px]">取消</div>
            </template>
          </div>
        </div>
        <div class="w-[50%] h-[100%] flex items-end justify-center pb-[26px] pl-[6vw]" behavior="edit">
          <div class="pointer-events-none z-[-1] w-[100px] h-[90px] flex justify-center items-center relative"
            :class="{ 'z-[1]': touchCode === 'end-edit' }">
            <div v-show="touchCode === 'edit'"
              class="absolute top-[-32px] text-[#FFDCAD] w-[100%] flex items-center text-[14px] justify-center pointer-events-none select-none">
              <span>编辑识别文本</span>
            </div>
            <div
              class="w-[56px] h-[56px] rounded-[50%] bg-[rgba(254,242,230,0.15)] transition-all flex items-center justify-center"
              :class="{ 'scale-[1.2]': touchCode.includes('edit'), 'bg-[#FEF2E6_!important]': touchCode.includes('edit'), 'pointer-events-auto': touchCode === 'end-edit' }"
              @click="touchCode === 'end-edit' && handleEditSend()">
              <svg v-if="touchCode === 'end-edit'" xmlns="http://www.w3.org/2000/svg" width="17" height="16"
                viewBox="0 0 17.022 16">
                <g transform="translate(-299.489 -586)">
                  <path
                    style="fill:none;stroke:#6c3600;stroke-linecap:round;stroke-linejoin:round;stroke-miterlimit:10;stroke-width:4px;"
                    d="M3527.05,77.552l4.055,4.055,7.31-7.31" transform="translate(-3224.732 516.203)" />
                  <g style="fill:none;stroke:#707070;opacity:0;" transform="translate(300 586)">
                    <rect style="stroke:none;" width="16" height="16" />
                    <rect style="fill:none;" x="0.5" y="0.5" width="15" height="15" />
                  </g>
                </g>
              </svg>
              <svg v-else xmlns="http://www.w3.org/2000/svg" width="16" height="16.001" viewBox="0 0 16 16.001">
                <path :style="{ fill: touchCode === 'edit' ? '#6C3600' : '#ffdcad' }"
                  d="M410.329,951.932l-10.686,10.685a.984.984,0,0,0-.282.6l-.326,3.51a.851.851,0,0,0,.837.917c.025,0,.049,0,.074,0l3.511-.326a.971.971,0,0,0,.6-.283l10.685-10.685a.98.98,0,0,0,0-1.385l-3.031-3.03A.977.977,0,0,0,410.329,951.932Z"
                  transform="translate(-399.031 -951.645)" />
              </svg>
            </div>
          </div>
        </div>
        <div class="absolute bottom-[200px] rounded-[16px] transition-all min-h-[100px] p-[12px]"
          :style="{ background: isListen ? (touchCode === 'remove' ? '#F45E59' : '#FEF2E6') : '#FDC368', left: touchCode === 'remove' ? '8vw' : '3vw', width: touchCode === 'remove' ? '30vw' : '94vw' }">
          <div v-show="touchCode !== 'remove'" class="relative text-[18px] min-h-[100px] max-h-[42vh] overflow-y-scroll"
            @touchmove.stop>
            <span :style="{ visibility: touchCode === 'end-edit' ? 'hidden' : '' }">{{ isListen ? text : '等待录音器启动中...'
            }}</span>
            <textarea v-show="touchCode === 'end-edit'" :value="text" @input="handleChangeText"
              class="absolute left-0 top-0 w-[100%] h-[100%] resize-none bg-transparent"></textarea>
          </div>
        </div>
      </div>
      <div :style="{ visibility: touchCode === 'end-edit' ? 'hidden' : '' }"
        class="absolute bottom-0 left-0 w-[100%] h-[100px] bg-[#777] send-rounded transition-all flex flex-col justify-center items-center"
        behavior="send" :class="{ 'scale-[1.14]': touchCode === 'send', 'bg-[#FEF2E6]': touchCode === 'send' }">
        <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="28" height="28"
          viewBox="0 0 28 28">
          <defs>
            <linearGradient id="c" x1="0.5" x2="0.5" y2="1" gradientUnits="objectBoundingBox">
              <stop offset="0" stop-color="#fc462f" />
              <stop offset="1" stop-color="#d53629" />
            </linearGradient>
          </defs>
          <g transform="translate(-13 -700)">
            <path :style="{ fill: touchCode === 'send' ? 'url(#c)' : '#FFDCAD' }"
              d="M2.892,26.415a1.137,1.137,0,0,1,0-2.272H7.921V22.652A9.026,9.026,0,0,1,0,13.7V10.282A1.109,1.109,0,0,1,1.08,9.146a1.109,1.109,0,0,1,1.08,1.136V13.7a6.841,6.841,0,0,0,13.681,0V10.282a1.081,1.081,0,1,1,2.159,0V13.7a9.025,9.025,0,0,1-7.92,8.954v1.491h5.029a1.137,1.137,0,0,1,0,2.272ZM3.4,12.889v-7A5.756,5.756,0,0,1,9,0a5.756,5.756,0,0,1,5.6,5.891v7A5.756,5.756,0,0,1,9,18.779,5.756,5.756,0,0,1,3.4,12.889Z"
              transform="translate(18 701)" />
            <g style="fill:none;stroke:#707070;opacity:0;" transform="translate(13 700)">
              <rect style="stroke:none;" width="28" height="28" />
              <rect style="fill:none;" x="0.5" y="0.5" width="27" height="27" />
            </g>
          </g>
        </svg>
        <div v-show="touchCode === 'send'"
          class="text-[#D73A36] text-[14px] mt-[2px] text-center pointer-events-none z-[-1]">
          <span class="pointer-events-none select-none">松开发送</span>
        </div>
      </div>
    </div>
  </van-overlay>
</template>
<script setup>

import { ref } from 'vue';

const emit = defineEmits(['handleTouchStart', 'initChat', 'handleChangeText', 'handleEditSend', 'handleEditCancel']);
const props = defineProps({
  touchCode: {
    type: String,
    require: true
  },
  isListen: {
    type: Boolean,
    require: true
  },
  text: {
    type: String,
    require: true
  }
})

//textarea
const textarea = ref(null);

function handleChangeText({ target: { value } }) {
  emit('handleChangeText', value);
}

function handleEditSend() {
  emit('handleEditSend');
}

function handleEditCancel() {
  emit('handleEditCancel');
}

// 对话模式
const diaType = ref(true); //true为输入模式 false为语音模式
function handleChangeDiaType() {
  diaType.value = !diaType.value;
}

function handleTouchStart(e) {
  emit('handleTouchStart', e)
}

const inputText = ref('');
//输入框直接发送
function handleSendInputText() {
  if (!inputText.value) return showToast('发送文本不能为空!');
  textarea.value.blur();
  emit('initChat', inputText.value);
  inputText.value = "";
}

//位置复原
function handleBlur(){
  setTimeout(() => {
    window.scrollTo(0, 0);
  }, 0);
}
</script>

<style lang="less" scoped>
.disabled-all {
  pointer-events: none !important;

  * {
    pointer-events: none !important;
  }

  &:after {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.35);
  }
}</style>