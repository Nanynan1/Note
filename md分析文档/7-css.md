列表

- 无序列表：ul>li
- 有序列表：ol>li
- 自定义列表：dl>dt dd

font: font-style font-weight font-size font-family

text:（会继承）

- text-align：文本对齐 center left right justify

  只要给父元素加center，里面的所有行内块、行内元素都会水平居中

- text-decoration：文本装饰 none underline overline line-through

- text-indent 文本缩进  2em

- line-height 行间距

#### 复合选择器

##### 后代选择器：

- 后代选择器``元素1 元素2`` 元素可以为任意一基础选择器
- 属性选择器``input[type="text"]`` 选择所有type属性为"text" 的<input>元素
- 子选择器``元素1>元素2`` 选择元素1里面的所有**直接**后代子元素2

##### 并集选择器 ``元素1, 元素2`` 

##### 链接伪类选择器

```css
a:link /*选择所有未被访问的链接*/
a:visited /*选择所有已被访问的链接*/
a:hover /*选择鼠标指针位于其上的链接*/
a:active /*选择鼠标按下未弹起的链接*/
注意：按照LVHA顺序写
```

##### ：focus 伪类选择器

用于选取获得焦点的表单元素

```css
input:focus {
	background-color: red;
}
```

#### 元素类别

块元素：<h1>~<h6> <p> <div> <ul> <ol> <li>

行内元素：<a> <strong> <b> <em> <i> <del> <s> <ins> <u> <span>

行内块元素：<img />  <input /> <td> 中间会有默认的空隙

#### 背景

- background-color
- background-image: url()
- background-repeat 背景平铺 repeat no-repeat repeat-x(在横向上平铺)            repeat-y
- background-position 背景图片位置 ``x y``
- background-attachment 背景图像固定 scroll滚动 fixed固定

复合写法：颜色 图片地址 平铺 滚动 图片位置（习惯性写法）

背景色半透明：``background: rgba(0,0,0,0.3)`` 最后一个参数是alpha透明值，取值范围在0~1之间

background-size: 图片的宽度 图片的高度

- 只写一个参数：一定是宽度 高度省略了 等比例缩放

- 里面的单位可以跟 % 是相对于父盒子来说

- cover要完全覆盖div盒子 可能有部分背景图片显示不全

  ``background-size: cover;``

- contain 高度和宽度等比例拉伸 当宽度 或者高度铺满后就不再拉伸

#### 三大特性

##### 1. 层叠性

就近原则

##### 2. 继承性

子标签会继承父标签的某些属性 text- font- line-开头 color

行高的继承：

```css
body {
	font: 12px/1.5 Microsoft YaHei;
}
/*行高：12*1.5 */
```

##### 3. 优先级

``!important`` -> ``行内样式style=""``1000 ->ID选择器100 -> 类选择器10->元素选择器1->继承0

**权重叠加**：复合选择器会有权重叠加的问题

#### 盒子模型

margin外边距  border边框  padding内边距  content内容

1. border：border-width border-style(solid实现 dashed虚线 dotted点线) border-color没有顺序

   ``border-collapse：collapse;`` 表示相邻边框合并在一起

2. padding：上 右 下 左  /  上 右左 下  /   上下 右左

   元素没有设置width时，padding不会撑开盒子的宽度

3. margin：同上

   **水平居中问题**：

   外边距可以让块级盒子水平居中，两个条件：盒子指定了width，盒子左右的外边距为auto

   ```css
   .hrader {width:960px; margin: 0 auto;}
   ```

   行内元素/行内块元素：给父元素添加 text-align: center 即可

   **嵌套块元素塌陷**：

   对于两个嵌套关系（父子）的块元素，父元素有上外边距同时子元素也有上外边距，此时父元素会塌陷较大的外边距值。

   解决方案：

   1. 为父元素定义上边框；transparent透明
   2. 为父元素定义上内边距；
   3. 为父元素添加``overflow: hidden``

##### 圆角边框

``border-radius: length`` 左上角 右上角 右下角 左下角

##### 盒子阴影

``box-shadow: h-shadow v-shadow blur spread color inset;``

- h-shadow：（必）水平阴影的位置
- v-shadow：（必）垂直阴影的位置
- blur：模糊距离
- spread：阴影的尺寸
- color：阴影的颜色
- inset：将外部阴影改为内部阴影

