## (1)闭包

闭包是指有权访问另一个函数作用域中的变量的函数
作用：
1.可以在函数的外部访问到函数内部的局部变量
2.让这些变量保存在内存中，不会随着函数的结束而销毁
闭包真正的含义是，如果一个函数访问了此函数的父级及父级以上的作用域变量，就可以称这个函数是一个闭包

```js

    var a = 1;
    (function test (){
		alert(a);
	})()
```



上面的我们称为匿名闭包函数

```js
var elements=document.getElementsByTagName('li');
var length=elements.length;
for(var i=0;i<length;i++){
    elements[i].onclick=function(num){
        return function() {
                alert(num);
        };
    }(i);
}
```

这里的闭包指的是第19行的function（访问了父集作用域变量），通过匿名函数将i传入到闭包中，通过(i)执行了这里面的第一个函数，同时i的值被保存到num中。每个点击事件中都有一个局部变量num，num保存的是相应的i值。
个人理解：闭包的出现使得用户可以通过函数提供的手段修改函数内的变量，保证了变量不被污染（私有性），同时该变量一直被引用着，不会随着函数的结束而销毁。根据本人测试，函数外无法修改私有变量，必须调用闭包方法修改
<!-- 面试题 -->
如果期望代码的输出变成 0 -> 1 -> 2 -> 3 -> 4 -> 5，并且要求原有的代码块中的循环和两处 console.log 不变，该怎么改造代码？新的需求可以精确的描述为：代码执行时，立即输出 0，之后每隔 1 秒依次输出 1,2,3,4，循环结束后在大概第 5 秒的时候输出 5
ES6解法


```js
const tasks = [];
for (var i = 0; i < 5; i++) {   // 这里 i 的声明不能改成 let，如果要改该怎么做？
    ((j) => {
        tasks.push(new Promise((resolve) => {
            setTimeout(() => {
                console.log(new Date, j);
                resolve();  // 这里一定要 resolve，否则代码不会按预期 work
            }, 1000 * j);   // 定时器的超时时间逐步增加
        }));
    })(i);
}
```

```js
Promise.all(tasks).then(() => {
    setTimeout(() => {
        console.log(new Date, i);
    }, 1000);   // 注意这里只需要把超时设置为 1 秒
});
ES7解法
```

```js
const tasks = []; // 这里存放异步操作的 Promise
const output = (i) => new Promise((resolve) => {
    setTimeout(() => {
        console.log(new Date, i);
        resolve();
    }, 1000 * i);
});
```


```js
// 生成全部的异步操作
for (var i = 0; i < 5; i++) {
    tasks.push(output(i));
}

// 异步操作完成之后，输出最后的 i
Promise.all(tasks).then(() => {
    setTimeout(() => {
        console.log(new Date, i);
    }, 1000);
});
```

1. **封装私有变量和方法**：闭包可以用于创建私有变量和方法，将其封装在函数内部，并通过返回函数或对象来访问和操作这些私有成员。这样可以实现数据的隐藏和保护，避免全局命名冲突，提供模块化和封装的能力。
2. **实现函数工厂**：闭包可以用于创建函数工厂，即根据不同的参数或配置返回不同的函数。这样可以方便地生成具有特定行为和状态的函数，实现更灵活和可定制的功能。
3. **延迟执行和异步操作**：闭包可以用于实现延迟执行和异步操作。通过在闭包中存储外部变量或状态，并在需要时访问和修改它们，可以在合适的时机触发函数执行或完成异步操作。
4. **实现缓存**：闭包可以用于实现缓存功能，将一些计算结果缓存起来以提高性能。通过在闭包中保存计算过程中的中间结果或昂贵的计算结果，并根据输入参数进行快速查找和返回，可以避免重复计算，提高代码执行效率。
5. **事件处理和回调函数**：闭包可以用于实现事件处理和回调函数的机制。通过将事件处理函数或回调函数定义在外部作用域中，并在合适的时机执行或触发，可以保留事件处理函数所需的上下文和状态，实现事件驱动和异步编程。

## (2)变量、作用域和内存

typeof null ->Object

因此 typeof 无法区分 null 和 Object

使用 const 声明的变量必须同时初始化某个值，一经声明，在其生命周期内不能重新赋予新的值。const 声明只应用到顶级原语或者对象。对象的键不受影响。

### 垃圾回收

javaScript 是执行垃圾回收的语言，执行环境负责在代码执行时管理内存。基本思路很简单：确定哪个变量不使用，然后释放它占用的内存。这个过程是周期性的。回收策略常用（1）标记清理（主流） （2）引用计数（会有严重的循环引用问题）

 解除变量的引用不仅可以消除循环引用，而且对垃圾回收也有帮助。为了促进内存回收，全局对象，全局对象的属性和循环引用都应该在不需要的时候解除引用。

