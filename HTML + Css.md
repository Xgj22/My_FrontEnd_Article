## **A1**）CSS 垂直居中及水平居中

行内元素
1.和其他元素都在同一行
2.高，行高及外边距和内边距部分可以改变
3.宽度只与内容有关
4.行内元素只能容纳文本或者其他行内元素
5.a,img,input,lable,select,sapn,textarea,font

块级元素
1.总是在新行上开始，占据一整行
2.高度，行高以及外边距和内边距都课控制
3.宽度始终与浏览器的宽度一样，与内容无关
4.可以容纳行内元素和其他块级元素
5.div,p,table,form,h1,h2,h3,dl,ol,ul,li

居中方式分为三种：
水平居中
垂直居中
水平垂直居中
水平居中
1.行内元素水平居中
利用text-align:center可以实现行内元素水平居中
对行内元素(inline)，行内块(inline-block)，行内表(inline-table)，inline-flex也有效果。

<div class="center-text">
    简单是稳定的前提。
</div>
```css
div {
    height:60px;
    border: 2px dashed #f69c55;
}
.center-text {
    text-align: center;
}
```

2.块级元素水平居中
通过把固定宽度的块级元素的margin-left和margin-right设成auto,就可以使块级元素水平居中

```css
div {
    height:100px;
    border: 2px dashed #f69c55;
}
.center-block {
    margin: 0 auto;
    width: 8rem;
    padding:1rem;
    color:#fff;
    background:#000;
}
```





3.多块级元素水平居中
方法一：利用inline-block
通过改变块级元素为inline-block和父容器的text-align属性来实现多块级元素水平居中

<div id="app">
        <div class="center-block">
           第一个块级元素
        </div>
        <div class="center-block">
            第二个块级元素
        </div>
        <div class="center-block">
            第三个块级元素
        </div>
    </div>
#app {
    height:100px;
    border: 2px dashed #f69c55;
    text-align: center;
}
.center-block {
     

```css
width: 200px;
padding:1rem;
color:#fff;
background:#000;
display: inline-block;   
```
}



方法二：利用flex布局

```html
<div id="app">
    <div class="center-block">
        第一个块级元素
    </div>
    <div class="center-block">
        第二个块级元素
    </div>
    <div class="center-block">
        第三个块级元素
    </div>
</div>
```
```css
#app {
    height: 100px;
    border: 2px dashed #f69c55;
    display: flex;
    justify-content: center;
}

.center-block {
    width: 200px;
    padding: 1rem;
    color: #fff;
    background: #000;
}
```






垂直居中
1.单行行内元素垂直居中
通过设置内联元素的高度(height)和行高(line-height)相等，从而使元素垂直居中

  <div id="app">
            第一个块级元素
    </div>
```css
#app {
    height: 100px;
    border: 2px dashed #f69c55;
    line-height: 100px;
}
```





2.多行元素垂直居中
方法一：利用表格布局
利用表格布局的vertical-align:middle可以实现子元素的垂直居中

```css
.center-table {
    display: table;
    height: 140px;
    border: 2px dashed #f69c55;
}
.v-cell {
    display: table-cell;
    vertical-align: middle;
}
```



方法二：利用flex布局
利用flex布局实现垂直居中

```css
.center-flex {
    height: 140px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    border: 2px dashed #f69c55;
}
```




3.块级元素垂直居中
固定高度的块级元素
 已知居中元素的高度和宽度，通过绝对定位元素距离顶部50%，并设置margin-top向上偏移元素高度的一半，就可实现垂直居中。

```csss
.parent {
    height: 140px;
    position: relative;
    border: 2px dashed #f69c55;
}
.child {
    position: absolute;
    top: 50%;
    height: 100px;
    margin-top: -50px;
    color:#fff;
    background: #000;
}





.parent {
    height: 140px;
    position: relative;
    border: 2px dashed #f69c55;
}
.child {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    background: black;
    color: #fff;
    padding: 1rem;
    width: 12rem;
}
```






水平垂直居中
1.固定宽高水平垂直居中
利用margin，定位(多样，不再赘述)

