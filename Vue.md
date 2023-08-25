## 1   vuex 的基本使用方法

Vuex是一个用于Vue.js应用程序的状态管理模式和库。它可以帮助管理应用程序的所有组件之间的共享状态，并且可以很容易地进行修改和调试。vuex 有五个属性 state，getters，action，mutation，modules

1. state：vuex的基本数据，用来存储变量

2. geeter：从基本数据(state)派生的数据，相当于state的计算属性,具有返回值的方法

3. mutation：提交更新数据的方法，必须是同步的(如果需要异步使用action)。每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。commit：同步操作，写法： this.$store.commit(‘mutations方法名’,值)

4. action：和mutation的功能大致相同，不同之处在于 ==》1. Action 提交的是 mutation，而不是直接变更状态。 2. Action 可以包含任意异步操作。

5. modules：模块化vuex，可以让每一个模块拥有自己的state、mutation、action、getters,使得结构非常清晰，方便管理。

## 2   Vue  双向绑定原理

mvvm （Model-View-ViewModel）双向绑定，采用数据劫持结合发布者-订阅者模式的方式，通过 Object.defineProperty() 来劫持各个属性的 setter、getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

![image-20230325220136765](C:\Users\XGJ\AppData\Roaming\Typora\typora-user-images\image-20230325220136765.png)

## 3  Vue 中组件通信的 6 种方式

### 	3.1  props /$emit

父组件向子组件传递数据采用 props 父组件通过动态数据绑定传入子组件，子组件通过props接收

$ emit 绑定一个自定义事件，当这个事件被执行时传递参数给父组件，而父组件通过v-on 监听并接收参数

### 	3.2  ref  /  $refs

这种方式也可以实现父子组件的通信

当一个组件被用到ref时，它就指向了子组件的实例，可以通过实例来访问组件的数据和方法

```vue
//子组件
export default {
  data () {
    return {
      name: 'JavaScript'
    }
  },
  methods: {
    sayHello () {
      console.log('hello')
    }
  }
}
//父组件
<template>
  <child ref="child"></component-a>
</template>
<script>
  import child from './child.vue'
  export default {
    components: { child },
    mounted () {
      console.log(this.$refs.child.name);  // JavaScript
      this.$refs.child.sayHello();  // hello
    }
  }
</script>

```

### 	3.3  全局事件总线

通过在vue的原型对象上挂载 $bus 属性，各组件之间可以通过 $emit 发送数据，可以通过 $on 接收数据

### 	3.4  消息的发布和订阅

### 	3.5  $parent   /   $children

使用parent可以访问父组件的实例，使用 $children 可以让组件访问子组件的实例，但是， $children 并不能保证顺序，并且访问的数据也不是响应式的。

## 4  watch 和 computed 的区别

computed 所依赖的属性没有发生变化，那么调用当前的函数的时候会从缓存中读取，而watch每次监听的值发生变化的时候都会执行回调

computed 默认第一次加载的时候就开始监听；watch第一次加载不做监听，除非添加immediate：true

computed和methods的差异是它具备缓存性，如果依赖项不变时不会重新计算。侦听器**可以检测某个响应式数据的变化并执行副作用**，常见用法是传递一个函数，执行副作用，`watch`没有返回值，但可以执行**异步**操作等复杂逻辑。（一般用来请求接口）

个人觉得有异步请求的用Wach，其他情况能用计算属性就首选计算属性。

**1.如果一个数据依赖于其他数据，那么把这个数据设计为computed的**  

**2.如果你需要在某个数据变化时做一些事情，使用watch来观察这个数据变化**

## 5  v-if和v-for不能同时使用

在Vue官方文档中，明确说明了`不建议将v-for和v-if同时使用`。因为两者作用在同一个元素时，优先级是不同的。

## 6   vueRouter 篇

### 	前端路由的工作原理

前端路由的本质，对 url  的  hash 值进行改变和监听，切换对应页面组件的 dom 结构

Vue.use(VueRouter) //const router = new VueRouter

### 	vue  路由传参的方式

#### params 传参

通过params传递参数，果我们想获取 id 的参数值，可以通过`this.$route.params.id`这种方式来打印出来就可以得到了；

#### 路由属性传参

需要在路由组件提前配置

```js
this.$router.push({
    name:`/admin/${item.id}`
})

//这个组件对应的路由配置 
{ 　　
	//组件路径 
	path: '/admin:id', 　　
	//组件别名 
	name: 'admin', 　　
	//组件名 
	component: Admin, 
}
```

以上两种传参方式基本上可以理解为 ajax 中的 post 请求方式，**参数都是不可见的**，但是上面两种方法都有一个弊端，就是当**页面刷新了是获取不到参数值**的

#### query 传参

