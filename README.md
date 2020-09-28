# 1 响应式原理

## 1.1 ES5中Object.defineproperty

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <script>
      // const data = {
      //   name: 'zs',
      //   age: 18,
      // }

      const data = [1111, 2222]

      for (const key in data) {
        console.log(key, data[key])
        let temp = data[key] // 缓存值

        // 劫持
        Object.defineProperty(data, key, {
          get() {
            console.log(`劫持了${key}的获取`)
            return temp
          },
          set(value) {
            console.log(`劫持了${key}的修改,值为${value}`)
            temp = value
          },
        })

        // Object.defineProperty的缺点
        // 对象:无法劫持到对象属性的 添加(data.sex="male") 和 删除(delete data.name)
        // 数组:无法劫持到数组 下标(data[2]=123213) 和 长度(data.length=2) 的变化
      }

      // for ... in 可遍历对象和数组
      // for ... of 可遍历可迭代的数据
      // for (const iterator of object) {
      //   console.log(iterator)
      // }
    </script>
  </body>
</html>

```

## 1.2 vue2中使用

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div id="app">
      <p>{{car.brand}} -- {{car.color}} -- {{car.price}}</p>
      <p>{{arr}}</p>
    </div>

    <script src="https://unpkg.com/vue@2.6.12/dist/vue.js"></script>
    <script>
      const vm = new Vue({
        el: '#app',
        data: {
          car: {
            brand: '奔驰',
            color: 'blue',
          },
          arr: ['张三', '李四'],
        },
      })

      // vm.car.price = 10000 不会被劫持到 视图不会更新
    </script>
  </body>
</html>

```

## 1.3 ES6中proxy

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <script>
      const data = {
        name: 'zs',
        age: 18,
      }

      // const data = [1, 2, 3]

      const proxyData = new Proxy(data, {
        get(target, key, receiver) {
          // target : data
          // key : name 或 age
          // receiver : proxyData
          console.log(target, key, receiver)
          console.log(`劫持到${key}的获取`)
          return target[key]
        },
        set(target, key, value, receiver) {
          console.log(target, key, value, receiver)
          console.log(`劫持到${key}的设置,值为${value}`)
          target[key] = value
        },
        deleteProperty(target, key) {
          // 必须返回一个 Boolean 类型的值，表示了该属性是否被成功删除
          return delete target[key]
        },
      })

      // proxyData.sex = 'male' , delete proxyData.name  对象属性的添加和删除都能被劫持到
      // 数组的下标和长度操作都能被劫持到
    </script>
  </body>
</html>

```

## 1.4 vue3中使用

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div id="app">
      <h1>vue的例子</h1>
      <p>{{car.brand}} --- {{car.color}} ---{{car.price}}</p>
      <p>{{arr}}</p>

      <button @click="fn">按钮</button>
    </div>
    <!-- <script src="https://unpkg.com/vue@next"></script> -->
    <script src="https://cdn.bootcdn.net/ajax/libs/vue/3.0.0/vue.global.js"></script>
    <script>
      const App = {
        data() {
          return {
            car: {
              brand: '宝马',
              color: 'green',
            },
            arr: ['张三', '李四'],
          }
        },
        methods: {
          fn() {
            console.log('click')
            this.car.brand = '奔驰'
            // 会在DOM更新后才会执行回调函数
            this.$nextTick(function () {
              // vue为了性能考虑，不会改变完数据就立即更新DOM
              console.log(document.querySelector('p').innerHTML)
            })
          },
        },
      }
      const vm = Vue.createApp(App).mount('#app')

      // 一下操作都可以劫持到
      // vm.car.price = 10000 // 添加属性操作
      // delete vm.car.brand // 删除属性操作
      // arr[2] = '王五' // 数组下标操作
    </script>
  </body>
</html>

```

## 1.5 vue2和vue3响应式原理对比

### vue2中响应式

```
1.Vue2.0中使用ES5中的Object.defineProperty方法实现响应式数据

2.缺点
无法监测到对象属性的动态添加和删除
无法监测到数组的下标和length属性的变更 

3.解决方案
Vue2.0提供Vue.set方法用于动态给对象添加属性
Vue2.0提供Vue.delete方法用于动态删除对象的属性
重写vue中数组的方法，用于监测数组的变更

```

### vue3中响应式
```
1.Vue3.0中使用ES6中的proxy语法实现响应式数据

2.优点
可以检测到代理对象属性的动态添加和删除
可以监测到数组的下标和length属性的变更

3.缺点
ES6的proxy语法对于低版本浏览器不支持，IE11
Vue3.0会针对于IE11出一个特殊的版本用于支持ie11

```

# 2 创建vue3项目

## 2.1 使用vue-cli创建vue3.0项目

- 安装vue-cli到最新版本 (必须高于4.5.0)

```bash
yarn global upgrade @vue/cli
```

- 如果创建的是vue2项目，可以直接升级

```bash
vue add vue-next 
```

## 2.2 使用vite创建vue3.0项目

> Vite 是一个由原生 ESM 驱动的 Web 开发构建工具，在开发环境下基于浏览器原生 ES imports 开发，在生产环境下基于 Rollup 打包

vite的基本使用

```bash
npm init vite-app <project-name>

npm install

npm run dev
```

# 3 composition API

## 3.1 composition API 和 options API

- options API

```
1 Options API的优点是容易学习和使用，代码有明确的书写位置
2 Options API的缺点就是相似逻辑不容易复用，在大项目中尤为明显。
3 Options API可以通过mixins提取相同的逻辑，但是容易发生命名冲突且来源不清晰
```

![options api](.\参考资料\options api.png)