``box-shadow: 10px 10px 10px -4px rgba(0, 0 ,0, .3);``盒子阴影不占用空间

##### 文字阴影

``text-shadow: h-shadow v-shadow blur color;``

#### 浮动

``float: 值`` left right

特性：

1. 浮动元素会脱离标准流（脱标）

   > 脱离标准普通流的控制（浮） 移动到指定位置（动）
   >
   > 浮动的盒子不再保留原先的位置

2. 浮动的元素会一行内显示并且元素顶部对齐

3. 浮动的元素会具有行内块元素的特性

注意：

1. 浮动和标准流的父盒子搭配

   先用标准流的父元素排列上下位置，之后内部子元素采取浮动排列左右位置

2. 一个元素浮动了，理论上其余的兄弟元素也要浮动

   浮动的盒子只会影响浮动盒子后面的标准流，不会影响前面的标准流

##### 清除浮动

本质就是清除浮动元素脱离标准流造成的影响。如果父元素本身有高度，则不需要清除浮动。**清除浮动后，父元素就会根据浮动的子盒子自动检测高度。父级有了高度，就不会影响下面的标准流了**

``clear: 属性值``  left  right  both

清除浮动的策略是**闭合浮动**，只让浮动在父盒子内部影响，不影响父盒子外面的其他盒子。

清除浮动方法：

1. 额外标签法，也称为隔墙法

   在浮动元素末尾添加一个空的标签（**必须是块级元素**）

   ```html
   <div ...>...浮动元素</div>
   <div style="clear: both"></div>
   ```

2. 父级添加 overflow 属性

   给父元素添加 ``overflower: hidden/ auto/ scroll`` 中任意一个

   缺点：无法显示溢出的部分

3. 父级添加 after 伪元素

   :after 方式是额外标签法的升级版

   ```css
   /*单冒号是为了兼容低版本浏览器，写双冒号也可以*/
   .clearfix:after {
       content: "";
       display: block;
       height: 0;
       clear: both;
       visibility: hidden;
   }
   .clearfix {
       /*IE6、7 专用*/
       *zoom: 1;
   }
   ```

4. 父级添加双伪元素

   ```css
   .clearfix:before,
   .clearfix:after {
   	content: "";
   	diaplay: table; /*转为块级元素并一行显示*/
   }
   
   .clearfix:after {
   	clear: both;
   }
   
   .clearfix {
       *zoom: 1;
   }
   ```

#### css属性书写顺序

1. 布局定位属性：display/ position/ float/ clear/ visibility / overflow
2. 自身属性：width/ height/ margin/ padding/ border/ background
3. 文本属性：color/ font/ text-decoration/ text-align/ vertical-align/ white-space/ break-word
4. 其他属性：content/ cursor/ border-raduis/ box-shadow/ background:linear-gradient...

#### 定位

> 浮动可以让多个块级盒子一行没有缝隙排列显示，经常用于横向排列盒子
>
> 定位则是可以让盒子自由地在某个盒子内移动位置或者固定屏幕中某个位置

定位 = 定位模式 + 边偏移

**定位模式**用于指定一个元素在文档中的定位方式。边偏移决定了该元素的最终位置。

- static ：静态定位， 默认定位，就是按照标准流定位

- relative：相对定位，相对它原来的位置移动

  **特点**：

  1. 移动位置的时候参照点是自己原本的位置
  2. **不脱标**，继续保留原本位置

- absolute：绝对定位，相对它的祖先元素来移动

  **特点**：

  1. 如果没有祖先元素或祖先元素没有定位，则以浏览器为准定位
  2. 如果祖先元素有定位，则以最近一级的有定位祖先为准
  3. 绝对定位不再占有原先的位置（**脱标**）

- fixed：固定定位，是元素固定于浏览器可视区的位置。

  主要使用场景：可以在浏览器页面滚动时元素的位置不会改变

  特点：

  1. 以浏览器的可视窗口为参照点移动元素
     - 跟父元素没有任何关系
     - 不随滚动条滚动
  2. 固定定位不占有原先的位置（**脱标**）

##### 粘性定位 sticky 

可以被认为是相对定位和固定定位的混合

特点：

1. 以浏览器的可视窗口为参照点移动元素（固定定位特点）
2. 粘性定位占有原先的位置（相对定位特点）
3. 必须添加top、left、right、bottom其中一个才有效