携带query传参，传递的参数会带在地址栏的后面，这种方式是可以解决页面刷新参数消失问题的，这种方式可以理解为是 ajax 中的 get 方法，参数是直接在 url 后面添加的，参数是可见的，所以解决页面刷新参数消失问题建议使用此方法来解决

### hash 模式 和  history模式

hash 模式地址中永远带着 # 号，不美观。兼容性比较好。

history 模式地址干净美观，兼容性相比hash 模式较差，需要和后端配合

hash模式下,前端路由修改的是#中的信息,这样前端路由的路径没错，而浏览器请求的是不带hash的去请求服务器的,所以没有问题如果有点击前进后退，并且有刷新的需求，还是要使用history模式，那么后端就得设置

hash 相关的浏览器api

1. `window.location.hash`：获取或设置当前URL的片段标识符。例如，`window.location.hash = "#section"`会将URL的片段标识符设置为`#section`。
2. `window.onhashchange`：当URL的片段标识符发生变化时，浏览器会触发该事件。可以通过监听该事件来响应URL片段标识符的变化。
3. `window.location.href`：获取或设置完整的URL，包括片段标识符。例如，`window.location.href = "http://example.com/page#section"`会将页面导航到指定URL，并带有片段标识符。

history 相关的浏览器api

1. `window.history.back()`: 执行浏览器的后退操作，即导航到前一个页面。
2. `window.history.forward()`: 执行浏览器的前进操作，即导航到下一个页面。
3. `window.history.go(n)`: 导航到相对于当前页面的第n个页面，其中n为正数表示前进n个页面，为负数表示后退n个页面。
4. `window.history.pushState(state, title, url)`: 向浏览器的历史记录中添加一个新的状态，并修改当前URL。这个方法不会触发页面的实际导航，但会改变URL并将状态信息存储在浏览器历史记录中。可以使用`state`参数来传递自定义的状态数据，`title`参数用于设置文档的标题，`url`参数用于设置新的URL。
5. `window.history.replaceState(state, title, url)`: **修改当前历史记录条目的状态、标题和URL，但不添加新的历史记录。与`pushState`类似，但不会创建新的历史记录条目。**
6. `window.onpopstate`: 当浏览器的历史记录发生变化时，例如执行后退或前进操作时，会触发该事件。可以通过监听该事件来响应历史记录的变化。

hash 路由修改了 URL，但不会被包括在 HTTP 请求中。它被用来指导浏览器动作，并不影响服务端。hash 模式是根据 hash 值来发生改变，根据不同的值，渲染指定 DOM 位置的不同数据。History 路由利用了 HTML5 History Interface 中新增的 pushState() 和 replaceState() 方法。虽然改变了当前的 URL，但浏览器不会向后端发送请求，前端页面特殊方法监听 pushState 和 replaceState 方法动态匹配路由，但是由于路径直接拼接在端口号后面，后面的路径也会随着 http 请求发送给服务器，因此前端的 URL 必须和向发送请求后端URL保持一致，否则会报404错误。

history 模式要适配好，需要后端的支持，如果后端没有正确的配置，浏览器直接带着参数作为地址访问就会返回 404，这就十分不好看。因此我们需要在服务端添加一个覆盖所有情况的候选资源：如果 URL 匹配不到如何静态资源，应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。

#### 两种历史记录模式的原理

前端路由的原理关键有2点

1. 可以修改url，但不会引起刷新，从而在不刷新的页面的情况下跳转路由。
2. 监听url改变，根据url渲染对应组件。

hash模式和history模式的原理都是基于这两点。hash是通过浏览器提供的`location `API修改url，通过`onhashchange`方法监听hash改变；history通过浏览器提供的`history.pushState`或者`history.replacestate`修改url，通过`popState`事件监听url改变。

## 7   VUE 虚拟dom 和 diff 算法详解

### 	什么是虚拟dom？

虚拟dom是一个普通的 js 对象，是一个用来描述真实dom结构的js对象，因为它不是真实的，所以称为虚拟dom

