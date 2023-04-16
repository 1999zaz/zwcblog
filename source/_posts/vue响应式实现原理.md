---
title: vue响应式实现原理
banner:
  type: img
  bgurl: https://pic1.zhimg.com/v2-b3c2c6745b9421a13a3c4706b19223b3_r.jpg
cover: >-
  https://tse3-mm.cn.bing.net/th/id/OIP-C.fnmA2ubzvBQYO1oj6yixuAHaE-?pid=ImgDet&rs=1
categories: 学习之路
date: 2023-04-16 19:25:43
originalLink: https://sunseekers.github.io/2023/04/13/vuejs/#%E5%93%8D%E5%BA%94%E5%BC%8F%E6%95%B0%E6%8D%AE%E4%B8%8E%E5%89%AF%E4%BD%9C%E7%94%A8%E5%87%BD%E6%95%B0
---


### 响应系统的实现原理
  一个响应式数据最基本的实现依赖于对“读取”和“设置”操作的拦截，从而在副作用函数与响应式数据之间建立联系。
  当“读取”操作发生时，我们将当前执行的副作用函数存储到“桶”中；当“设置”操作发生时，再将副作用函数从“桶”里取出并执行。
  这就是响应系统的<strong>根本实现原理</strong>

### 响应式数据与副作用函数
  副作用函数，这个函数执行会产生副作用的函数，比如全局变量或者影响别人使用
  比如说下面这个函数

```bash
   function modidyArr(){
  const arr = [1,2,3]
  setTimeout(()=>{
  arr.pop()
  },200)
  return arr
  }
  const res = modidyArr()
  console.log(res,'返回的值')

  setTimeout(()=>{
  console.log(res,'返回的值，和上次返回的结果不一样')
},300)
```

  响应式数据，当下面的 obj 的 text 变化的时候 effect 能够被重新执行，这就叫响应式数据，自己变化了，所用到自己的所有函数都要重新执行

```bash
  const obj = {text:"hello world"}
  function effect(){
  document.body.innerText = obj.text
  }
```

### 响应式数据的实现原理
  只细观察可以发现，副作用函数 effect 执行的时候，会触发 obj.text 的读取操作，
  
  修改 obj.text 的时候会触发 obj.text 的设置操作

  如果我们可以拦截这个对象的读取和设置操作，这个事情就变的简单了

  当我们读取这个字段的时候，把副作用函数存到某一个桶里面，当这个对像被设置或者修改的时候，就把这个桶里面的副作用函数取出来执行

  如此以来就达到了数据变化，函数重新执行。

  在 vue3 使用的是 Proxy 进行拦截和设置

  具体实现参考文章代码，或者看我本地代码，太长了

### 调度执行
  响应式系统在数据变化的时候有能力决定副作用函数执行的时机次数和方式。通常是在 effect 函数在设计一个选项参数，允许用户指定调度器，任务队列，再后来的 computed 和 watch 等实现中都用到了

  例如 computed 的粗糙实现( computed 计算属性实际上是一个懒执行的副作用函数)

```bash
  function computed(getter){
    const effectFn = effect(getter,{lazy:true}) // 建立副作用函数,把这个 getter 放到那个桶里去，只要里面用的数据变化了，就会重新执行
    const obj = {
    get value(){
    return effectFn() // 读取时，才执行 effectFn
      }
    }
    return obj
  }
```

  watch 本质就是观测响应式数据，数据变化，通知执行相对应地回调函数,利用调度执行的 scheduler 选项即可实现(它本质上利用了副作用函数重新执行时的可调度性)

```bash
   function watch(source,cd){
    effect(source,{
    scheduler(){
    cd()
    }
    })
  }
```

### 响应式具体实现
  proxy 只能代理对象，原始类型的值我们需要用对象包装一层，进行代理。

  使用 Reflect 进行对象操作

  set/map/array 在 proxy 都做了单独的处理，保证在获取和设置的时候可以响应式的变化

  浅响应与深响应，只读和浅读都做了处理

  原始类型函数进行封装

  ```bash
    function ref(val){
      // 内部创建包装对象
      const wrapper = {
      value: val
      }
      // 区分是 ref 来源的数据
      Object.defineProperty(wrapper,'\_\_v_isRef',{value: true})
      // 包装对象变成响应式数据
      return reactive(wrapper)
      }

```

  还另外实现了 toRef 和 toRefs 等对响适应数据做的一层处理
