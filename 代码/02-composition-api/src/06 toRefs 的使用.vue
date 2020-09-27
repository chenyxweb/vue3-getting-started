// toRefs 的使用

<template>
  <div>{{money}}</div>
  <div>{{car.branch}} -- {{car.color}} -- {{car.price}}</div>
  <button @click="money++">money++</button>
  <button @click="car.price+=1000">price++</button>
</template>

<script>
import { reactive, ref, toRefs } from 'vue'
export default {
  setup() {
    // 数据集中到一个reactive管理
    const state = reactive({
      money: 100,
      car: {
        branch: '宝马',
        color: 'blue',
        price: 10000,
      }
    })

    console.log({ ...state })

    // 将响应式对象state转化成普通对象,同时将对象的每一个属性转成ref,生成新的响应式数据
    console.log(toRefs(state))

    return {
      // ...state
      // 直接使用扩展运算符会导致state中本来是简单类型的money丢失响应式,此时要使用到 toRefs

      // 正确做法
      ...toRefs(state)
    }
  }
}
</script>