![img](https://img-blog.csdnimg.cn/20210223170514938.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjcwNzI4Nw==,size_16,color_FFFFFF,t_70)

上图就是虚拟dom的结构，它是一个对象，sel表示当前节点标签名，data内是节点的属性，elm 表示当前虚拟节点对应的真实节点，text 表示当前节点下的文本，children 表示当前节点下的其他标签

**传统dom数据发送变化时，我们需要不断地去操作dom，才能更新dom的数据**，虽然后面出现了模板引擎这种东西，我们可以一次性去更新多个dom。但是模板引擎依然没有一种可以追踪状态的机制，当引擎内某个数据发生变化时，他依然要去操作dom去渲染整个引擎

**而虚拟dom可以追踪到当前的dom状态，它会根据当前数据生成一个”描述当前dom结构“的虚拟dom（对象），然后数据发生变化时，又会生成一个新的虚拟的dom，这两个dom恰恰保存了变化前后的状态。然后通过diff 算法，计算出前后两个虚拟 dom 的差异，得出一个最优的修改方法。可以明显地提高渲染效率以及用户体验**

**虚拟DOM的作用说白了就是利用js的计算性能来换取真实操作DOM所消耗的性能, 通过diff算法找出差异,最终做到只更新差异的部分,从而达到尽可能减少操作真实DOM操作,以提升性能.**

### 	什么是diff 算法

1.比较两个虚拟dom树，对根节点进行执行 patch(oldVnode,newVnode)函数，比较两个根节点是否是相同节点。如果不同，直接替换。

2.如果相同，对两个节点执行patchVnode(oldVnode,newVnode)，比较属性，文本，以及子节点。此时，要么新增，要么删除，要么修改文本内容。只有都存在子节点时，并且 oldVnode === newVnode 为false时。会执行updateChildren函数，进一步比较他们的子节点。

3、比较分3大类。
第一类：oldStart === newStart， oldStart === newEnd，oldEnd === newStart，oldEnd === newEnd 这4种情况的比较。如果这4种情况中任何一种匹配。那么会执行patchVnode进一步比较。同时指针往中间移

第二类：oldStart > oldEnd 或者 newStart > newEnd时。表示匹配结束。此时，多余的元素删除，新增的元素新增

第三类：上面几种情况都不匹配。那么这个时候key是否存在。就起到关键性作用了。存在key时，可以直接通过 key 去找到节点的原来的位置。如果没有找到，就新增节点。找到了，就移动节点位置。查找效率非常高。如果没有key呢，那么压根就不会去原来的节点中查找了，而是直接新增这个节点，这就导致这个节点下面的所有子节点都会被重新新增，出现明显的性能损耗

## 8  Vue 中 keep-alive 的用法

keep-alive 是vue中缓存组件的一种方式，当它包裹动态组件是，会缓存不活动的组件实例，而不是销毁它们。keep-alive 是一个抽象组件，它自身不会渲染成一个 DOM 元素。

keep-alive 结合 include 属性缓存组件 exclude属性不缓存组件

```js
// include 只缓存组件名字为home的组件，其他组件不会缓存，而exclude恰好相反
<keep-alive include="home">
   <router-view />
</keep-alive>
```

keep-alive 结合路由中的meta 属性来控制组件缓存

```js
{
      path: '/',
      name: 'home',
      meta:{
        keepAlive:true
      },
      component: Home
    }
}
```

keep-alive 包裹的组件不会调用create等生命周期函数

**解决方法**
使用activated与deactivated来获取当前组件是否处于活动状态
我在home组件里面写入了**activated与deactivated生命周期函数**
activated：组件激活时调用
deactivated：组件停用时调用

```js
  activated(){
   //  进入home组件的那一刻就会打印
    console.log("哎呀看见我了")
    console.log("----------activated--------")
  },
  deactivated(){
  //  离开home组件的那一刻就会打印
    console.log("讨厌！！你又走了")
    console.log("----------deactivated--------")
  }
```

## 9  nextTick 的使用和原理

### 什么是  nextTick

nextTick 是等待下一次 DOM 更新刷新的工具方法。Vue 有个异步更新策略，意思是如果数据变化，Vue不会立刻更新 DOM ，而是开启一个队列，把组件更新函数保存到一个队列中，在同一事件循环中发生的所有数据变更会异步的批量更新，这一策略导致我们对数据的修改不会立刻体现在DOM上，此时如果想要获取更新后的DOM状态，就需要使用nextTick。简单概括，vue中的nextTick主要用于处理数据动态变化后，DOM还未及时更新的问题，用nextTick可以获取数据更新后最新dom的变化。

### 项目中什么时候用 nextTick

生命周期的create当中需要进行一些 DOM 操作的时候将代码放到Vue.nextTick() 的回调函数中。原因：在 create 钩子函数中，DOM还未进行渲染，此时 DOM 操作是没有用的，需要等 下一次 DOM 更新之后才生效。因此一定要发到 Vue.nextTick() 的回调函数中。

又或者在某个数据变化后要执行某个动作，而这个动作需要用到 更新后的 DOM 结构数据时，也需要把相关逻辑写入Vue.nextTick ()回调函数中。

### $nextTick既然把它传入的方法变成微任务了，那它和其它微任务的执行顺序是怎样的呢？

$nextTick 既然把它传入的方法变成微任务了，那它和其它微任务的执行顺序是怎样的呢？
这简单来说就是谁先挂载Promise对象的问题，在调用 $nextTick方法时就会将其闭包内部维护的执行队列挂载到Promise对象，在数据更新时Vue内部首先就会执行 $nextTick方法，之后便将执行队列挂载到了Promise对象上，其实在明白Js的Event Loop模型后，将数据更新也看做一个 $nextTick方法的调用，并且明白 $nextTick方法会一次性执行所有推入的回调，就可以明白执行顺序的问题了。

### $nextTick和nextTick区别

$nextTick 和 nextTick 区别就是nextTick多了一个context参数，用来指定上下文。但两个的本质是一样的，$nextTick是实例方法，nextTick是类的静态方法而已；实例方法的一个好处就是，自动给你绑定为调用实例的this罢了。

## 10   Vue 2.x 中 data 为什么是一个函数

在Vue2.x 中，组件的data 选项需要以函数的形式返回一个对象，这是因为当组件被定义时，Vue 会对组件中的 data 选项进行特殊处理，将其变成 响应式 的数据对象

如果 data 选项是一个对象，那么这个对象会在组件被创建时被共享，如果一个组件实例修改了这个对象中的属性，那么这个修改会影响到所有使用这个组件的实例，这样可能会导致数据混乱的问题

**为了避免这种情况发生，Vue要求 data 选项必须以函数的形式返回一个对象，每个组件实例都会有自己 独立 的数据对象，这个函数在组件实例化时会被调用，返回一个全新的数据对象，这个对象是组件实例私有的，它的修改不会影响其他实例**

## 11 v-for 循环为什么要绑定 key

key 的作用是帮助 Vue.js 识别每个节点的标识，以便在更新列表时能够跟踪每个节点的变化。如果没有唯一的标识符，Vue.js 无法判断哪些节点是新的、哪些节点是旧的，从而无法进行高效的更新操作，这可能导致性能下降或出现错误。

此外，如果列表中的项没有唯一的 key ，如果列表中的项没有唯一的 key ，当移除列表中的某一项时，Vue.js 会重新渲染整个列表，而不是只渲染需要更新的部分，这可能会导致性能问题。

## 12 Vue 生命周期

1. **创建阶段**：
   - beforeCreate: 在实例被创建之后，但是数据观测和事件配置之前调用。此时组件实例还没有被初始化，无法访问到数据和方法。
   - created: 在实例创建完成后调用。此时组件实例已经完成了数据观测、属性和方法的配置，但尚未挂载到DOM上。
2. **挂载阶段**：
   - beforeMount: 在挂载开始之前被调用。此时模板编译已完成，但尚未将组件挂载到DOM上。
   - mounted: 在挂载完成后被调用。此时组件已经被挂载到DOM上，可以进行DOM操作和异步请求等操作。
3. **更新阶段**：
   - beforeUpdate: 在更新之前被调用，即组件的数据发生变化导致重新渲染之前。在该钩子函数中可以进行数据比较和准备更新所需的操作。
   - updated: 在更新完成后被调用。此时组件的数据已经更新，DOM也已经重新渲染完成。可以执行依赖于DOM的操作。
4. **销毁阶段**：
   - beforeDestroy: 在实例销毁之前调用。此时组件实例仍然完全可用，可以进行善后处理和清理工作。
   - destroyed: 在实例销毁之后调用。此时组件实例已经销毁，所有的事件监听器和观察者都被移除，组件相关的内存和资源都被释放。

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/7/21/164bbf610b61f3cd~tplv-t2oaga2asx-zoom-in-crop-mark:4536:0:0:0.awebp)

