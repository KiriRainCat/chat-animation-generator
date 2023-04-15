<script setup>
import { reactive, ref } from 'vue';
import FrnMessage from '@/components/FrnMessage.vue';
import MyMessage from '@/components/MyMessage.vue';
import AvatarUpload from "@/components/AvatarUpload.vue";

// ==== Form ==== //
const drawer = ref(false);
const form = reactive({
  name: 'Lovely Brutus',
  username: 'BrutusTheUgly',
  animationSpeed: 0.5,
  typingSpeed: 10,
  rightAvatarUrl: '',
  leftAvatarUrl: '',
  messages: '',
})

// ==== Context Animation ==== //
const showIndex = ref(1);
const buttonActive = ref(false);
const sleep = (delay) => new Promise((resolve) => setTimeout(resolve, delay))
let tmp = 0;
const startAnimation = async () => {
  if (showIndex.value < messages.value.length - 1) {
    if (tmp !== 0) {
      tmp = 0;
      document.getElementById("sendBtn").click();
      buttonActive.value = true;
      await sleep(200);
      buttonActive.value = false;
    }
    showIndex.value++;
    scroll();
    try {
      if (messages.value[showIndex.value + 1].isFriend === false) {
        typeValue.value = messages.value[showIndex.value + 1].msg;
        charIndex.value = 0;
        tmp = 1000 / form.typingSpeed * typeValue.value.length + 3000;
        setTimeout(typeText, 2500);
      }
      if (messages.value[showIndex.value].isFriend === true) {
        await new Audio("/twitter-notification-sound.mp3").play();
      }
    } catch (e) {}
    setTimeout(startAnimation, (tmp ? tmp : tmp + form.animationSpeed * 1000));
  }
}

// ==== Context Control ==== //
const isLarge = ref(false)
const messages = ref([
  {msg: "Hi", isFriend: true},
  {msg: "Hi There", isFriend: false},
])
const processMessages = () => {
  messages.value = [];
  form.messages.split("\n").forEach(msg => {
    if (msg.charAt(0) === "#") {
      messages.value.push({msg: msg.substring(1), isFriend: true})
    } else {
      messages.value.push({msg: msg, isFriend: false});
    }
  })
  showIndex.value = messages.value.length -1 ;
}
const scroll = () => {
  const scroller = document.querySelector(".content")
  scroller.scrollTo({top: 100000, behavior: "smooth"});
}
const blurButton = (e) => {
  e.target.blur();
  if(e.target.nodeName === 'SPAN') {
    e.target.parentNode.blur();
  }
}

// ==== Typing Animation ==== //
const typeStatus = ref(false);
const typeValue = ref("Hello World!");
const typingValue = ref("");
const charIndex = ref(0);
const typeText = () => {
  if (charIndex.value < typeValue.value.length) {
    typingValue.value += typeValue.value.charAt(charIndex.value);
    charIndex.value ++;
    setTimeout(typeText, 1000 / form.typingSpeed);
  }
}
</script>

