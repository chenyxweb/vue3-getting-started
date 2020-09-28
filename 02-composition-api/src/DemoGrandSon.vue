// 孙组件 -- provide inject
<template>
  <div class="grandson">
    <div>孙组件: {{money}} <button @click="handleClick">money--</button> </div>
  </div>
</template>

<script>
import { inject } from 'vue'
import { contextMoney, handleContextMoney } from './App.vue'

export default {
  setup() {
    const money = inject(contextMoney) // 使用爷组件数据
    const handleMoney = inject(handleContextMoney) // 使用爷组件方法

    // 处理点击事件
    const handleClick = () => {
      handleMoney(money.value - 1)
    }

    return {
      money,
      handleClick
    }
  }

}
</script>

<style scoped>
.grandson {
  border: 1px solid #000;
  padding: 15px;
}
</style>