2.未知宽高元素水平垂直居中
利用2D变化，在水平和垂直方向都反向平移宽高的一半

3.利用flex布局
 利用flex布局，其中justify-content用于设置或检索弹性盒子元素在主轴上方向上的对齐方式；而align-items属性定义flex子项在flex容器的当前行的侧轴方向上的对齐方式。

## 2）CSS盒子模型

1.什么是盒子模型？

盒子模型就是网页设计中CSS技术所使用的一种思维模型。网页中所有元素都具备以下四个属性：首先包含 外边距（margin）边框（border）内边距（padding）内容（content）也就是元素本身的width，height。它主要针对页面布局来使用，规范了我们页面的所有元素都是由外向内进行布局。

有两种盒模型：IE盒模型（也称怪异盒模型）（border-box）、W3C标准盒模型（content-box）

IE盒模型和W3C标准盒模型的区别：

W3C标准盒模型：属性width，height只包含内容content，不包含border和padding。

IE盒模型：属性width，height包含content、border和padding，指的是content +padding+border

#### 2.1）面试题--两种不同盒模型的高度计算

W3C 标准盒模型的width不包含border和padding，因此计算宽度时需要加上

怪异盒模型（IE盒模型）包含了border和padding，它不由子元素撑开，所以宽度直接是width

## 3） Flex（Flexible Box）布局

flex布局是CSS3 中的新布局模块。它可以改善容器中物品的对齐，方向和顺序。flex容器的主要特征是能够修改其子项的宽度和高度，以及在不同的屏幕尺寸上以最佳方式填充可用空间

1. flex-direction   主轴方向

2. flex-wrap        主轴一行满了换行

3. flex-flow        1和2的组合

4. justify-content  主轴元素对齐方式

5. align-items      交叉轴元素对齐方式//单行

6. align-content    交叉轴行对齐方式//多行

  

## 4) CSS 常见面试题

##### 4.1）什么是响应式网页设计？

响应式网站设计是一种网络页面设计布局，其理念是：集中创建页面的图片排版大小，可以智能地根据用户行为以及使用的设备环境进行相应的布局

##### 4.2）{visibility：hidden}和{display：none}有什么区别？

简单来说，visibilty设置为hidden表示将该DOM元素隐藏起来，但是在页面上为其分配了空间，仍然占据着文档流。而display设置为none则表示标签根本不会出现在页面上，不会占据文档流。

##### 4.3） CSS 如何实现元素的定位

position属性指定用于元素的定位方法的类型。
有五个不同的位置值：
position: static; 默认值，意味着将元素放在文档流的正常位置，并没有什么特别的
position: relative; relative 相对的，参照物是元素原来的位置，即相对于自己在标准文档流中的位置进行移动
position: absolute; absolute 比较特别，绝对定位的元素不再存在于正常文档布局流中，它坐在自己的层独立于一切，然后使用left right等进行移动，参照物是最接近的一个具有定位属性的父包含块（一般父包含块都是开启相对定位），如果不存在这样的包含块，则相对于body元素（浏览器窗口）
position: fixed; fixed（固定）与absoulte 定位相似，不同的是它的参照物是视图本身（屏幕内的网页窗口），脱离文档流
position: sticky; 粘性定位，sticky允许被定位的元素表现得像相对定位拥有，直到它滚动到某个阈值，它就变得固定了。我们可以用它来做导航栏滚到某个特定点就粘在页面顶部

```css
*{
    margin: 0;
}
.box {
    position: relative;
    margin: 0 auto;
    border: 2px solid #1790FF;
    padding-top: 40px;
    width: 400px;
    height: 1500px;
    background: #f5f5f5;
}
.head{
    position: sticky;
    top: 0;
    left: 0;
    width: 100%;
    line-height: 40px;
    color: #fff;
    background: #1790FF;
}

```



##### 4.4）自适应正方形

实现自适应的正方形效果，既让元素高度随宽度改变

有两种方式，

- 一种是使用子元素的padding-top或者padding-bottom撑开父元素高度，因为子元素的padding的百分比是相对于父元素的宽度。

