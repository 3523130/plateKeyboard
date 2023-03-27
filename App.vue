<template>
    <div class="">
      <WidgetKeyboard @modelValue="Keyboard" :modelValue="carNo"></WidgetKeyboard>
    </div>
</template>

<script setup lang="ts">
import '@assets/scss/common/App.scss';
import WidgetKeyboard from './components/WidgetKeyboard.vue';
import { ref, onMounted } from 'vue'
const carNo = ref('');

// onMounted(() => {
//   carNo.value = '粤W11111'
// });
const Keyboard = (data: string) => {
  console.log(data);
};
</script>

<style scoped>

</style>
