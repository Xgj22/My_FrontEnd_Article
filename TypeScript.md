## 类

### 抽象类

TypeScript 中的抽象类：它是提供其他类继承的基类，不能直接被实例化。

用abstract关键字定义抽象类和抽象方法，抽象类中的抽象方法不包含具体实现并且必须在派生类（也就是其子类）中实现，abstract抽象方法只能放在抽象类里面。

我们常常使用抽象类和抽象方法用来定义标准。

//抽象类中必须要有抽象方法，抽象方法不必在父类中具体实现，但是必须在子类里面实现

```ts
//动物抽象类，所有动物都会跑（假设），但是吃的东西不一样，所以把吃的方法定义成抽象方法
abstract class Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    abstract eat(): any;//抽象方法不包含具体实现并且必须在派生类中实现
    run() {
        console.log(this.name + "会跑")
    }
}

class Dog extends Animal {
    constructor(name: string) {
        super(name);
    }
    eat(): any {//抽象类的子类必须实现抽象类里面的抽象方法
        console.log(this.name + "吃骨头");
    }
}

var d: Dog = new Dog("小狼狗");
d.eat();

class Cat extends Animal {
    constructor(name: string) {
        super(name);
    }
    eat(): any {//抽象类的子类必须实现抽象类里面的抽象方法
        console.log(this.name + "吃老鼠");
    }
}

var c: Cat = new Cat("小花猫");
c.eat();

```

### 多态

多态：父类定义一个方法不去实现，让继承它的子类去实现 ，每一个子类有不同的表现，多态属于继承。

//多态的实际实现参考抽象类中的代码，父类只提供方法名，让不同的子类实现具体方法，多方面不同。

## 接口

### 接口的用途

接口的用途就是对行为和动作进行规范和约束，跟抽象类有点像，但是，接口中不能有方法体，只允许有方法定义。（就是说接口中定义的方法不能有方法名，只能有方法的参数，而抽象类中有方法的名字）

```ts
//对传入对象的属性约束，以下这个是一个属性接口
interface FullName {
    firstName: string;
    secondName: string;
}

function printName(name: FullName) {
    console.log(name.firstName + "--" + name.secondName);
}

//传入的参数必须包含firstName、secondName
var obj = {
    age: 20,
    firstName: '张',
    secondName: '三'
};
printName(obj);//正确
// printName("1213");//错误
---------------------------------------------------
    //加密的函数类型接口
interface encrypt {
    (key: string, value: string): string;
}

var md5: encrypt = function (key: string, value: string): string {
    //模拟操作
    return key + "----" + value;
}
console.log(md5("name", "zhangsan"));

var sha1: encrypt = function (key: string, value: string): string {
    //模拟操作
    return key + "====" + value;
}
console.log(sha1("name", "lisi"));

---------------------------------------------------
    //可索引接口，对数组的约束
interface UserArr {
    [index: number]: string
}
var arr1: UserArr = ["aaa", "bbb"];
console.log(arr1[0]);

//可索引接口，对对象的约束
interface UserObj {
    [index: string]: string
}
var arr2: UserObj = { name: '张三', age: '21' };
console.log(arr2);
---------------------------------------------------
    interface Animal {
    name: string;
    eat(str: string): void;
}

class Dog implements Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    eat() {
        console.log(this.name + "吃大骨头");
    }
}

var d = new Dog("小狼狗");
d.eat();

class Cat implements Animal {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    eat(food: string) {
        console.log(this.name + "吃" + food);
    }
}

var c = new Cat("小花猫");
c.eat("大老鼠");

```

### 接口的继承

接口可以继承接口，接口之间和抽象类之间的继承都是单向单继承，但是实现接口的子类可以实现多个接口。

//简单来说，对于类、抽象类、接口继承只能单继承，但接口可以继承多个对象，如下面代码，前端工程师可以继承 Person ， Web多个接口

```ts
//人这个接口
interface Person {
    eat(): void;
}

//程序员接口
interface Programmer extends Person {
    code(): void;
}

//小程序接口
interface Web {
    app(): void;
}

//前端工程师
class WebProgrammer implements Person, Web {
    public name: string;
    constructor(name: string) {
        this.name = name;
    }
    eat() {
        console.log(this.name + "下班吃饭饭")
    }
    code() {
        console.log(this.name + "上班敲代码");
    }
    app() {
        console.log(this.name + "开发小程序");
    }
}

var w = new WebProgrammer("小李");
w.eat();
w.code();
w.app();
```

## 泛型

### 定义

泛型就是解决类、接口、方法的复用性，以及对不特定数据类型的支持。

### 泛型类

```ts
//类的泛型
class MinClas<T>{
    public list: T[] = [];
    add(value: T): void {
        this.list.push(value);
    }
    min(): T {
        var minNum = this.list[0];
        for (var i = 0; i < this.list.length; i++) {
            if (minNum > this.list[i]) {
                minNum = this.list[i];
            }
        }
        return minNum;
    }
}
//实例化类并且制定了类的T代表的类型是number
var m1 = new MinClas<number>();
m1.add(11);
m1.add(3);
m1.add(2);
console.log(m1.min());
//实例化类并且制定了类的T代表的类型是string
var m2 = new MinClas<string>();
m2.add('c');
m2.add('a');
m2.add('v');
console.log(m2.min());

```

### 泛型接口

```ts
//泛型接口
interface ConfigFn<T> {
    (value: T): T;
}

function getData<T>(value: T): T {
    return value;
}

var myGetData: ConfigFn<string> = getData;
console.log(myGetData('20'));

```

### 泛型类接口

