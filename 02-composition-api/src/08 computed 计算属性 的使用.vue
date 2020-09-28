// computed 计算属性 的使用

<template>
  <div>今年年龄: <input type="text" v-model="age"> </div>
  <div>明年年龄: <input type="text" v-model="nextAge"> </div>
  <div>后年年龄: <input type="text" v-model="nextNextAge"> </div>
</template>

<script>
import { computed, ref } from 'vue'
export default {
  setup() {
    const age = ref(18)

    // 传入一个getter函数，会返回一个不允许修改的计算属性
    const nextAge = computed(() => {
      return parseInt(age.value) + 1
    })

    // 传入一个带有getter和setter函数的对象，会返回一个允许修改的计算属性
    const nextNextAge = computed({
      get(){
        return parseInt(age.value)+2
      },
      set(value){
        console.log(value)
        // 修改后年的年龄
        age.value = value-2
      }
    })

    return {
      age,
      nextAge,
      nextNextAge
    }
  }
}
</script>