<template>
  <div style="display: flex; justify-content: space-between">
    <div class="control-panel">
      <el-button type="primary" style="margin-bottom: 10px;" @click="e => {
        blurButton(e);
        showIndex = -2;
        startAnimation();
      }">
        Play Animation
      </el-button>
      <el-button type="primary" style="margin: 0 10px 10px 0;" @click="e => {
        blurButton(e);
        drawer = true;
      }">
        Edit Data
      </el-button>
      <el-button type="primary" style="margin-bottom: 10px; margin-left: 0;" @click="(e) => {
        blurButton(e);
        isLarge = !isLarge;
      }">Switch Size</el-button>
    </div>

    <div :class="{'container': true, 'large': isLarge}" style="text-align: center">
      <el-drawer v-model="drawer" :with-header="false" direction="ltr" size="25%">
        <el-form
            :model="form"
            label-position="left"
            label-width="170px"
        >
          <el-form-item label="Interval (s):">
            <el-input v-model="form.animationSpeed" type="number" />
          </el-form-item>
          <el-form-item label="TypingSpeed (letter/s):">
          <el-input v-model="form.typingSpeed" type="number" />
        </el-form-item>
          <div style="display: flex; justify-content: space-around">
            <AvatarUpload @url="url => form.leftAvatarUrl = url" label="LeftSide Avatar:" />
            <AvatarUpload @url="url => form.rightAvatarUrl = url" label="RightSide Avatar:" />
          </div>
          <el-form-item label="Name:">
            <el-input v-model="form.name"/>
          </el-form-item>
          <el-form-item label="Username:">
            <el-input v-model="form.username"/>
          </el-form-item>
          <el-input
              v-model="form.messages"
              :autosize="{ minRows: 14, maxRows: 14 }"
              resize="none"
              type="textarea"
              placeholder="Messages &#10;Use # for friend message&#10;&#10;Ex. &#10;Hi&#10;#Hi There"
              @change="processMessages"
          />
        </el-form>
      </el-drawer>
      <div class="header">
        <div class="left" style="display: flex;">
          <img src="@/assets/return.png" alt="back-icon" style="margin: 5px; width: 40px; height: 40px"/>
          <div style="margin: auto 0; text-align: left">
            <div style="font-size: 14px; font-weight: 600;">{{ form.name }}</div>
            <div style="font-size: 7px; color: #999;">{{ form.username }}</div>
          </div>
        </div>
        <img src="@/assets/info.png" alt="info_icon"
             style="margin: 12px; width: 26px; height: 26px;">
      </div>
      <div class="content">
        <div v-for="(msg, index) in messages" :key="index">
          <template v-if="showIndex >= index">
            <template v-if="msg.isFriend" class="frn">
              <FrnMessage :avatarURL="form.leftAvatarUrl" :msg="msg.msg" :scroll="scroll" />
            </template>
            <template v-else>
              <MyMessage :avatarURL="form.rightAvatarUrl" :msg="msg.msg" :scroll="scroll" />
            </template>
          </template>
        </div>
        <div style="height: 160px;"></div>
      </div>
      <div :class="{'input-area': true, 'large': isLarge}">
        <div class="textarea">
          <span class="typed-text">{{ typingValue }}</span>
          <span class="cursor" :class="{'typing' : typeStatus}">&nbsp;</span>
        </div>
        <div class="button-area">
          <el-button
              id="sendBtn"
              :class="{'buttonActive': buttonActive}"
              type="primary"
              style="width: 80px"
              @click="e => {
                blurButton(e);
                typingValue = '';
              }">
            Send
          </el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* ==== TextArea Typing Animation ==== */
.buttonActive {
  background-color: royalblue;
}
.cursor {
  display: inline-block;
  margin-left: 3px;
  width: 1px;
  background-color: #000;
  animation: cursorBlink 1s infinite;
}
@keyframes cursorBlink {
  49% { background-color: #000 }
  50% { background-color: transparent }
  99% { background-color: transparent }
}
.typing {
  animation: none;
}

/* ==== Static Styles ==== */
.container {
  height: 740px;
  min-width: 500px;
  border-radius: 4px;
  border: 0.5px solid #e0e0e0;
  background-color: #f5f5f5;
  display: flex;
  flex-flow: column;
  overflow: hidden;
}

.control-panel {
  width: 200px;
  text-align: left;
  margin-top: 20px;
}

.header {
  height: 50px;
  background-color: #fff;
  display: flex;
  align-content: center;
  justify-content: space-between;
}

.content {
  width: calc(100% - 40px);
  padding: 10px 20px;
  overflow-y: scroll;
  overflow-x: hidden;
  flex: 1;
}

.input-area {
  max-width: 500px;
  border-top: 0.5px solid #e0e0e0;
  height: 150px;
  display: flex;
  flex-flow: column;
  background-color: #fff;
}

.large {
  max-width: 960px;
  min-width: 960px;
}

.textarea {
  height: 120px;
  margin: 20px 20px 0;
  text-align: left;
  padding: 5px 5px 0;
  font-size: 14px;
}

.button-area {
  display: flex;
  height: 26px;
  margin-right: 10px;
  line-height: 40px;
  padding: 20px;
  justify-content: flex-end;
}

/* 设置滚动条的样式 */
::-webkit-scrollbar {
  margin-top: 2px;
  width: 4px;
}

/* 滚动槽 */
::-webkit-scrollbar-track {
  margin: 2px 0;
}

/* 滚动条滑块 */
::-webkit-scrollbar-thumb {
  border-radius: 6px;
  background: rgba(0, 0, 0, 0.3);
}
</style>