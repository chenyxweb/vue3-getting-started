// composition API

<template>
  <h3>当前鼠标的位置</h3>
  <div>{{x}} -- {{y}}</div>
</template>

<script>
import { onMounted, reactive, onBeforeUnmount, toRefs, toRef } from 'vue'

// composition API 可以实现逻辑的提取
const mousePosition = () => {
  const mouse = reactive({ x: 0, y: 0 })

  const mousemove = (e) => {
    mouse.x = e.pageX
    mouse.y = e.pageY
  }

  onMounted(() => document.addEventListener("mousemove", mousemove))

  onBeforeUnmount(() => document.removeEventListener("mousemove", mousemove))

  return mouse
}

export default {

  // setup为composition API 的起点
  // setup在beforeCreate钩子函数之前执行
  setup() {
    console.log('setup')

    const mouse = mousePosition()

    return {
      ...toRefs(mouse)
    }
  },

  beforeCreate() {
    console.log('beforeCreate')
  },

}
</script>

<style>
</style>