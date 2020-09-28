// 生命周期钩子函数

<template>生命周期钩子函数</template>

<script>
import { onBeforeMount, onMounted, onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted, onErrorCaptured } from 'vue'
export default {
  setup() {
    onBeforeMount(() => console.log('onBeforeMount'))
    onMounted(() => console.log('onMounted'))
    onBeforeUpdate(() => console.log('onBeforeUpdate'))
    onUpdated(() => console.log('onUpdated'))
    onBeforeUnmount(() => console.log('onBeforeUnmount'))
    onUnmounted(() => console.log('onUnmounted'))

  },
}
</script>
