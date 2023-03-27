<template>
    <div class="WidgetKeyboard">
      <div class="box">
        <ul class="input">
          <li v-for="n in 8" :class="[n == currentInputIndex + 1 && n != 8 ? 'on' : '', n == 7 && chinese ? 's':'']" @click="onInputClick(n - 1)" :key="n">{{ n === 8 ? plateArray[n - 1] === '' ? fonts : plateArray[n - 1] : plateArray[n - 1]}}</li>
        </ul>
      </div>
      <van-popup class="keyboard-row" v-model:show="showKeyboard" :overlay-style="{background:'rgba(255,255,255,0)'}" :safe-area-inset-bottom=true position="bottom" @closed="handleKeyboardClosed" teleport="#app">
        <div class="keyboard-top">
          <button class="btn-complete" @click="onCloseButtonClick">完成</button>
        </div>
        <div class="plate_chinese_box">
          <div class="cont" v-if="currentInputIndex === 0" >
            <van-button size="small" v-for="(province, index) in provinces" @click="onCharClick(province)" :key="index">{{ province }}</van-button>
          </div>
          <div class="cont2" v-if="currentInputIndex !== 0">
            <van-button size="small" v-for="(char, index) in plateChars" :disabled="disable(char)" @click="onCharClick(char)" :key="index">{{ char }}</van-button>
          </div>
          <div class="keyboard-row-item" @click="backspace">
            <div class="keyboard-cancel"></div>
          </div>
        </div>
      </van-popup>
    </div>
</template>

<script setup lang="ts">
import '../assets/scss/components/WidgetKeyboard.scss';
import { CAR_1_INCLUDE, CAR_2_INCLUDE, CAR_7_INCLUDE, CAR_CHAR, CAR_PROVINCES, CAR_REGEXP, DEFAULT_PROVINCE } from '@/constants';
import { ref, reactive, watch } from 'vue'

const props = defineProps({
  modelValue: {
    required: false,
  },
  fonts: {
    type: String,
    required: false,
    default: '新',
  }
})

const showKeyboard = ref(false);  //显示键盘
const provinces = CAR_PROVINCES;  // 省份简称
const plateChars = CAR_CHAR;      // 车牌号
const plateArray = reactive([DEFAULT_PROVINCE, 'B', '', '', '', '', '', '']);
const currentInputIndex = ref(2);
const chinese = ref(false);
const emit = defineEmits(['modelValue']);

watch( () => props.modelValue, (newValue) => {
  if (CAR_REGEXP.test(String(newValue))) {
    for (let i = 0; i < String(newValue).length; i ++) {
      plateArray[i] = String(newValue)[i];
    }
  }
});

// 点击输入框
const onInputClick = (index: number) => {
  console.log(index);
  
  showKeyboard.value = true;
  currentInputIndex.value = index;
};
// 关闭键盘触发
const handleKeyboardClosed = () => {
  const plate = plateArray.join('');
  emit('modelValue', plate);
};

const onCloseButtonClick = () => {
  showKeyboard.value = false;
};

// 输入
const onCharClick = (char: string) => {
  if (/^[\u4e00-\u9fa5]/.test(char)) {
    chinese.value = true;
  } else {
    chinese.value = false;
  }
  plateArray[currentInputIndex.value] = char;
  currentInputIndex.value ++;
  if (currentInputIndex.value > 6) {
    showKeyboard.value = false;
  }
};

// 禁用
const disable = (char: string) => {
  return (currentInputIndex.value ===1 && CAR_1_INCLUDE.indexOf(char) >= 0) || (currentInputIndex.value !== 1 && CAR_2_INCLUDE.indexOf(char) >= 0) || (currentInputIndex.value !== 6 && CAR_7_INCLUDE.indexOf(char) >= 0);
};

// 删除
const backspace = () => {
  if (!plateArray[currentInputIndex.value]) {
    if (currentInputIndex.value > 0) {
      currentInputIndex.value --;
    }
    plateArray[currentInputIndex.value] = '';
  } else {
    plateArray[currentInputIndex.value] = '';
  }
};
</script>

<style scoped>

</style>
