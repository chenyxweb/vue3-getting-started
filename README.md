# 响应式原理

## ES5中Object.defineproperty

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

## vue2

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

## ES6中proxy

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

## vue3

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

## vue2和vue3响应式原理对比

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

# 创建vue3项目

## 使用vue-cli创建vue3.0项目

- 安装vue-cli到最新版本 (必须高于4.5.0)

```bash
yarn global upgrade @vue/cli
```

- 如果创建的是vue2项目，可以直接升级

```bash
vue add vue-next 
```

## 使用vite创建vue3.0项目

> Vite 是一个由原生 ESM 驱动的 Web 开发构建工具，在开发环境下基于浏览器原生 ES imports 开发，在生产环境下基于 Rollup 打包

vite的基本使用

```bash
npm init vite-app <project-name>

npm install

npm run dev
```

