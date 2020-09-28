// 子组件 -- provide inject
<template>
  <div class="son">
    <div>子组件: {{money}}</div>
    <demo-grand-son></demo-grand-son>
  </div>
</template>

<script>
import { inject } from 'vue'
import DemoGrandSon from './DemoGrandSon.vue'
import { contextMoney } from './App.vue'
export default {
  components: {
    DemoGrandSon
  },

  setup() {
    // 使用父组件数据
    const money = inject(contextMoney)

    return {
      money
    }
  }

}
</script>

<style scoped>
.son {
  border: 1px solid #000;
  padding: 15px;
}
</style>