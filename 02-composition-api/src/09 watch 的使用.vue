// watch 的使用

<template>
  <div>我的存款: {{money}}</div>
  <div>车: {{car.branch}} -- {{car.price}}</div>
  <div>count: {{count}}</div>
  <button @click="money++">money++</button>
  <button @click="car.price++">price++</button>
  <button @click="count++">count++</button>
</template>

<script>
import { reactive, ref, toRefs, watch } from 'vue'
export default {
  setup() {
    const state = reactive({
      money: 100,
      car: {
        branch: '宝马',
        price: 10000
      }
    })

    const count = ref(0)

    // 接受3个参数
    // 参数1：监视的数据源 可以是一个ref 或者是一个函数等...
    // 参数2：回调函数 (value, oldValue) =>{}
    // 参数3：额外的配置 是一个对象 { deep: true, immediate: true }

    // 1 侦听单个数据源
    // 1.1 侦听器一个拥有返回值的 getter 函数
    watch(() => state.money, (money, prevMoney) => {
      console.log(`money变化了, 新值${money}, 旧值${prevMoney}`)
    })
    // 1.2 直接侦听一个 ref
    watch(count, (count, prevCount) => {
      console.log(`count变化了, 新值${count}, 旧值${prevCount}`)
    })
    // 1.3 也可以直接侦听reactive
    watch(state, (state, prevState) => {
      console.log(state, prevState)
    }, {
      deep: true, // 深度监听
      immediate: true // 初始化时,马上执行一次
    })

    // 2 侦听多个数据源
    watch([() => state.money, () => state.car.price], ([money, prevMoney], [price, prevPrice]) => {
      console.log('数据变化了', state)
    })

    return {
      ...toRefs(state),
      count,
    }
  },
}
</script>
