// ref 的使用

<template>
  <!-- money.value 自动解套 -->
  <div>{{money}}</div>
  <button @click="money++">++</button>
</template>

<script>
import { ref } from 'vue'
export default {
  setup() {
    // 1. ref函数接受一个简单类型, 返回一个响应式的对象
    // 2. 这个响应式对象只有一个属性 value
    // 3. 在模板中使用ref，会自动解套，，，，会自动调用value
    const money = ref(1000)
    console.log(money)

    // money++ // error  setup内没有解套,仍然需要使用money.value
    money.value++

    return {
      money
    }

  },

  mounted() {
    console.log(this.money)
    // money.value 自动解套
    this.money++
  }
}
</script>

<style>
</style>