- 另一种方法是用子元素的伪类将父元素高度撑开

  padding-top属性会随着宽度的改变而改变，是因为CSS3中的百分比值是相对于包含块的宽度来计算的。在这个例子中，由于元素的宽度是一个百分比值，因此它的包含块就是父元素的宽度。当我们将padding-top的值设置为一个百分比值时，它所参照的就是父元素的宽度，而不是父元素的高度。因此，随着元素宽度的改变，padding-top的值也会随之改变，从而实现了一个高度随着宽度改变的自适应正方形。

##### 4.5）float 和position 的区别

float 和 position 是两种不同的布局方式，float 可以块级元素横线布局，文本元素浮动在它左右。position 是相对其他其他元素定位。两者的层级不同，float<<position。

两者适用场景不同，float用于图片周围浮动文本、信息流等场景，position用在固定某个元素位置，如导航栏等场景

##### 4.6）伪类和伪元素

伪类作用的对象是整个元素，例如 hover active 之类，将触发某些操作的元素添加新的样式

伪元素其实相当于伪造了一个元素，例如 before，first-letter 达到的效果就是伪造了一个元素，然后添加了其相应的效果而已；而伪类没有伪造元素，例如 first-child 只是给子元素添加样式而已。

##### 4.7）px、em、rem的区别

px 是固定的像素，一旦设置了就无法因为适应页面大小而改变

em和rem更具灵活性，他们是相对长度单位，更符合响应式页面布局的思想。

em是相对父元素来设置字体大小的，例如 font-size：2em 就是父元素的两倍。而其他属性例如width/height/padding/margin用em的话是相对于该元素的font-size（如果没有就继承父级），假如font-size 换算成 px 是10px，那么 height ：5em 就是50px。这样就会存在一个问题，进行任何元素设置都需要知道他父元素的大小。

而 rem 是相对根元素<html>,这样我们只需要在根元素设置一个参考值，例如根元素 10px，那么后面需要用到长度定位都用 rem，10rem 就是 100px

rpx是小程序独有的解决屏幕自适应的尺寸单位，通过rpx设置元素和字体的大小，可以实现自动适配

##### 4.8）解释浏览器如何确定哪些元素与 CSS 选择器匹配

浏览器从最右边的选择器（关键选择器）根据关键选择器，浏览器从 DOM 中筛选出元素，然后向上遍历被选元素的父元素，判断是否匹配。选择器匹配语句链越短，浏览器的匹配速度越快。避免使用标签和通用选择器作为关键选择器，因为它们会匹配大量的元素，浏览器必须要进行大量的工作，去判断这些元素的父元素们是否匹配

##### 4.9）响应式设计与自适应设计有何不同？

响应式设计和自适应设计都以提高不同设备间的用户体验为目标，根据视窗大小、分辨率、使用环境和控制方式等参数进行优化调整。

响应式设计的适应性原则：网站应该凭借一份代码，在各种设备上都有良好的显示和使用效果。响应式网站通过使用媒体查询，自适应栅格和响应式图片，基于多种因素进行变化，创造出优良的用户体验。就像一个球通过膨胀和收缩，来适应不同大小的篮圈。

自适应设计更像是渐进式增强的现代解释。与响应式设计单一地去适配不同，自适应设计通过检测设备和其他特征，从早已定义好的一系列视窗大小和其他特性中，选出最恰当的功能和布局。与使用一个球去穿过各种的篮筐不同，自适应设计允许使用多个球，然后根据不同的篮筐大小，去选择最合适的一个。

##### 4.10）为什么要初始化 CSS 样式

因为浏览器的兼容问题，不同浏览器对有些标签的默认值是不同的，如果没对 CSS 初始化往往会出现浏览器之间的页面显示差异。具体来说，初始化 CSS 样式可以清除元素的默认样式并提供一组统一的基础样式，以确保在不同浏览器上元素的样式表现一致，这样可以避免由于浏览器差异而导致的样式不一致，从而提高网页的可维护性和可靠性。

