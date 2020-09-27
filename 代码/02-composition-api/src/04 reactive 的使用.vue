// reactive 的使用

<template>
  <div>{{car.brand}} -- {{car.color}}</div>
  <button @click="()=>car.brand='宝马'">修改品牌</button>
  <!-- <button @click="car.brand='宝马'">修改品牌</button> -->
</template>

<script>
import { reactive } from 'vue'
export default {
  setup() {
    // setup中药使用响应式数据, 需要使用reactive
    const car = reactive({
      brand: '奔驰',
      color: 'blue'
    })

    // setup中返回数据暴露给组件使用
    return {
      car
    }

  },
}
</script>

<style>
</style>