##### 固定到版心右侧：

```css
.fixed {
    position: fixed;
    /* 1. 走浏览器宽度的一半 */
    left: 50%;
    /* 2. 利用margin走版心盒子宽度的一半 */
    margin-left: 400px;
}
```

##### 绝对定位的盒子居中

```css
.fixed {
    position: absolute;
    /* 1. 走父盒子宽度的一半 */
    left: 50%;
    /* 2. 利用margin走自己盒子宽度的一半 */
    margin-left: 100px;
    weight: 200px;
}
/*实现水平居中*/
```

##### 子绝父相

总结：因为父级需要占有位置，因此是相对定位，子盒子不需要占有位置，则是绝对定位。

##### 定位特殊特性

1. 行内元素添加绝对或相对定位，可以直接设置高度和宽度。
2. 块级元素添加绝对或相对定位，如果不给宽度或高度，默认大小是内容的大小

##### 绝对定位（固定定位）会完全压住盒子

浮动元素不同，浮动只会压住它下面标准流的盒子，但是不会压住下面标准流盒子里面的文字（图片）

但是绝对定位（固定定位）会压住下面标准流所有的内容。

#### 元素的显示和隐藏

1. display显示隐藏
   - display: none; 隐藏元素（不再占有位置）
   - display: block; 除了转换为块级元素之外，同时还有显示元素的意思
2. visibility显示隐藏
   - visibility: visible; 元素可视
   - visibility: hidden; 元素隐藏（位置继续占有）
3. overflow溢出显示隐藏
   - overflow: visible; 溢出也照常显示
   - overflow: hidden; 隐藏溢出部分（有定位的盒子，谨慎使用）
   - overflow: scroll; 溢出部分显示滚动条，不溢出也显示滚动条
   - overflow: auto; 自适应

#### 用户界面样式

##### 鼠标样式 cursor

- default 小白 默认
- pointer 小手
- move 移动（十字架那种形状）
- text 文本（I）
- not-allowed 禁止

##### 表单轮廓线

``input {outline: none;}    或者  outline: 0;`` 可以去掉默认的蓝色边框域

##### 防止文本域拖拽

``textarea {resize: none;}``

##### 文字不能选中

``user-select: none;``

#### Vertical-align 属性应用

经常用于设置图片或者表单元素（行内块元素）与文字垂直对齐

**只针对行内元素或者行内块元素**

- baseline 默认，元素放置在父元素的基线上
- top 把元素的顶端与行中最高元素的顶端对齐
- middle 放置在父元素的中部
- bottom 把元素的顶端与行中最低元素的顶端对齐

##### 解决图片底部默认空白缝隙问题

原因：行内块元素会和文字的基线对齐

解决方法：

1. 给图片添加``vertical-align: middle / top / bottom;``
2. 把图片转换成块级元素``display:block;``

#### 溢出的文字省略号显示

##### 1. 单行文本溢出显示省略号

必须满足三个条件：

```css
/*1. 先强制一行内显示文本*/
white-space: nowrap; (默认 normal 自动换行)
/*2. 超出部分隐藏*/
overflow: hidden;
/*3. 文字用省略号替代超出的部分*/
text-overflow: ellipsis;
```

##### 2. 多行文本溢出显示省略号

具有较大兼容性问题，适合于webKit（内核）浏览器或移动端

```css
overflow: hidden;
text-overflow: ellipsis;
/*弹性伸缩盒子模型显示*/
display: -webkit-box;
/*限制在一个块元素显示的文本的行数*/
-webkit-line-clamp: 2;
/*设置或检索伸缩盒对象的子元素的排列方式*/
-webkit-box-orient: vertical;
```

#### 常用布局技巧

##### 1. margin负值运用

两个盒子（都有边框时）并排，会造成 1+1>2 

让每个盒子margin往左侧移动 -1px 正好压住相邻盒子边框

鼠标经过某个盒子时候，提高当前盒子的层级（如果没有定位，则添加相对定位-占有位置，如果有定位，则加z-index

##### 2. 文字围绕浮动元素

##### 3. 三角强化

```css
width: 0;
height: 0;
/*把上边框宽度调大*/
border-top: 100px solid transport;
border-right: 50px solid skyblue;
/*左边和下边的边框宽度设置为0 */
border-bottom: 0 solid blue;
border-left: 0 solid green;
```