## 5) BFC (Block Formatting Context) 块级格式上下文

一个BFC区域包含创建该上下文的所有子元素，但是不包含创建了的新的 BFC 子元素的内部元素，BFC是一块块独立的渲染区域，可以将BFC看成元素的一种属性，拥有这种元素就与世隔绝，不会影响到外部其他元素。

**总结:
1,每一个BFC区域只包括其子元素，不包括其子元素的子元素。
2,每一个BFC区域都是独立隔绝的,互不影响
第一条就是字面意思，第二条直接看代码理解**

##### 5.1) 怎么使一个元素变成 BFC 区域

**·body根元素**

**·设置浮动，不包括none
·设置定位，absoulte或者fixed
·行内块显示模式，inline-block
·设置overflow，即hidden，auto，scroll
·表格单元格，table-cell**

**·弹性布局，flex**

##### 5.2）BFC的作用

1，解决高度塌陷的问题（1和2一般都是设置 父元素 overflow：hidden）

2，解决包含塌陷问题（子元素带着父元素一起跑）

3.清除浮动。

大家都知道，浮动会导致父元素高度塌陷，那大家还记得怎么清除浮动吗？相信很多人都知道：overflow：hidden嘛。相信在认识BFC之前大家肯定不太清楚为什么overflow：hidden可以清除浮动。现在知道了，BFC区域内的子元素任何边动都是不会影响到外部元素的。所以BFC区域同样可以清除浮动带来的影响。这里就不上例子了

4.BFC可以阻止标准流元素被浮动元素覆盖

大家都知道，浮动的元素会脱离文档流，跑到上一个层面，也就是和原本的元素们不在一个层面了。所以可能会导致浮动元素覆盖基本元素的问题。

## 6）IFC （Inline Formatting Contexts）行内级格式化上下文

行内格式化上下文是一个网页的渲染结果的一部分。其中，各行内框（inline boxes）一个接一个地排列，其排列顺序根据书写模式（writing-mode）的设置来决定：

- 对于水平书写模式，各个框从左边开始水平地排列
- 对于垂直书写模式，各个框从顶部开始水平地排列

#### IFC应用场景

1）元素水平居中

当一个块要在环境中水平居中时，设置其为inline-block则会在外层产生IFC，通过text-align则可以使其水平居中。

2）多行文本水平垂直居中

创建一个IFC，然后设置其vertical-align:middle，其他行内元素则可以在此父元素下垂直居中。

## 7） CSS 优先级

#### css 优先级的计算规则，对于每条 CSS 规则，生成一个优先级四元组<内联，ID，class|属性|伪类|元素选择器>

##### 为什么不推荐使用多层选择器？ 答：性能太低，选择器的匹配需要在整个DOM树中查找节点，层级越高性能越低

##### 选择器的匹配是从右向左匹配的，先匹配子节点，避免遍历DOM树，这样性能更好

## 8）HTML 语义化标签

```html
<header>
    <nav></nav>//导航标签
</header>
<main>
    <aside></aside> //侧边栏标签
    <article></article>//文章标签
</main>
<footer>
</footer>//页脚标签

```

## 9）FLEX 布局

### justify-content 主轴元素对齐方式

这里是元素的对齐，区别于刚刚的「方向」

flex-start (默认)靠着main-start对齐//参考常见术语(一般是左方向)
flex-end   靠着main-end对齐//参考常见术语(一般是右方向)
center     靠着主轴居中对齐//一般就是居中对齐
space-between 两端对齐，靠着容器壁，剩余空间平分
space-around  分散对齐，不靠着容器壁，剩余空间在每个项目二侧平均分配
space-evenly  平均对齐，不靠着容器壁，剩余空间平分

### align-items item「项目」在交叉轴上对齐方式「单轴」

这个是 container 容器的属性，设置的是 items 项目元素在交叉轴上对齐样式

flex-start：交叉轴的起点对齐。
flex-end：交叉轴的终点对齐。
center：交叉轴的中点对齐。
baseline: 项目的第一行文字的基线对齐。
stretch（默认值）伸展：如果项目未设置高度或设为auto，将占满整个容器的高度。