```vue
// options API : 处理鼠标位置的逻辑分散在多处,不利于维护和逻辑复用

<template>
  <h3>当前鼠标的位置</h3>
  <div>{{x}} -- {{y}}</div>
</template>

<script>
export default {
  data() {
    return {
      x: 0,
      y: 0
    }
  },

  methods: {
    mousemove(e) {
      this.x = e.pageX
      this.y = e.pageY
    }
  },

  created() {
    document.addEventListener("mousemove", this.mousemove)
  },

  beforeDestroy() {
    document.removeEventListener("mousemove", this.mousemove)
  }

}
</script>

<style>
</style>
```



- composition API

```
1 Composition API是根据逻辑功能来组织代码的，一个功能所有的api放到一起
2 即便项目很大，功能很多，都能够快速的定位到该功能所有的API
3 Composition API提高了代码可读性和可维护性
```

![composition api](.\参考资料\composition api.png)

![composition api2](.\参考资料\composition api2.png)

```vue
// composition API

<template>
  <h3>当前鼠标的位置</h3>
  <div>{{x}} -- {{y}}</div>
</template>

<script>
import { onMounted, reactive, onBeforeUnmount, toRefs, toRef } from 'vue'

// composition API 可以实现逻辑的提取
const mousePosition = () => {
  const mouse = reactive({ x: 0, y: 0 })

  const mousemove = (e) => {
    mouse.x = e.pageX
    mouse.y = e.pageY
  }

  onMounted(() => document.addEventListener("mousemove", mousemove))

  onBeforeUnmount(() => document.removeEventListener("mousemove", mousemove))

  return mouse
}

export default {

  // setup为composition API 的起点
  // setup在beforeCreate钩子函数之前执行
  setup() {
    console.log('setup')

    const mouse = mousePosition()

    return {
      ...toRefs(mouse)
    }
  },

  beforeCreate() {
    console.log('beforeCreate')
  },

}
</script>

<style>
</style>
```



- Vue3.0中推荐使用composition API,也保留了options API

## 3.2 setup

```
1 setup函数是一个新的组件选项,作为组件中composition API的起点
2 从生命周期钩子的角度来看，setup会在beforeCreate钩子函数之前执行
3 setup中不能使用this，this指向undefined
4 组件内只有一个setup
5 setup在整个生命周期只会执行一次
```

```vue
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
```

## 3.3 reactive

```
1 reactive函数接收一个普通对象, 返回该对象的响应式代理对象 (实际就是Proxy的封装)
2 要在setup中使用响应式数据, 就要使用reactive包裹一下
```



```vue
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
```

## 3.4 ref

```
1 ref函数接受一个简单类型的值，返回一个响应式的ref对象。返回的对象有唯一的属性 value
2 在setup函数中，通过ref对象的value属性可以访问到值
3 在模板中，ref属性会自动解套，不需要额外的.value
4 如果ref接受的是一个对象，会自动调用reactive
```

```vue
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
```

## 3.5 toRefs

```
1 把一个响应式对象转换成普通对象，该普通对象的每个 property 都是一个 ref
2 Reactive的响应式功能是赋予给对象的，但是如果给对象解构或者展开的时候，会让数据丢失响应式的能力。
3 使用toRefs可以保证该对象展开的每一个 属性都是响应式的
```

```vue
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

```

## 3.6 readonly

```
1 传入一个对象（响应式或普通）或 ref，返回一个原始对象的只读代理。
2 一个只读的代理是“深层的”，对象内部任何嵌套的属性也都是只读的。
3 可以防止对象被修改
4 readonly的实现: 实际就是Proxy的封装, 当set时, 不修改数据, 同时提示warn
```

```vue
// readonly 的使用

<template>
  <div>{{money}}</div>
  <div>{{car.branch}} -- {{car.price}}</div>
  <button @click="money++">money++</button>
  <button @click="car.price++">price++</button>
</template>

<script>
import { readonly, ref } from 'vue'
export default {
  setup() {
    const money = ref(100)

    // readonly的实现: 实际就是Proxy的封装, 当set时, 不修改数据, 同时提示warn
    const car = readonly({
      branch: '宝马',
      price: 10000
    })

    console.log('readonly car: ', car)

    return {
      // money 只读,不可修改
      money: readonly(money),
      car
    }
  }
}
</script>

```

## 3.7 computed

```
1 computed函数用于创建一个计算属性
2 如果传入一个getter函数，会返回一个不允许修改的计算属性
3 如果传入一个带有getter和setter函数的对象，会返回一个允许修改的计算属性
```

```vue
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

```

## 3.8 watch

```vue
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

```



## 3.9 钩子函数

```
vue3提供的生命周期钩子注册函数,只能在setup(){}期间使用
可以直接导入 onXXX 一族的函数来注册生命周期钩子
```

### 和vue2中对比

- `beforeCreate` -> 使用 `setup()`
- `created` -> 使用 `setup()`
- `beforeMount` -> `onBeforeMount`
- `mounted` -> `onMounted`
- `beforeUpdate` -> `onBeforeUpdate`
- `updated` -> `onUpdated`
- `beforeDestroy` -> `onBeforeUnmount`
- `destroyed` -> `onUnmounted`
- `errorCaptured` -> `onErrorCaptured`

## 3.10 provide 和 inject

```
1 provide 和 inject 提供依赖注入, 可以实现组件通讯,跨多级组件通讯
2 父传子: 父组件provide数据,子组件inject数据
3 子传父: 父组件privide方法,子组件inject方法,调用方法
3 provide和inject过程中的key可以使用Symbol,string
```

- 父组件

```vue
// App.vue

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

```

- 子组件
```vue
// DemoSon.vue

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
```

- 孙组件
```vue
// DemoGrandSon.vue

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
```