1. **ajax请求最好放在`created`里面**，因为此时已经可以访问`this`了，请求到数据就可以直接放在`data`里面。

   这里也碰到过几次，面试官问：ajax请求应该放在哪个生命周期。

2. **关于dom的操作要放在`mounted`里面**，在`mounted`前面访问dom会是`undefined`。

3. 每次进入/离开组件都要做一些事情，用什么钩子：

- 不缓存：

  进入的时候可以用`created`和`mounted`钩子，离开的时候用`beforeDestory`和`destroyed`钩子,`beforeDestory`可以访问`this`，`destroyed`不可以访问`this`。

- 缓存了组件：

  缓存了组件之后，再次进入组件不会触发`beforeCreate`、`created` 、`beforeMount`、 `mounted`，**如果你想每次进入组件都做一些事情的话，你可以放在`activated`进入缓存组件的钩子中**。

  同理：离开缓存组件的时候，`beforeDestroy`和`destroyed`并不会触发，可以使用`deactivated`离开缓存组件的钩子来代替。

## 13 Composition API 对比 Option API 的

1. 更好的代码组织：composition API 允许我们将逻辑相关的代码组织在一起，使得代码更加可读，可维护
2. 更好的复用性：通过自定义组合函数，我们可以将逻辑进行封装，使其在不同组件之间进行复用
3. 更好的类型推断：Composition API 使用了 TypeScript的方式来定义函数签名，可以提供更好的类型推断和编码支持
4. 更小的生产包体积：搭配 <script setup>  在等价情况下的选项式 API 更高效，对代码压缩也更友好
5. 更好的响应式

