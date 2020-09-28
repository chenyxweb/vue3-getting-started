// provide 和 inject

<template>
  <div class="father">
    <div>父组件: {{money}} <button @click="money++">money++</button> </div>
    <demo-son></demo-son>
  </div>
</template>

<script>
import { ref, provide } from 'vue'
import DemoSon from './DemoSon.vue'
export const contextMoney = Symbol('money')
export const handleContextMoney = Symbol('handleMoney')

export default {
  components: {
    DemoSon
  },

  setup() {
    const money = ref(100)

    // 提供数据
    provide(contextMoney, money)

    // 提过修改数据的方法
    provide(handleContextMoney, (value) => { money.value = value })

    return {
      money
    }
  },
}
</script>

<style scoped>
.father {
  border: 1px solid #000;
  padding: 15px;
}
</style>
