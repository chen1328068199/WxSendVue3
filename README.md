```javascript
/*  props
  *  touchCode 标识整体状态   string  状态 noAuth-未获取到录音权限 noRtc-浏览器不支持rtc error-初始化异常 wait-等待 start-录音开始分三种(send,remove,edit) end-录音结束分两种（end-edit,end-over） load-已发送等待回传
  *  isListen  麦克风状态     bool 
  *  text      识别到的文本   string 
 *  emit
  *  handleTouchStart  长按启动事件                父组件变更touchCode值
  *  initChat          输入框发送文本事件           @params text 输入框文本
  *  handleChangeText  识别文本后的编辑识别后文本    @params value 
  *  handleEditSend    识别文本并编辑后在进行发送
  *  handleEditCancel  识别文本并编辑然后取消
*/

  <WxSend :touchCode="touchCode" :text="text" :isListen="isListen" @handleTouchStart="handleTouchStart"
      @handleChangeText="handleChangeText" @handleEditSend="handleEditSend" @handleEditCancel="handleEditCancel"
      @initChat="initChat"/>
```      



```javascript
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
```