### align-content 交叉轴行对齐方式 多行

这个和 justify-content 属性一模一样，

justify-conent，align-items，align-content 都极度相似.

flex-start (每一行)(默认)靠着cross-start对齐//参考常见术语(一般是左方向)
flex-end   （每一行)靠着cross-end对齐//参考常见术语(一般是右方向)
center     (每一行)靠着cross线居中对齐//一般就是居中对齐
space-between (每一行)两端对齐，靠着容器上下壁，剩余空间平分
space-around  (每一行)分散对齐，不靠着容器壁，剩余空间在每个项目二侧平均分配
strentch      (每一行)伸缩，占满整个高度

## 项目元素 item 的属性

容器里面的子元素item「项目」的属性

```js
flex-grow：长大
flex-shrinik: 缩小
align-self: 覆盖container align-items 属性
order 排序
flex-basis: 有效宽度
```

### flex-grow 长大

- 在容器主轴上存在剩余空间时, `flex-grow`才有意义
- 该属性的值,称为**放大因子**, 常见的属性值如下:

| 序号 | 属性值    | 描述                 |
| ---- | --------- | -------------------- |
| 1    | `0`默认值 | 不放大,保持初始值    |
| 2    | `initial` | 设置默认值,与`0`等效 |
| 3    | `n`       | 放大因子: 正数       |

###  flex-shrinik: 缩小

当容器主轴 “空间不足” 且 “禁止换行” 时, flex-shrink才有意义
该属性的值,称为收缩因子, 常见的属性值如下:

| 序号 | 属性值    | 描述                     |
| ---- | --------- | ------------------------ |
| 1    | `1`默认值 | 允许项目收缩             |
| 2    | `initial` | 设置初始默认值,与 1 等效 |
| 3    | `0`       | 禁止收缩,保持原始尺寸    |
| 4    | `n`       | 收缩因子: 正数           |

### align-self: 覆盖container align-items 属性

该属性可覆盖容器的align-items, 用以自定义某个项目的对齐方式

| 序号 | 属性值       | 描述                           |
| ---- | ------------ | ------------------------------ |
| 1    | `auto`默认值 | 继承 `align-items` 属性值      |
| 2    | `flex-start` | 与交叉轴起始线对齐             |
| 3    | `flex-end`   | 与交叉轴终止线对齐             |
| 4    | `center`     | 与交叉轴中间线对齐: 居中对齐   |
| 5    | `stretch`    | 在交叉轴方向上拉伸             |
| 6    | `baseline`   | 与基线对齐(与内容相关用得极少) |

### flex属性

flex属性是 flex-grow属性、flex-shrink属性、flex-basis属性的简写。默认值为：0 1 auto；

## 10）CSS哪些属性可以继承



在CSS中，有一些属性可以被子元素继承，这些属性被称为"可继承属性"。当一个元素没有指定某个可继承属性的值时，它会从它的父元素继承该属性的值。以下是一些常见的可继承属性：

- font-family：字体系列
- font-size：字体大小
- font-style：字体风格（如斜体）
- font-weight：字体粗细
- line-height：行高
- text-align：文本对齐方式
- color：文本颜色
- visibility：可见性

## Less 和 Sass

Less和Sass是两种流行的CSS预处理器，它们都提供了一些扩展和增强CSS的功能，但有一些区别。

1. 语法差异：Less使用类似于CSS的语法，而Sass使用了更加强大和灵活的语法。Sass使用缩进和嵌套来表示层级关系，而Less使用大括号和选择器来表示。Sass还支持使用`@`符号（less也是用 @，同时有extend继承）和特殊字符来定义变量、混合（Mixins）、函数等。
2. 文件扩展名：Less的文件扩展名为`.less`，而Sass可以使用两种不同的语法，分别对应不同的文件扩展名。`.sass`文件使用缩进语法，`.scss`文件使用类似于CSS的语法。
3. 兼容性：由于Less的语法更接近CSS，它可以更容易地使用现有的CSS代码进行逐步迁移和集成。Sass的语法相对更加强大和灵活，但在某些情况下可能需要更多的学习和适应成本。
4. 生态系统和社区支持：Less和Sass都有庞大的用户社区和活跃的开发者社区，提供了大量的文档、资源和工具库。在这方面，Sass的生态系统可能更为成熟和丰富一些。
5. 功能特性：Sass提供了一些高级特性，如嵌套规则、继承、条件语句等，这些功能在编写复杂的样式时非常有用。Less也提供了一些类似的功能，但相对较少。