#### HTML5新增特性

##### 1. 1 新增的语义化标签

``<header>  <nav>导航标签  <article>内容标签  <section>定义文档某个区域  <aside>侧边栏标签  <footer>尾部标签``

##### 1.2 新增的多媒体标签

``<video src="" controls>视频标签`` muted静音

``<audio src="" controls="controls">音频标签`` autoplay自动播放  loop循环播放

##### 1.3 新增input类型

- type = "email"：限制用户输入必须为Email类型
- type = "url"：必须为URL类型
- date：日期类型
- time：时间类型
- month：月类型
- week：周类型
- number：数字类型
- tel：手机号码
- search：搜索框
- color：生成一个颜色选择框

##### 1.4 新增表单属性

- required：该内容不能为空

- placeholder：提示文本

  可以通过 ``input::placeholder {}`` 修改里面字体样式

- autofocus：自动聚焦属性

- autocomplete：off/on 当用户在字段开始键入时，浏览器基于之前键入过的值，应该显示出在字段中填写的表单  默认打开

- multiple：可以多选文件提交

#### CSS新增选择器

##### 1. 属性选择器（权重为10）

``input[value] {}`` 必须是input，同时具有value这个属性

``input[type="text"] {}`` 必须是input，同时具有type="text"

``input[type^="icon"] {}`` 以icon开头

``input[type$="icon"] {}`` 以icon结尾

``input[type*="val"] {}`` 含有val

##### 2. 结构伪类选择器

- ul li:first-child 选择ul中的第一个li孩子

- :last-child：最后一个

- ul li:nth-child(n) : ul 的第n个 li 孩子

  1. n可以为数字(从1开始计算)

  2. n可以为 even偶数、odd奇数

  3. n可以为公式(从0开始计算)

     | n    | 2n   | 2n+1 | 5n        | n+5      | -n+5  |
     | ---- | ---- | ---- | --------- | -------- | ----- |
     | 全选 | 偶数 | 奇数 | 5，10,... | 5,6,7... | 前5个 |

- E:first-of-type    :last-of-type  :nth-of-type(n)使用同上

- nth-child 会把所有的盒子都排列序号，执行时候先看nth-child(n)，再看前面的元素E；first-of-type 只把指定元素进行排列序号

##### 3. 伪元素选择器(权重为1)

可以帮助我们利用CSS创建新标签元素，而不需要HTML标签。

| 选择符   | 简介                     |
| -------- | ------------------------ |
| ::before | 在元素内部的前面插入内容 |
| ::after  | 在元素内部的后面插入内容 |

必须有 content 属性，属于行内元素

``.tudou:hover::before {}`` 鼠标经过 土豆 这个盒子，就让::before显示出来

#### CSS3 盒子模型

1. ``box-sizing: content-box;`` 盒子大小为width+padding+border
2. ``box-sizing: border-box;`` 盒子大小为width

#### CSS3其他特性

##### filter滤镜

``filter: blur(5px);`` 数值越大越模糊

brightness(%)  超过100%则越明亮

##### calc函数

```css
.son {
    /*永远比父盒子小 30px */
    width: calc(100% - 30px);
}
```

##### 过渡

当元素从一种样式变换为另一种样式时为元素添加效果

``transition: 要过渡的属性 花费时间 运动曲线 何时开始(默认是0s);``

**谁要过渡给谁加**

```css
div {
	width: 200px;
    height: 200px;
    background-color: pink;
    transition: width .5s, height .5s ease 1s;
    /*如果想要多个属性都变化，属性写 all 就可以了*/
}
```

#### transform转换

可以实现元素的位移、旋转、缩放

- 移动：translate

  ```css
  transform: translate(x,y); /*x,y轴上移动位置*/
  /*或者分开写*/
  transform: translateX(n);
  transform: translateY(n);
  ```

  **最大的优点**：不会影响其他元素的位置

  translate中的百分比单位是相对于元素自身来说：``transform: translate(-50%, -50%);`` 垂直居中

  **对行内元素没有效果**。

- 旋转：rotate

  让元素在2维平面内顺时针（正数）旋转或逆时针（负数）。

  ``transform: rotate(45deg);``

  转换中心点``transform-origin : x y;`` 可以设置像素或者方位名词

