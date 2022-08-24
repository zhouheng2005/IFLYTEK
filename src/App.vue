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
    <popupShow v-if="isShowPopup" @cancelClick="fnClickCancel" @confirmClick="fnClickConfirm" />
  </div>
</template>

<script>
import popupShow from "./components/popup.vue"
export default {
  name: "App",
  components: {
    popupShow,
  },
  data() {
    return {
      endmessage: "",
      num: 0,
      isShowPopup: false,
    };
  },
  mounted() {
   
  },
  methods: {
    fnOpenClick() {
      this.isShowPopup = true;
    },
    fnClickCancel() {
      this.isShowPopup = false;
    },
    fnClickConfirm(e) {
      this.endmessage = this.endmessage + e;
    }
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