```ts
//定义操作数据库的泛型类
class MysqlDb<T>{
    add(info: T): boolean {
        console.log(info);
        return true;
    }
}

//想给User表增加数据，定义一个User类和数据库进行映射
class User {
    username: string | undefined;
    pasword: string | undefined;
}
var user = new User();
user.username = "张三";
user.pasword = "123456";
var md1 = new MysqlDb<User>();
md1.add(user);

//想给ArticleCate增加数据，定义一个ArticleCate类和数据库进行映射
class ArticleCate {
    title: string | undefined;
    desc: string | undefined;
    status: number | undefined;
    constructor(params: {
        title: string | undefined,
        desc: string | undefined,
        status?: number | undefined
    }) {
        this.title = params.title;
        this.desc = params.desc;
        this.status = params.status;
    }
}

var article = new ArticleCate({
    title: "这是标题",
    desc: "这是描述",
    status: 1
});
var md2 = new MysqlDb<ArticleCate>();
md2.add(article);
```

## 修饰器

修饰器是一种特殊类型的声明，它能够被附加到类、方法、属性或参数上，可以修改类的行为，通俗来讲修饰器就是一个方法，可以注入到类、方法、属性或参数上来扩展类、方法、属性或参数的功能。常见的装饰器有：类装饰器、方法装饰器、属性装饰器、参数装饰器。

装饰器的写法：普通装饰器（无法传参）、装饰器工厂（可传参），装饰器是过去几年中JS最大的成就之一，已是ES7的标准特性之一。

### 类修饰器

#### 普通装饰器

```ts
function logClass(params: any) {
    console.log(params);//params就是当前类
    params.prototype.apiUrl = "我是动态扩展的属性";
    params.prototype.run = function () {
        console.log("我是动态扩展的方法");
    }
}

@logClass
class HttpClient {

}

var http: any = new HttpClient();
console.log(http.apiUrl);
http.run();

```

#### 装饰器工厂（可传参）

```ts
function logClass(params: string) {
    return function (target: any) {
        console.log(target);//target就是当前类
        console.log(params);//params就是当前类传递进来的参数
        target.prototype.apiUrl = params;
    }
}

@logClass("http://www.baidu.com")
class HttpClient {

}

var http: any = new HttpClient();
console.log(http.apiUrl);

```

### 属性修饰器



```TS
{
  "compilerOptions": {

    /* 基本选项 */
    "target": "es5",                       // 指定 ECMAScript 目标版本: 'ES3' (default), 'ES5', 'ES6'/'ES2015', 'ES2016', 'ES2017', or 'ESNEXT'
    "module": "commonjs",                  // 指定使用模块: 'commonjs', 'amd', 'system', 'umd' or 'es2015'
    "lib": [],                             // 指定要包含在编译中的库文件
    "allowJs": true,                       // 允许编译 javascript 文件
    "checkJs": true,                       // 报告 javascript 文件中的错误
    "jsx": "preserve",                     // 指定 jsx 代码的生成: 'preserve', 'react-native', or 'react'
    "declaration": true,                   // 生成相应的 '.d.ts' 文件
    "sourceMap": true,                     // 生成相应的 '.map' 文件
    "outFile": "./",                       // 将输出文件合并为一个文件
    "outDir": "./",                        // 指定输出目录
    "rootDir": "./",                       // 用来控制输出目录结构 --outDir.
    "removeComments": true,                // 删除编译后的所有的注释
    "noEmit": true,                        // 不生成输出文件
    "importHelpers": true,                 // 从 tslib 导入辅助工具函数
    "isolatedModules": true,               // 将每个文件做为单独的模块 （与 'ts.transpileModule' 类似）.

    /* 严格的类型检查选项 */
    "strict": true,                        // 启用所有严格类型检查选项
    "noImplicitAny": true,                 // 在表达式和声明上有隐含的 any类型时报错
    "strictNullChecks": true,              // 启用严格的 null 检查
    "noImplicitThis": true,                // 当 this 表达式值为 any 类型的时候，生成一个错误
    "alwaysStrict": true,                  // 以严格模式检查每个模块，并在每个文件里加入 'use strict'

    /* 额外的检查 */
    "noUnusedLocals": true,                // 有未使用的变量时，抛出错误
    "noUnusedParameters": true,            // 有未使用的参数时，抛出错误
    "noImplicitReturns": true,             // 并不是所有函数里的代码都有返回值时，抛出错误
    "noFallthroughCasesInSwitch": true,    // 报告 switch 语句的 fallthrough 错误。（即，不允许 switch 的 case 语句贯穿）

    /* 模块解析选项 */
    "moduleResolution": "node",            // 选择模块解析策略： 'node' (Node.js) or 'classic' (TypeScript pre-1.6)
    "baseUrl": "./",                       // 用于解析非相对模块名称的基目录
    "paths": {},                           // 模块名到基于 baseUrl 的路径映射的列表
    "rootDirs": [],                        // 根文件夹列表，其组合内容表示项目运行时的结构内容
    "typeRoots": [],                       // 包含类型声明的文件列表
    "types": [],                           // 需要包含的类型声明文件名列表
    "allowSyntheticDefaultImports": true,  // 允许从没有设置默认导出的模块中默认导入。

    /* Source Map Options */
    "sourceRoot": "./",                    // 指定调试器应该找到 TypeScript 文件而不是源文件的位置
    "mapRoot": "./",                       // 指定调试器应该找到映射文件而不是生成文件的位置
    "inlineSourceMap": true,               // 生成单个 soucemaps 文件，而不是将 sourcemaps 生成不同的文件
    "inlineSources": true,                 // 将代码与 sourcemaps 生成到一个文件中，要求同时设置了 --inlineSourceMap 或 --sourceMap 属性

    /* 其他选项 */
    "experimentalDecorators": true,        // 启用装饰器
    "emitDecoratorMetadata": true          // 为装饰器提供元数据的支持
  }
}

```