## 11）伪元素和伪类的区别

伪元素，其核心就是需要创建通常不存在文档中的元素，比如:: before

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f9592a395a204e46bd791417263d70c8~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

伪类，其核心就是用来选择那些不能够被普通选择器选择的文档之外的元素，比如 :hover

![2.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5497c283a92a4c73832aa7648067b95f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

判断伪元素和伪类最关键的点在于是否需要去创建一个新的元素才能达到目的，是的话就是伪元素，否则就是伪类

例如说我要把一个文本节点的第一个字母或者第一行设置成红色且字号变大，显然这利用 CSS selector 是无法实现的

```html
<p>我是p标签中的文字</p>
这里想把第一个字母设置成红色，想破头都不知道怎么选择到它
但是如果像下面这样写呢
<p><span :style="{color:red;}">我</span>是p标签中的文字</p>
```

由上可看出通过新创建的元素进而改变了第一个字母的颜色，成功了。这里我们通过创建新的元素达到了::first-letter 的作用，且不能通过添加其他类来实现这一效果，因此把 :: first-letter 叫做伪元素而不是伪类。同样的还有 ::first-line。

在这里，我们很容易把 :first-child、:nth-child(n) 等等跟伪元素搞混，尽管它们长得很像，还是回到关键点，需不需要去创建一个新的元素去达到目的，我们看以下代码

```html
<div id="parent">
    <p id="first">我是第一</p>
	<p>uu第二</p>
    <p>ppp第三</p>
</div>
```

如果我想要第一行元素变红，我们只需要添加以下 css 代码

```css
#first{
    color:red;
}
```

这样我们即可改变它的颜色，其效果等同于

```css
#parent:first-child{
    color:red
}
```

因此，first-child 是伪类

### **结论**

- 伪类和伪元素都是用来表示文档树以外的"元素"。
- 伪类和伪元素分别用单冒号`:`和双冒号`::`来表示。
- 伪类和伪元素的区别，最关键的点在于如果没有伪元素(或伪类)，是否需要添加元素才能达到目的，如果是则是伪元素，反之则是伪类。

## src 和 href 的区别

### src 的特性

1. 引用外部资源，比如 script 元素，img 元素，iframe 元素，video 元素

2. 会替代元素本身的内容

3. 会暂停其他资源的下载（当浏览器加载到使用 src 的元素时，会暂停其他资源的下载，直到src 引用资源的加载、编译、执行完毕。这就是为什么 script 元素推荐放在 html 结构的底部）

   扩展：通常把script 标签放到 html 结构底部，必要时还要加上 defer 和 async 异步加载。两者有以下区别：

   1. 执行顺序：async 加载完毕后会立即执行，不管 HTML 是否解析完成。而 defer 会推迟到 HTML  文档解析完成之后
   2. 阻塞效果：两者都不会阻塞 HTML 解析，但是 async 可能会在文档的任意时刻执行，所以适用于不依赖于 DOM 结构的脚本
   3. 多个 async 脚本的执行顺序是不固定的，多个 defer 脚本是按顺序依次执行的

### href 的特性

1. 表示超链接，比如 a 链接，link 标签，表示外部资源与该页面联系
2. 不会替代元素本身的内容
3. 不会暂停其他资源的下载（像 css 那种影响页面观感的可以放在html 的头部优先加载）

核心思想

src 表示网站的一部分，链接的是内部资源，没有会对网站的使用造成影响

href 代表的是网站的附属资源，链接的是外部资源，没有不会对网站的核心逻辑和结构造成影响
