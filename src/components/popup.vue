<template>
  <div>
    <div class="bg"></div>
    <div class="confirm" :class="isShow ? 'confirm-fade-enter-active' : ''">
      <div class="confirm-wrapper">
        <div class="confirm-content" :class="active ? 'active' : ''">
          <div class="flex flex-column u-col-center u-row-center top">
            <div class="flex flex-column u-col-center u-row-center">
              <div class="icon">机器人图标</div>
              <div class="icon-title">你可以说</div>
            </div>
            <div
              class="flex u-col-center u-row-center transfer"
              v-if="endmessage"
            >
              {{ endmessage }}
            </div>
            <div
              class="flex flex-column u-col-center u-row-center transfer"
              v-else
            >
              <div>“卫生间”</div>
              <div>“我要去服务台”</div>
            </div>
          </div>

          <div class="flex u-col-center u-row-center footer">
            <div class="flex-1 left flex u-col-center u-row-center">
              <div
                class="cancel"
                @click="fnEmpty()"
                v-if="!isLongPress && endmessage !== '' && timeOutEvent === 0"
              >
                清空
              </div>
              <div
                class="cancel"
                @click="fnCancel()"
                v-if="!isLongPress && endmessage === '' && timeOutEvent === 0"
              >
                取消
              </div>
              <div
                class="voiceprint flex u-col-center u-row-center" :style="'height:'+voice*10+'px'"
                v-if="isLongPress || timeOutEvent !== 0"
              >
               <div class="yinjie one" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie two" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie three" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie four" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie five" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie six" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie seven" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie six" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie five" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie four" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie three" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie two" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie one" :style="'height:'+voice*10+'px'"></div>
              </div>
            </div>

            <div class="btn flex flex-column u-col-center u-row-center">
              <div class="longpress">{{ longPressText }}</div>
              <div class="sound-btn" :class="isBtn ? 'active' : ''"></div>
              <div
                class="sound-recording"
                @touchstart="onVoiceStart"
                @touchend="onVoiceEnd"
              >
                录音按钮图片
              </div>
            </div>

            <div class="flex-1 right flex u-col-center u-row-center">
              <div
                class="voiceprint flex u-col-center u-row-center" 
                v-if="isLongPress || timeOutEvent !== 0"
              >
                <div class="yinjie one" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie two" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie three" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie four" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie five" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie six" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie seven" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie six" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie five" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie four" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie three" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie two" :style="'height:'+voice*10+'px'"></div>
                <div class="yinjie one" :style="'height:'+voice*10+'px'"></div>
              </div>
              <div
                class="cancel"
                v-if="!isLongPress && endmessage === ''"
              ></div>
              <div
                class="cancel"
                v-if="!isLongPress && endmessage !== '' && timeOutEvent === 0"
                @click="fnConfirm()"
              >
                确定
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { IatRecorder } from "@/config/voice.js";
export default {
  name: "popupShow",
  props: {
    msg: String,
  },
  data() {
    return {
      isShow: false,
      active: false,
      iatRecorder3: null,
      longPressText: "长按说话识别文字",
      endmessage: "",
      isLongPress: false,
      timeOutEvent: 0,
      isBtn: false,
      voice:2,
    };
  },
  created() {
    this.iatRecorder3 = new IatRecorder();
    this.myData();
    // this.isShow = true;
    if (this.timeOutEvent !== 0) {
      clearTimeout(this.timeOutEvent); //清除定时器
      this.timeOutEvent = 0;
    }
  },
  methods: {
    myData() {
      // 监听识别结果的变化
      let that = this;
      this.iatRecorder3.onTextChange = function (text) {
        if (text === "") {
          return;
        }
        let data = text;
        that.endmessage = that.endmessage + data;
      };
      this.iatRecorder3.onVoiceChange= function (voice) {
        console.log(voice)
        if (voice === "") {
          return;
        }
        that.voice = voice
      };
    },
    fnEmpty() {
      this.endmessage = "";
    },
    fnConfirm() {
      const _this = this;
      _this.$emit("confirmClick", _this.endmessage);
      _this.fnCancel();
      setTimeout(function () {
        _this.endmessage = "";
      }, 200);
    },
    fnCancel() {
      const _this = this;
      _this.active = true;
      setTimeout(function () {
        _this.$emit("cancelClick", 1);
      }, 300);
    },
    onVoiceStart() {
      let that = this;
      that.timeOutEvent = setTimeout(() => {
        this.isBtn = true;
        this.longPressText = "松开结束";
        that.iatRecorder3.start();
      }, 300);
    },
    onVoiceEnd() {
      clearTimeout(this.timeOutEvent); //清除定时器
      this.timeOutEvent = 0;
      this.longPressText = "长按说话识别文字";
      this.iatRecorder3.stop();
      this.isBtn = false;
    },
  },
};
</script>

