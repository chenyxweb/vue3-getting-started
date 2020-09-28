// setup 的使用

<template>
  <div>{{car.brand}} -- {{car.color}}</div>
</template>

<script>
export default {

  // setup为composition API 的起点
  // setup在beforeCreate钩子函数之前执行
  setup() {
    console.log('setup')
    console.log(this) // undefined  setup内部没有this

    // 这个数据不是响应式的,要使用响应式的数据想要使用reactive
    const car = {
      brand: '奔驰',
      color: 'blue'
    }

    // setup中返回数据暴露给组件使用
    return {
      car
    }

  },

  beforeCreate() {
    console.log('beforeCreate')
    console.log(this.car)
  },


}
</script>

<style>
</style>