- 缩放：scale

  ``transform: scale(x,y);`` 大于1就是宽高分别放大几倍，小于1就是缩小

  优势：可以设置中心点缩放，默认以中心点缩放的，而且不影响其他盒子

##### 2D转换综合写法

格式：``transform: translate() rotate() scale()...``

一定先移动后旋转

#### CSS动画

可以设置多个节点来精确控制一个或一组动画，常用来实现复杂的动画效果。

相比较过渡，动画可以实现更多变化，更多控制，连续自动播放等效果

> 制作动画分为两步：
>
> 1. 先定义动画
> 2. 再调用动画

```css
/*定义动画*/
@keyframes move {
    /*开始状态 等价于from */
    0% {
        transform: translateX(0px);
    }
    /*结束状态 等价于to*/
    100% {
        transform: translateX(1000px);
    }
}

/*调用动画*/
div {
    animation-name: move; /*动画名称*/
	animation-duration: 2s; /*持续时间*/
}
```

##### 动画中常见属性

| 属性                      | 描述                                                      |
| ------------------------- | --------------------------------------------------------- |
| @keyframes                | 规定动画                                                  |
| animation                 | 所有动画属性的简写，除了animation-play-state属性          |
| animation-name            | 动画名称（必须）                                          |
| animation-duration        | 规定动画完成一个周期所花费时间（必须）                    |
| animation-timing-function | 规定动画的速度曲线，默认是"ease"，linear匀速，steps()步长 |
| animation-delay           | 动画开始时间，默认0                                       |
| animation-iteration-count | 动画被播放次数，默认是1，还有infinite（无限）             |
| animation-direction       | 是否在下一周期逆向播放，默认normal，逆播放alternate       |
| animation-play-state      | 动画是否正在运行或暂停，默认running，还有pause            |
| animation-fill-mode       | 规定动画结束后状态，保持forwards  回到起始backwards默认   |

 简写：``animation: 动画名称 持续时间 运动曲线 何时开始 播放次数 是否反方向 动画结束状态`` 

#### 3D转换

##### 三维坐标系

z轴：往外面是正值，往里面是负值

##### 3D移动 translate3d

```css
transform: translate3d(x,y,z);
transform: translateZ(100px); /*向外移动100px（向我眼睛来移动）*/
/*z值越大，看得越大*/
```

##### 透视 perspective

用于在2D平面产生近大远小视觉立体

透视写在被观察元素的父盒子上面``perspective: 200px`` 值越小，元素越大

##### 3D旋转 rotate3d

```css
transform: rotateX(180deg); /*沿着x轴正方向旋转180度 左手原则*/
transform: rotateY(180deg);/*左手原则：拇指指向y轴方向*/
transform: rotateZ(45deg);/*沿着Z轴正方向旋转45deg*/

transform: rotate3d(x,y,z,deg);/*沿着自定义轴旋转 deg为角度*/
rotate3d(1, 0, 0, 45deg);
```

##### 3D呈现 transform-style

控制子元素是否开启三维立体环境。代码写给父级，但影响的是子盒子。

``transform-style: preserve-3d`` 子元素开启立体空间   flat不开启，默认

##### 背面隐藏 

在两个div重叠在一处，进行翻转时候

``backface-visibility: hidden;``

#### 浏览器私有前缀

- -moz-：代表 firefox 浏览器私有属性
- -ms-：代表ie浏览器私有属性
- -webkit-：代表safari、chrome私有属性
- -o-：代表Opera私有属性

#### meta视口标签

```html
<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
<!--初始缩放比，最大缩放比，最小缩放比-->
```

#### 移动端常见布局

##### 1. 单独制作移动端页面（主流）

- 流式布局（百分比布局）
- flex弹性布局
- less+rem+媒体查询布局
- 混合布局

##### 2. 响应式页面兼容移动端（其次）

- 媒体查询
- bootstrap

##### 1.1 流式布局

max-width 最大宽度 （max-height 最大高度）

min-width 最小宽度（min-height 最小高度）

##### 1.2 flex布局

任何一个容器都可以指定为flex布局

通过给父盒子添加flex属性，来控制子盒子的位置和排列方法

- 当我们为父盒子设为flex布局以后，子元素的float、clear和vertical-align 属性将失效。

###### flex布局父项常用属性：