## (3) JS实现继承的6种方式

### 3.1 原型链继承

让一个函数的构造函数的原型是另一个类型的实例，那么这个构造函数new 出来的实例就具有该实例的属性。当试图访问一个对象的属性时，它会依次向上搜索直到原型链的末尾

```js
function Parent() {
   this.isShow = true
   this.info = {
       name: "mjy",
       age: 18,
   };
}
 
Parent.prototype.getInfo = function() {
   console.log(this.info);
   console.log(this.isShow);
}
 
function Child() {};
Child.prototype = new Parent();
 
let Child1 = new Child();
Child1.info.gender = "男";//传递参数
Child1.getInfo(); // {name: 'mjy', age: 18, gender: '男'} ture
 
let child2 = new Child();
child2.isShow = false
console.log(child2.info.gender) // 男，对象实例共享了所有继承的方法
child2.getInfo(); // {name: 'mjy', age: 18, gender: '男'} false
```



优点：写法方便简洁，容易理解。

缺点：对象实例共享所有继承的属性和方法。传教子类型实例的时候，不能传递参数，因为这个对象是一次性创建的（没办法定制化）。

### 3.2 借用构造函数继承

```js

function Parent(gender) {
  this.info = {
    name: "yhd",
    age: 19,
    gender: gender
  }
}
 
function Child(gender) {
    Parent.call(this, gender)
}
```

在子类型构造函数的内部调用父类型构造函数，使用 apply() 或 call()方法将父对象的构造函数绑定在子对象上。

优点：解决了原型链实现继承的不能传参的问题和父类的原型共享的问题。

缺点：借用构造函数的缺点是方法都在构造函数中定义，因此无法实现函数复用。在父类型的原型中定义的方法，对子类型而言也是不可见的，结果所有类型都只能使用构造函数模式。

### 3.3 组合继承

将 `原型链` 和 `借用构造函数` 的组合到一块。**使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。这样，既通过在原型上定义方法实现了函数复用，又能够保证每个实例都有自己的属性**

```js
function Person(gender) {
  console.log('执行次数');
  this.info = {
    name: "mjy",
    age: 19,
    gender: gender
  }
}
 
Person.prototype.getInfo = function () {   // 使用原型链继承原型上的属性和方法
  console.log(this.info.name, this.info.age)
}
 
function Child(gender) {
  Person.call(this, gender) // 使用构造函数法传递参数
}
 
Child.prototype = new Person()
 
let child1 = new Child('男');
child1.info.nickname = 'xiaoma'
child1.getInfo()
console.log(child1.info);
 
let child2 = new Child('女');
console.log(child2.info);
```



优点: 解决了原型链继承和借用构造函数继承造成的影响。

缺点: 无论在什么情况下，都会调用两次超类型构造函数：一次是在创建子类型原型的时候，另一次是在子类型构造函数内部

### 3.4  原型式继承

借用构造函数在一个函数A内部构建一个临时性的构造函数，然后将传入的对象作为这个构造函数的原型，最后返回临时类型一个新实例

本质上，函数A是对传入的对象执行了一次浅复制。

```js
function createObject(obj) {//浅复制
  function Fun() {}
  Fun.prototype = obj
  return new Fun()
}
 
let person = {
  name: 'mjy',
  age: 18,
  hoby: ['唱', '跳'],
  showName() {
    console.log('my name is:', this.name)
  }
}
 
let child1 = createObject(person)
child1.name = 'xxxy'
child1.hoby.push('rap')
let child2 = createObject(person)
 
console.log(child1)//{name:xxxy}
console.log(child2)//{}
console.log(person.hoby) // ['唱', '跳', 'rap']
```

个人想法：createObject 返回了一个新的空的构造函数，该构造函数的原型对象是我们传入的对象，let 一个对象指向这个空的构造函数的实例对象身上，该思想跟原型链继承一样的。

优点是：不需要单独创建构造函数。

缺点是：属性中包含的引用值始终会在相关对象间共享，子类实例不能向父类传参

### 3.5  寄生式继承

寄生式继承的思路与(寄生) `原型式继承` 和 `[工厂模式]` 似, 即创建一个仅用于封装继承过程的函数,该函数在内部以某种方式来增强对象,最后再像真的是它做了所有工作一样返回对象。

