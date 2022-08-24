<template>
  <div>
    <div class="flex">
      <div>搜索</div>
      <div class="flex-1">
        <input class="input" v-model="endmessage" type="text" />
      </div>
      <div @click="fnOpenClick">
        <bottom>语音搜索</bottom>
      </div>
    </div>

    <!-- <div
      @click="onVoiceClick"
      @touchstart="onVoiceStart"
      @touchend="onVoiceEnd"
    >
      预备开始
    </div>
    <div>{{ endmessage }} {{ num > 0 ? num : "" }}</div> -->

    <popupShow
      v-if="isShowPopup"
      @cancelClick="fnClickCancel"
      @confirmClick="fnClickConfirm"
    />
  </div>
</template>

<script>
import { IatRecorder } from "@/config/index.js";
import popupShow from "./components/popup.vue";
export default {
  name: "App",
  components: {
    popupShow,
  },
  data() {
    return {
      endmessage: "",
      num: 0,
      iatRecorder3: null,
      mySecond: 0,
      timeOutEvent: 0,
      isShowPopup: false,
    };
  },
  mounted() {
    //初始化语音
    //这里用一个iatRecorder3接是为了保持当前vue既用到了语音 又可以有自己的私有方法
    this.iatRecorder3 = new IatRecorder();
    // //  接收语音转文字结果
    this.myData();
    // this.audioContext = window.AudioContext || window.webkitAudioContext;
  },
  methods: {
    myData() {
      // let countInterval;
      // 状态改变时处罚
      this.iatRecorder3.onWillStatusChange = function (oldStatus, status) {
        console.log(oldStatus + "******" + status);
        // 可以在这里进行页面中一些交互逻辑处理：倒计时（听写只有60s）,录音的动画，按钮交互等
        // let senconds = 0;
        // if (status === "ing") {
        //   console.log("ing");
        //   // 倒计时相关
        //   countInterval = setInterval(() => {
        //     senconds++;
        //     if (senconds >= 60) {
        //       this.stop();
        //       clearInterval(countInterval);
        //     }
        //   }, 1000);
        // } else if (status === "init") {
        //   console.log("init");
        // } else {
        //   clearInterval(countInterval);
        // }
      };
      // 监听识别结果的变化
      let that = this;
      this.iatRecorder3.onTextChange = function (text) {
        //转文字结果是text 然后onSearch是之后的操作可根据自己情况修改
        //注意！！这个方法不断会有新的翻译文字过来不是一锤子买卖O(∩_∩)O哈哈~
        if (text != "") {
          return;
        }

        that.endmessage = text;
      };
    },

    onVoiceClick() {},
    /**
     * 语音输入开始
     */
    onVoiceStart() {
      let that = this;
      clearTimeout(this.timeOutEvent); //清除定时器
      this.timeOutEvent = 0;
      this.timeOutEvent = setTimeout(function () {
        console.log(that.iatRecorder3.status + "*****");
        //执行长按要执行的内容，
        if (that.iatRecorder3.status === "ing") {
          that.iatRecorder3.stop();
        } else {
          that.iatRecorder3.start();
        }
      }, 150);
      // this.myToast = Toast.loading({
      //   duration: 0,
      //   forbidClick: true,
      //   message: `请讲话\n ${this.mySecond}s`,
      // });
      // this.myTimer = setInterval(() => {
      //   this.mySecond += 1;
      //   console.log("请讲话" + this.mySecond + "s");
      //   // this.myToast.message = `请讲话\n ${this.mySecond}s`;
      // }, 1000);
    },

    /**
     * 语音输入结束
     */
    onVoiceEnd() {
      clearTimeout(this.timeOutEvent); //清除定时器
      this.timeOutEvent = 0;
      this.iatRecorder3.stop();
    },

    fnOpenClick() {
      this.isShowPopup = true;
    },
    fnClickCancel() {
      this.isShowPopup = false;
    },
    fnClickConfirm(e) {
      this.endmessage = this.endmessage + e;
    },
    // confirm() {
    //   const that = this;
    //   navigator.getUserMedia =
    //     navigator.getUserMedia ||
    //     navigator.webkitGetUserMedia ||
    //     navigator.mozGetUserMedia;
    //   if (navigator.getUserMedia) {
    //     navigator.getUserMedia({ audio: true }, that.onSuccess, that.onError);
    //   } else {
    //     that.mystatus = "您的浏览器不支持获取音频。";
    //   }
    // },
    // onError(error) {
    //   this.mystatus = "获取音频时好像出了点问题" + error;
    // },
    // onSuccess(stream) {
    //   console.log(stream + "***&&********");
    //   let context = new this.audioContext();
    //   let audioInput = context.createMediaStreamSource(stream);
    //   let volume = context.createGain();
    //   audioInput.connect(volume);
    //   let bufferSize = 2048;
    //   let recorder = context.createScriptProcessor(bufferSize, 1, 1);
    //   recorder.onaudioprocess = function (e) {
    //     var buffer = e.inputBuffer.getChannelData(0);
    //     var maxVal = 0;
    //     for (var i = 0; i < buffer.length; i++) {
    //       if (maxVal < buffer[i]) {
    //         maxVal = buffer[i];
    //       }
    //     }
    //     this.num = Math.round(maxVal * 100);
    //     console.log(this.num + "声音大小");
    //   };
    // },
  },
};
</script>

<style>
html,
body {
  height: 100%;
  width: 100%;
  margin: 0;
  border: 0;
  padding: 0;
}
.flex {
  display: -webkit-flex;
  display: flex;
}

.flex-1 {
  flex: 1;
}

.input {
  width: 100%;
}
</style>
