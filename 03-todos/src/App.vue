<template>
  <section class="todoapp">
    <header class="header">
      <h1>todos</h1>
      <input class="new-todo" placeholder="What needs to be done?" autofocus v-model="input" @keyup.enter="addTodo">
    </header>
    <!-- This section should be hidden by default and shown when there are todos -->
    <section class="main">
      <input id="toggle-all" class="toggle-all" type="checkbox" v-model="allSelected">
      <label for="toggle-all">Mark all as complete</label>
      <ul class="todo-list">
        <!-- These are here just to show the structure of the list items -->
        <!-- List items should get the class `editing` when editing and `completed` when marked as completed -->
        <li :class="{completed:item.done,editing:currentEditingId===item.id}" v-for="item in list" :key="item.id">
          <div class="view">
            <!-- vue 里面使用v-model对表单元素进行双向绑定 -->
            <input class="toggle" type="checkbox" v-model="item.done">
            <label @dblclick="dblclick(item)">{{item.name}}</label>
            <button class="destroy" @click="deleteTodo(item.id)"></button>
          </div>
          <input class="edit" v-model="currentEditingName" @keyup.enter="confirmEdit(item.id)" @keyup.esc="cancelEdit">
        </li>
      </ul>
    </section>
    <!-- This footer should hidden by default and shown when there are todos -->
    <footer class="footer" v-show="showFooter">
      <!-- This should be `0 items left` by default -->
      <span class="todo-count"><strong>{{leftCount}}</strong> item left</span>
      <!-- Remove this if you don't implement routing -->
      <ul class="filters">
        <li>
          <a class="selected" href="#/">All</a>
        </li>
        <li>
          <a href="#/active">Active</a>
        </li>
        <li>
          <a href="#/completed">Completed</a>
        </li>
      </ul>
      <!-- Hidden if no completed items are left ↓ -->
      <button class="clear-completed" v-show="showClear" @click="clearCompleted">Clear completed</button>
    </footer>
  </section>
</template>

<script>
import { reactive, toRefs, computed, watch } from 'vue'

export default {
  setup() {

    // 集中管理state
    const state = reactive({
      // 任务列表
      // list: [
      //   { id: 1, name: '吃饭', done: true },
      //   { id: 2, name: '睡觉', done: false },
      //   { id: 3, name: '打豆豆', done: false },
      // ],
      list: JSON.parse(localStorage.getItem('todos')) || [],
      input: '', // 输入框数据
      currentEditingId: -1, // 当前正在编辑的todo id
      currentEditingName: '' // 当前正在编辑的todo name
    })

    // 集中管理computed 方式一
    // const computedState = reactive({
    //   // 未完成的项目数
    //   leftCount: computed(() => state.list.filter(item => !item.done).length),
    //   // 是否展示footer
    //   showFooter: computed(() => !!state.list.length),
    //   // 是否展示clear按钮
    //   // 思路一: list的长度!==leftCount
    //   // showClear: computed(() => state.list.length !== computedState.leftCount)
    //   // 思路二: list里面存在done===true的
    //   showClear: computed(() => state.list.some(item => item.done)), // 没有setter的计算属性,只读
    //   // 是否全选
    //   allSelected: computed({ // 有setter的计算属性,可读可写
    //     get() { return state.list.every(item => item.done) },
    //     set(value) {
    //       state.list = state.list.map(item => {
    //         return { ...item, done: value }
    //       })
    //     }
    //   })
    // })

    // 集中管理computed 方式二
    const computedState = {
      // 未完成的项目数
      leftCount: computed(() => state.list.filter(item => !item.done).length),
      // 是否展示footer
      showFooter: computed(() => !!state.list.length),
      // 是否展示clear按钮
      // 思路一: list的长度!==leftCount
      // showClear: computed(() => state.list.length !== computedState.leftCount)
      // 思路二: list里面存在done===true的
      showClear: computed(() => state.list.some(item => item.done)), // 没有setter的计算属性,只读
      // 是否全选
      allSelected: computed({ // 有setter的计算属性,可读可写
        get() { return state.list.every(item => item.done) },
        set(value) {
          state.list = state.list.map(item => {
            return { ...item, done: value }
          })
        }
      })

    }

    // 监听list的变化,存本地
    watch(() => state.list, () => {
      console.log('list变化了')
      // 存本地
      localStorage.setItem('todos', JSON.stringify(state.list))
    }, { deep: true })

    // const leftCount = computed(() => {
    //   return state.list.filter(item => !item.done).length
    // })

    // 添加todo
    const addTodo = () => {
      if (!state.input.trim()) return
      state.list.unshift({ id: +new Date(), name: state.input, done: false })
      state.input = ''
    }

    // 删除todo
    const deleteTodo = (id) => {
      state.list = state.list.filter(item => item.id !== id)
    }

    // 双击todo
    const dblclick = ({ id, name, done }) => {
      state.currentEditingId = id
      state.currentEditingName = name
    }

    // 确认编辑
    const confirmEdit = (id) => {
      state.currentEditingId = -1
      state.list.find(item => item.id === id).name = state.currentEditingName
    }

    // 取消编辑
    const cancelEdit = () => {
      state.currentEditingId = -1
    }

    // 清除完成项
    const clearCompleted = () => {
      state.list = state.list.filter(item => !item.done)
    }

    return {
      // 把响应式对象转成普通对象,并且将对象内的每一项转成ref
      ...toRefs(state),
      // ...toRefs(computedState),
      ...computedState,
      // 只要是模板中要用到的setup中的数据都要return
      addTodo,
      deleteTodo,
      dblclick,
      confirmEdit,
      cancelEdit,
      clearCompleted
    }
  }

}
</script>