<style scoped>
.bg {
  position: absolute;
  z-index: 10;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  background: rgba(0, 0, 0, 0.5);
}

.confirm.confirm-fade-enter-active {
  animation: confirm-fadein 0.3s;
}

.confirm .confirm-content {
  height: 100%;
  background-image: linear-gradient(#99ccff, #00ffcc);
}

.confirm .confirm-content {
  animation: confirm-zoom 0.3s;
}

.confirm .confirm-content.active {
  -moz-animation: myfirst 0.4s ease-in-out;
  -webkit-animation: myfirst 0.4s ease-in-out;
  -o-animation: myfirst 0.4s ease-in-out;
  animation: myfirst 0.4s ease-in-out;
}

.confirm .confirm-wrapper {
  position: fixed;
  bottom: 0px;
  left: 0;
  right: 0;
  width: 100%;
  height: 90%;
  z-index: 999;
}

@keyframes confirm-fadein {
  0% {
    opacity: 0;
  }

  100% {
    opacity: 1;
  }
}

@keyframes confirm-zoom {
  0% {
    transform: translateY(90%);
  }

  100% {
    transform: translateY(0%);
  }
}

@keyframes myfirst {
  0% {
    transform: translateY(0);
  }

  100% {
    transform: translateY(90%);
  }
}

.flex {
  display: -webkit-flex;
  display: flex;
}

.flex-column {
  -webkit-box-orient: vertical;
  -webkit-flex-flow: column;
  flex-flow: column;
}

.flex-1 {
  flex: 1;
}

.u-col-center {
  align-items: center;
}

.u-row-center {
  justify-content: center;
}

.u-flex-wrap {
  flex-wrap: wrap;
}

.u-row-around {
  justify-content: space-around;
}

.top {
  padding-top: 30px;
}

.icon {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: #000;
  color: #fff;
  font-size: 12px;
}

.icon-title {
  font-size: 14px;
  color: #ccccff;
  text-align: center;
}

.transfer {
  margin-top: 20px;
  font-size: 18px;
  color: #fff;
  text-align: center;
}

.footer {
  position: fixed;
  bottom: 60px;
  left: 0;
  right: 0;
}

.voiceprint {
  width: 100%;
}

.yinjie {
  width: 4px;
  height: 60px;
  margin: 0 5px;
  border-radius: 5px;
}

@keyframes tiao {
  0% {
    transform: scale(1, 1);
    background-color: blue;
  }
  100% {
    transform: scale(1, 0.2);
    background-color: red;
  }
}

.one {
  animation: tiao 0.5s 2s linear infinite alternate;
}

.two {
  animation: tiao 0.5s 1.8s linear infinite alternate;
}

.three {
  animation: tiao 0.5s 1.6s linear infinite alternate;
}

.four {
  animation: tiao 0.5s 1.4s linear infinite alternate;
}

.five {
  animation: tiao 0.5s 1.2s linear infinite alternate;
}

.six {
  animation: tiao 0.5s 1s linear infinite alternate;
}

.seven {
  animation: tiao 0.4s linear infinite alternate;
}

.cancel {
  font-size: 16px;
  color: #fff;
}

.btn {
  text-align: center;
  position: relative;
  z-index: 99;
  margin: 0 20px;
}

.left {
  height: 60px;
}

.right {
  height: 60px;
}

.longpress {
  position: absolute;
  z-index: 10;
  top: -50px;
  width: 300px;
  font-size: 16px;
  color: #fff;
}

.sound-recording {
  position: relative;
  z-index: 99;
  width: 60px;
  height: 60px;
  background-color: #000;
  border-radius: 50%;
  text-align: center;
  color: #fff;
}

.sound-btn.active::before,
.sound-btn.active::after {
  position: absolute;
  z-index: 1;
  left: 20px;
  bottom: 20px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  content: "";
  background-color: #000;
}

.sound-btn.active::before {
  animation: scale 2s infinite;
}

@keyframes scale {
  0% {
    transform: scale(1);
    opacity: 0.9;
  }

  100% {
    transform: scale(6);
    opacity: 0;
  }
}
</style>