```js
function objectCopy(obj) {
  function Fun() { };
  Fun.prototype = obj;
  return new Fun();
}
 
function createAnother(obj) {
  let clone = objectCopy(obj);
  //比原型式继承多了一步可以添加函数
  clone.showName = function () {
    console.log('my name is：', this.name);
  };
  return clone;
}
 
let person = {
     name: "mjy",
     age: 18,
     hoby: ['唱', '跳']
}
 
let child1 = createAnother(person);
child1.hoby.push("rap");
console.log(child1.hoby); // ['唱', '跳', 'rap']
child1.showName(); // my name is： mjy
 
let child2 = createAnother(person);
console.log(child2.hoby); // ['唱', '跳', 'rap']
```

## (4)   数据类型

Number String undefined null boolean Object

ES6 中新增了一种 Symbol 。这种类型的对象永不相等，即始创建的时候传入相同的值，可以解决属性名冲突的问题，做为标记。

谷歌67版本中还出现了一种 bigInt。是指安全存储、操作[大整数](https://so.csdn.net/so/search?q=大整数&spm=1001.2101.3001.7020)。

![img](https://img-blog.csdnimg.cn/7d646aed4ad34173970e8ff47a24d258.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6bq76L6j6Jm-5aS0,size_20,color_FFFFFF,t_70,g_se,x_16)

## (5)  call  apply bind 的区别

三者都是用来改变 this 的指向。

### call 的用法

调用 fn.call() 会将 fn 中的this 修改为传入的第一个参数 thisArg；将后面的参数传入给 fn ，并立即执行函数 fn

```js
let obj = {
        name: "xiaoming",
        age: 24,
        sayHello: function (job, hobby) {
            console.log(`我叫${this.name},今年${this.age}岁。我的工作是: ${job}，我的爱好是: ${hobby}。`);
        }
    }
    obj.sayHello('程序员', '看美女'); // 我叫xiaoming,今年24岁。我的工作是: 程序员，我的爱好是: 看美女。


    let obj1 = {
        name: "lihua",
        age: 30
    }
    // obj1.sayHello(); // Uncaught TypeError: obj1.sayHello is not a function
    obj.sayHello.call(obj1, '设计师', '画画'); // 我叫lihua,今年30岁。我的工作是: 设计师，我的爱好是: 画画。
 	obj.sayHello.apply(obj1, ['设计师', '画画']); // 我叫lihua,今年30岁。我的工作是: 设计师，我的爱好是: 画画。

//手写call
Function.prototype.call = function(context,...args){
    context = context ? context:window //检查是否传入对象，没有则默认为window对象
    args = args ? args : [] //args 不传时默认是空数组
    context.fn = this // 将调用call的函数作为 将要绑定对象的一个方法
    const res = context.fn(...args)
    //个人理解，用context.fn 相当于复制了一份 原本的this函数 ，然后context.fn 传入了args参数，这时候的this指向指的就是context，成功达成改变 this 指向的目的。
    delete context.fn
    return res
}

```

### apply 的用法

apply 和 call 的用法大致相同，立即修改 this 的指向，唯一不同的是 apply 只能传递两个参数，第二个参数是一个数组，数组里面的元素会展开传入 fn ，作为 fn 的参数

```js
// 手写apply
Function.prototype.apply = function(context,[...args]){
    context = context ? context : window
    args = args : args ? []
    context.fn = this
    const res = context.fn([...args])
    delete context.fn
    return res
}
```



### bind 的用法

bind 的传参方式跟 call 一样可以传递多个参数，它与 call ，apply 不同的是调用它之后不会立即执行，而是返回一个修改this后的函数

```js
    let obj = {
        name: "xiaoming",
        age: 24,
        sayHello: function (job, hobby) {
            console.log(`我叫${this.name},今年${this.age}岁。我的工作是: ${job}，我的爱好是: ${hobby}。`);
        }
    }
    // obj.sayHello('程序员', '看美女'); // 我叫xiaoming,今年24岁。我的工作是: 程序员，我的爱好是: 看美女。

    let obj1 = {
        name: "lihua",
        age: 30
    }
    
    obj.sayHello.bind(obj1, '设计师', '画画'); // 无输出结果
    obj.sayHello.bind(obj1, '设计师', '画画')(); // 我叫lihua,今年30岁。我的工作是: 设计师，我的爱好是: 画画。
-----------------------------------------------------------------------------
    //手写 bind 函数
    //手写 bind 函数最大的不同就是判断它是否被当作构造函数调用
Function.prototype.bind = fnnction(context,...args){
    context = context ? context ? window
    let fn = this
    return function newFn(...fnArgs){
    	let res 
    	if(this instanceof newFn){
            //如果是以构造函数的形式被调用，this 的指向就无需去修改了
            res = new fn(...args,...fnArgs)
        }else{
            //如果不是则跟call一样，只需要合并参数就行了
            res = fn.call(context,...args,...fnArgs)
        }
    	return res
    }
}
```

## (6)  防抖和节流

防抖就是在规定时间内 假设是 300ms，点击多次但是只执行最后一次，并且每次点击都会重置规定时间。比如我在 300 ms 点击了 30次，规定时间又会从我点击第三十次时开始计算，假如我在新的300ms 内没有再点击，那么这次请求（监听）则可以发送成功

#### 防抖实现

```js
//监听输入框的值变化
// 第一个参数是要执行的动作函数，第二个是防抖的秒数
input.oninput = debounce(function(){
	// this指向window
	console.log(this.value)
},3000)

// 封装debounce 函数
function debounce(fn,delay){
	let timeId = null
	return function(){
		if(timeId!=null){
			// 如果有了定时器，根据防抖的定义，将定时器取消再重新设立一个
			clearTimeOut(timeId)
		}
		timeId = setTimeOut(()=>{
			// 执行fn函数,且改变this的指向，指向到input
			fn.call(this)
		})
	}
}
```

节流：控制高频事件的执行次数（事件频繁触发时，每隔多少秒触发一次）

防抖是只执行最后一次，而节流是减少执行次数。

#### 节流实现

```js
window.onscroll = throttle(function () {
        console.log("hello world");
      }, 3000);
 
      // 通过定时器实现节流
      function throttle(fn, wait) {
        let lock = true;
        return function () {
          if (lock) {
            // 关锁
            lock = false;
            // 通过定时器开锁
            setTimeout(() => {
              lock = true;;
              fn();
            }, wait);
          }
        };
      }
```

## (7) 深拷贝和浅拷贝

js 的数据类型分为基本数据类型和引用数据类型，前者存储在栈中，后者则将地址存储在栈内存中，而真实数据存储在堆内存中。

![img](https://img-blog.csdnimg.cn/69936ebc472448f3b8150dd9adb555fb.png#pic_center)

基本类型赋值时，赋的是数据，所以不存在深拷贝和浅拷贝的问题。

引用类型有问题，引用类型赋值时，赋的是地址。

![img](https://img-blog.csdnimg.cn/20190807125742367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppYW5nNzcwMTAzNw==,size_16,color_FFFFFF,t_70)

深拷贝最终版本

深拷贝_如果属性都是json对象，那么用递归的方式

 

//如果对象的属性是对象(引用类型),属性的属性也是引用类型,即层层嵌套很多.怎么办,只能递归

//如下对象,要复制:

```js
var p = {
	"id":"007",
	"name":"刘德华",
	"wife":{
		"id":"008",
		"name":"刘德的妻子",
		"address":{
			"city":"北京",
			"area":"海淀区"
		}
	}
}

//写函数
function copyObj(obj){
	let newObj={};
	for(let key in obj){
		if(typeof obj[key] =='object'){//如:key是wife,引用类型,那就递归
			newObj[key] = copyObj(obj[key])
		}else{//基本类型,直接赋值
			newObj[key] = obj[key];
		}
	}
	return newObj;
}

let pNew = copyObj(p);
pNew.wife.name="张三疯";
pNew.wife.address.city = "香港";
console.log(pNew);
console.log(p);
```






3.2、深拷贝_如果属性是数组等非键值对的对象

      就得单独处理：要么给数组增加一个自我复制的函数（建议这样做），要么单独判断。

//给数组对象增加一个方法，用来复制自己

```js
Array.prototype.copyself = function(){
	let arr = new Array();
	for(let i in this){
		arr[i]  = this[i]
	}
	return arr;
}

var p = {
	"id":"007",
	"name":"刘德华",
	"books":new Array("三国演义","红楼梦","水浒传")//这是引用类型
}

function copyObj(obj){
	let newObj={};
	for(let key in obj){
		if(typeof obj[key] =='object'){//如:key是wife,引用类型,那就递归
			newObj[key] = obj[key].copyself();
		}else{//基本类型,直接赋值
			newObj[key] = obj[key];
		}
	}
	return newObj;
}

var pNew = copyObj(p);
pNew.books[0] = "四国";
console.log(pNew);
console.log(p);  
```

`JSON.parse()` 用于将 JSON 字符串解析为JavaScript 对象

```js
const jsonString = '{"name":"John", "age":30, "city":"New York"}';
const jsonObject = JSON.parse(jsonString);
console.log(jsonObject); // 输出: { name: 'John', age: 30, city: 'New York' }

```

`JSON.stringify()`: 该方法用于将 JavaScript 对象或值转换为 JSON 字符串。

```js
const person = {
  name: 'John',
  age: 30,
  city: 'New York'
};
const jsonString = JSON.stringify(person);
console.log(jsonString); // 输出: '{"name":"John","age":30,"city":"New York"}'

```



可以通过 `const obj2 =  JSON.parse(JSON.stringify(obj1))` 实现深拷贝

![image-20230711164747632](C:\Users\XGJ\AppData\Roaming\Typora\typora-user-images\image-20230711164747632.png)

## (8) js 有几种方法判断变量的类型

1. typeof 运算符：typeof 是一种操作符，用于返回一个值的数据类型，如 "string"、"number"、"boolean"、"undefined"、"object"、"function" 和 "symbol"。但需要注意的是，typeof null 的返回值为 "object"，这是一个历史遗留问题。
2. instanceof 运算符：instanceof 用于判断一个对象是否是另一个对象的实例。例如，如果 variable instanceof Array 返回 true，那么 variable 就是一个数组。
3. Object.prototype.toString.call() 方法：这是一种更可靠的方法，它返回一个表示变量类型的字符串，不受 typeof 和 instanceof 的局限。例如，Object.prototype.toString.call(variable) 返回 "[object Array]" 或 "[object Object]"，可以确定变量是一个数组或对象。
4. Array.isArray() 方法：这是一种专门用于判断一个值是否为数组的方法。如果 variable 是一个数组，则 Array.isArray(variable) 返回 true。

总的来说，typeof 运算符适用于基本类型和函数，instanceof 运算符适用于判断对象类型，Object.prototype.toString.call() 方法更加全面和可靠，Array.isArray() 方法适用于判断是否为数组。需要根据具体的场景选择合适的方法来判断变量类型。

## (9) js 中数组去重的几种方式

在 JavaScript 中，有很多种方法可以用来去除数组中的重复项。以下是几种常见的方法：

1. 使用 Set

```js
const arr = [1, 2, 3, 3, 4, 4, 5];
const uniqueArr = [...new Set(arr)];
console.log(uniqueArr); // [1, 2, 3, 4, 5]
```

使用 Set 去除数组中的重复项是最常见的方法之一。Set 是一种不允许重复值的数据结构，所以将数组转换成 Set，再将 Set 转换回数组即可去除重复项。

1. 使用 filter 和 indexOf

```js
const arr = [1, 2, 3, 3, 4, 4, 5];
const uniqueArr = arr.filter((item, index) => arr.indexOf(item) === index);
console.log(uniqueArr); // [1, 2, 3, 4, 5]
```

使用 filter 和 indexOf 的方法，遍历数组，筛选出在数组中第一次出现的元素。

1. 使用 reduce 和 includes

```js
const arr = [1, 2, 3, 3, 4, 4, 5];
const uniqueArr = arr.reduce((prev, curr) => prev.includes(curr) ? prev : [...prev, curr], []);
console.log(uniqueArr); // [1, 2, 3, 4, 5]
```

使用 reduce 和 includes 的方法，遍历数组，将不重复的元素添加到一个新的数组中。

1. 使用 Map

```js
const arr = [1, 2, 3, 3, 4, 4, 5];
const map = new Map();
arr.forEach(item => {
  if(!map.has(item)) {
    map.set(item, true);
  }
});
const uniqueArr = Array.from(map.keys());
console.log(uniqueArr); // [1, 2, 3, 4, 5]
```

使用 Map 的方法，将数组中的元素作为 key 添加到 Map 中，遇到重复元素时不添加。然后将 Map 中的 keys 转换成数组即可。

## (10) 为什么要将任务队列分为微任务和宏任务呢，他们之间的本质区别是什么呢？

JavaScript在遇到异步任务时，会将此任务交给其他线程来执行（比如遇到setTimeout任务，会交给定时器触发线程去执行，待计时结束，就会将定时器回调任务放入任务队列等待主线程来取出执行），主线程会继续执行后面的同步任务。

对于微任务，比如promise.then，当执行promise.then时，浏览器引擎不会将异步任务交给其他浏览器的线程去执行，而是将任务回调存在一个队列中，当执行栈中的任务执行完之后，就去执行promise.then所在的微任务队列。

所以，宏任务和微任务的本质区别如下：

- 微任务：不需要特定的异步线程去执行，没有明确的异步任务去执行，只有回调；
- 宏任务：需要特定的异步线程去执行，有明确的异步任务去执行，有回调；

## (11) const 声明的对象怎么不让它也更改 key 值

const 无法冻结对象，但是可以使用 Object.freeze() 冻结对象，使得 key 值无法更改，但是也只是冻结第一层