| 属性            | 描述                                                       |
| --------------- | ---------------------------------------------------------- |
| flex-deirection | 设置主轴的方向（默认"row" -x轴右侧）                       |
| justify-content | 设置**主轴**上的子元素排列方式                             |
| flex-wrap       | 设置子元素是否换行（默认不换行）                           |
| align-content   | 设置**侧轴**上的子元素的排列方式（多行）子项出现**换行**   |
| align-items     | 设置**侧轴**上的子元素排列方式（单行（**换行**））默认拉伸 |
| flex-flow       | 集合属性，相当于同时设置了flex-direction和flex-wrap        |

1. flex-direction：row-reverse(从右到左)  column(从上到下)  column-reverse(从下到上)

2. justify-content：

   - **flex-start** 默认从头部开始；
   - flex-end 从尾部开始排列
   - **center** 在主轴居中对齐（如果主轴是x轴，则水平居中）
   - **space-around** 平分剩余空间
   - **space-between** 先两边贴分，再平分剩余空间

3. flex-wrap：

   - nowrap 默认不换行，如果装不下则压缩其他盒子
   - wrap 换行

4. align-items:

   - flex-start  侧轴从头开始
   - flex-end 从下到上
   - center  居中（挤在一起居中）
   - stretch  拉伸，但子盒子不要给高度

5. align-content：属性值与 justify-content 相符合

6. flex-flow：

   ``flex-flow: row nowrap;``

###### 子项常见属性

1. flex属性：定义子项目分配剩余空间，用flex来表示占多少份数

   ``flex: <number>;``

2. align-self：控制子项自己在侧轴上的排列方式

3. order：定义项目的排列顺序

   数值越小，排列越靠前，默认是0

#### rem布局

##### 1. rem是什么

em相对于父元素的字体大小来说的

rem相对于**html元素**的字体大小来说的

##### 2. 媒体查询

可以针对不同的屏幕尺寸设置不同的样式

```css
@media mediatype and|not|only (media featrue) {
    CSS-Code;
}
mediatype:媒体类型--all所有设备 print用于打印机和打印预览 scree用于电脑屏幕，平板电脑，智能手机等
and|not|only：关键字
media featrue：媒体特性 必须小括号包含--width max-width min-width
```

```css
@media scree and (max-width: 800px) and (min-width: 540px){
    body {
        background-color: pink;
    }
}
```

**引入资源**：当样式比较繁多的时候，我们可以针对不同的媒体使用不同stylesheets

原理，就是直接在link中判断设备的尺寸，然后引用不同的css文件

```html
<link rel="stylesheet" media="mediatype and|not|only (media feature)" href="mystylesheet.css">
```

#### Less

Less是一门CSS扩展语言，也成为CSS预处理器。在CSS的语法基础上，引入了变量、混入、运算以及函数等功能

```less
@color:pink;
body {
    background-color:@color;
}
区分大小写
```

less嵌套

less运算  可以加减乘除 

1. **两边必须空格隔开**
2. 两个数参与运算 如果只有一个数有单位，则最后结果就以这个单位
3. 如果两个数都有单位，且单位不一致，以第一个数的单位为准

##### rem实际开发适配方案1

less+媒体查询+rem

```css
@media screen and (min-width:320px) {
    html {
        font-size: 21.33px;
    }
}
@media screen and (min-width:750px) {
    hrml {
        font-size:50px;
    }
}
div {
    width:2rem;
    height:2rem;
}
```

页面元素的rem值 = 页面元素值（px）/ html font-size字体大小

##### rem实际开发适配方案2

flexible.js + rem

#### 响应式开发

原理：使用**媒体查询**针对不同宽度的设备进行布局和样式的设置，从而适配不同设备的目的。

| 设备划分                 | 尺寸区间                   |
| ------------------------ | -------------------------- |
| 超小屏幕（手机）         | < 768px  (100%)            |
| 小屏设备（平板）         | >= 768px ~ <992px  (750px) |
| 中等屏幕（桌面显示器）   | >=992px ~ <1200px  (970px) |
| 宽屏设备（大桌面显示器） | >= 1200px  (1170px)        |



#### 单位

vw与vh是相对单位，相对视口的尺寸计算结果

vmin和vmax是与当下屏幕的宽度和高度的最大值或最小值有关 取决于哪个更大和更小

1 vw = 1/100 视口宽度

vw：viewport width

vh：viewport height
