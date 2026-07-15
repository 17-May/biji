# web基础笔记

# 浏览器

### 内核

|浏览器|内核|
| ------| ---------------|
|火狐|gecko|
|opear|webkit|
|safari|webkit|
|chrome|webkit分支blink|

# html（超文本标记语言）

### mate

- chartset：设置字符集（UTF-8）
- http-equiv="X-UA-Compatible"：设置文档兼容模式
- name=“viewport”：视窗设置，用于移动端
- name="robots"：用于SEO

### 标签

- h1-h6：可以用于SEO优化
- br：文字换行
- hr：下划线
- ol：有序列表
- ul：无序列表
- dl：自定义列表
  - dt：标题目录
  - dd：内容目录
- table：表格
  - tr：行
  - td：单元格
    - rowspan：合并，占x行
    - colspan：合并，占x列
  - th：头部
  - 语义化：
    - caption：标题
    - thead：表头
    - tbody：内容主体
    - tfoot：底部

### SEO（Search Engine Optimization）

- 含义：搜索引擎优化
- 作用：让网站在搜索引擎上的排名靠前
- 提升方法：
  1. 竞价排名
  2. 将网页制作成html后缀
  3. 标签语义化
- SEO三大标签，
  1. title：网页标题标签
  2. description：网页描述标签，用在mate
  3. keywords：网页关键词标签，用在mate

### 网页icon

```
<link rel="shortcut icon" href="favicon.ico" type="image/x-icon">
```

# css(层叠样式表)

### 样式

- font：最小12px，0px字体会消失。font:style weight size family;
  - 无衬线字体（sans-serif）
    - 特点：文字笔画粗细均匀，首尾无装饰
    - 场景：网页使用
    - 常见系列：黑体，Arial
  - 衬线字体（serif）
    - 特点：文字笔画粗细均匀，首尾有笔锋装饰
    - 场景：报刊书籍中应用广泛
    - 常见字体：宋体，Times New Roman
  - 等宽字体（monospace）
    - 特点：每个字母或者字体的宽度相同
    - 场景：程序代码编写，有利于代码的阅读和编写
    - 常见字体：Consolas，fira code
- backgruond：color image repeat position

### 盒模型

- 内容区域（content）、内边距区域（padding）、边框区域（border）、外边距区域（margin）
- 标准盒模型：width(content)+border+padding+margin
- 怪异盒模型：width(content+border+padding)+margin
- 坍塌现象：
  - 场景：互相嵌套的块级元素，子元素的margin-top会作用在父元素上
  - 结果：导致父元素一起往下移动
  - 解决方法：
    - 给父级元素设置border-top或者padding-top（分隔父子元素的margin-top）
    - 给父级元素设置overflow:hidden
    - 转换成行内块元素
    - 设置浮动

### 结构伪类选择器

|选择器|说明|
| ---------------------| ----------------------------------------|
|E:first-child{}|匹配父元素中第一个子元素，并且是E元素|
|E:last-child{}|匹配父元素中最后一个子元素，并且是E元素|
|E:nth-child(n){}|匹配父元素中第n个子元素，并且是E元素|
|E:nth-last-child(n){}|匹配父元素中倒数第n个子元素，并且是E元素|

##### n：0、1、2、3...

|功能|公式|
| ----------------| ---------------|
|找到从第五个往后|n+5|
|奇数|2n+1、2n-1、odd|
|偶数|2n、even|
|找到前5个|-n+5|

### 浮动

- 清除浮动的方式：
  - 给父级元素加高度
  - 父元素后面添加一个块元素，设置clear:both;
  - 添加伪元素设置display:block;和设置clear:both;
  - 添加双伪元素设置display:table;和设置clear:both;
  - 父元素设置overflow:hidden;

### BFC

- 块格式化上下文（Block Formatting Context,BFC）是web页面的可视CSS渲染的一部分，是块盒子布局过程发生的区域，也是浮动元素与其他元素交互的区域

#### 创建BFC

- float的值不是none
- position的值不是absolute、fixed
- display的值是inline-block、flow-root、table-cell、table-caption、flex或者inline-flex、grid或者inline-gril
- overflow的值不是visible

##### 功能一 用BFC包住浮动元素

- 块级元素浮动之后，宽度会变为内容的宽度
- 子元素浮动，脱离了普通文档流，父元素包不住子元素

##### 功能二 和浮动元素产生边界

### 定位

|定位方式|属性值|作用|
| --------| --------| ------------------------------------------------------------------------------|
|静态定位|static|默认值|
|相对定位|relative|相对于自己之前的位置定位|
|绝对定位|absolute|1.相对于最近的有定位的祖先元素进行定位<br />2.祖先元素没有定位则相对于浏览器进行定位|
|固定定位|fixed|相对于浏览器进行定位|

### 光标 cursor

|属性值|效果|
| -------| ----------------------|
|default|默认值，一般是箭头|
|pointer|小手，提示可以点击|
|text|工字型，提示选择文字|
|move|十字光标，提示可以移动|

### 元素隐藏

- visibility:hidden 隐藏元素本身，还在文档流中占位
- display:none 隐藏元素本身，不占位置、

### 精灵图（雪碧图）

- 场景：项目中将多张图片合并成一张大图片
- 优点：减少服务器发生次数，减轻服务器压力

### 文字阴影（text-shadow）

- text-shadow:h-shadow v-shadow blur color;

|参数|作用|
| --------| --------------------------|
|h-shadow|必须，水平偏移量。允许负值|
|v-shadow|必须，水平偏移量。允许负值|
|blur|可选，模糊度|
|color|可选，阴影颜色|

### 盒子阴影（box-shadow）

- box-shadow:h-shadow v-shadow blur spread color inset;

|参数|作用|
| --------| --------------------------|
|h-shadow|必须，水平偏移量。允许负值|
|v-shadow|必须，水平偏移量。允许负值|
|blur|可选，模糊度|
|spread|可选，阴影扩大|
|color|可选，阴影颜色|
|inset|可选，将阴影改为内部阴影|

### 过渡（transition）

- transition:all/属性值 时间曲线 时间；

##### 时间曲线

|值|描述|
| ---------------------| ---------------------------------------------------------------------------------|
|linear|规定以相同速度开始至结束过渡（相当于cubic-bezier(0,0,1,1)）|
|ease|规定慢速开始，然后变快，最后慢速结束过渡（相当于cubic-bezier(0.25,0.1,0.25,0.1)）|
|ease-in|规定以慢速开始过渡（相当于cubic-bezier(0.42,0,1,1)）|
|ease-out|规定以慢速结束过渡（相当于cubic-bezier(0,0,0.58,1)）|
|ease-in-out|规定以慢速开始和结束的过渡（cubic-bezier(0.42,0.0.58,1)）|
|cubic-bezier(n,n,n,n)|值为0-1|

### transform

- 优势：transform可以触发机器GPU加速。
- 类别
  - `translate`(水平移动距离，垂直移动距离);
  - `translate3d`(x,y,z);3D旋转
    - 父级元素添加`perspective`:值；（800-1000）
  - `rotate`(角度deg);旋转，默认圆点是盒子中心点
  - `scale`(X轴缩放倍数,Y轴缩放倍数);一般只会设置一个值，小于1缩小，大于1放大。
    - scale不会改变元素占用的大小
    - 突破浏览器字体大小限制
  - `transform-origin`:圆点水平位置 圆点垂直位置；（left、top、right、bottom、center）

### 渐变(方向,color-stop1,color-stop2,...)

- **线性渐变：** background:linear-gradient();

```CSS
background: linear-gradient(
	to left,
	lime 20%,
	red 30%,
	red 45%,
	cyan 55%,
	cyan 70%,
	yellow 80%
);
```

- **径向渐变：** background: radial-gradient()
  - **径向渐变的大小：**​`closest-corner`、`closest-side`、`farthest-corner` 和 `farthest-side`，其中 `farthest-corner` 是默认值

```CSS
background: radial-gradient(
    ellipse closest-side,
    red,
    yellow 10%,
    #1e90ff 50%,
    beige
);
```

- **锥形渐变：** background:conic-gradient()
  - 关键字“at”来设置锥形渐变的中心位置

```CSS
background: conic-gradient(at 0% 30%, red 10%, yellow 30%, #1e90ff 50%);
```

- **重复渐变：**  background: repeating-linear-gradient();

### 动画

- 定义动画

```CSS
@keyframes 动画名称{
	动画轨迹
}
```

- 使用动画

```CSS
animation:动画名称 动画时长 速率曲线 延迟时间 重复次数 动画方向 执行完毕时状态 是否运行或者停止;
animation: name duration timing-function delay iteration-count direction fill-mode play-state;
```

|值|说明|
| -------------------------| ----------------------------------------------------------------------------------------|
|animation-name|指定要绑定到选择器的关键帧的名称|
|animation-duration|动画指定需要多少秒或毫秒完成|
|animation-timing-function|设置动画将如何完成一个周期|
|animation-delay|设置动画在启动前的延迟间隔。|
|animation-iteration-count|定义动画的播放次数。|
|animation-direction|指定是否应该轮流反向播放动画。|
|animation-fill-mode|规定当动画不播放时（当动画完成时，或当动画有一个延迟未开始播放时），要应用到元素的样式。|
|animation-play-state|指定动画是否正在运行或已暂停。|

##### 速率曲线

|值|说明|
| ----------------------| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|linear|动画从头到尾的速度是相同的。|
|ease|默认。动画以低速开始，然后加快，在结束前变慢。|
|ease-in|动画以低速开始。|
|ease-out|动画以低速结束。|
|ease-in-out|动画以低速开始和结束。|
|steps(int,start \ end)|指定了时间函数中的间隔数量（步长）。有两个参数，第一个参数指定函数的间隔数，该参数是一个正整数（大于 0）。 第二个参数是可选的，表示动画是从时间段的开头连续还是末尾连续。含义分别如下：- start：表示直接开始。end：默认值，表示戛然而止。|
|cubic-bezier(n,n,n,n)|在 cubic-bezier 函数中自己的值。可能的值是从 0 到 1 的数值。|

### flex布局（弹性布局）

- 容器（content）和项目（item）

##### 容器属性（content）

- `flex-direction` ：主轴方向（排列方向）
  - flex-direction:row | row-reverse | column | column-reverse;
- `flex-wrap` ：是否换行,换行的方向
  - flex-wrap:nowrap | wrap | wrap-reverse;
- `flex-flow`：flex-direction || flex-wrap;
- `justify-content`：主轴对齐方式
  - justify-content:flex-start | flex-end | center | space-between | space-around;

|值|描述|
| -------------| ----------------------------------------------------------|
|flex-start|默认值。从行首起始位置开始排列。|
|flex-end|从行尾位置开始排列。|
|center|居中排列。|
|space-between|均匀排列每个元素，首个元素放置于起点，末尾元素放置于终点。|
|space-evenly|均匀排列每个元素，每个元素之间的间隔相等。|
|space-around|均匀排列每个元素，每个元素周围分配相同的空间。|

- `align-items` ：定义项目在交叉轴上如何对齐(Y轴)
  - align-items:flex-start | flex-end | center | baseline | stretch;
  - `baseline`：项目(item)的第一行文字的基线对齐
- `align-content`：多跟轴线的对齐方式（多行)，只有一跟轴线不生效
  - align-content:flex-start | flex-end | center | space-between | space-arou;

##### 项目属性（item）

- `order`：排序，值越小排序越靠前，默认为0
  - order:\<number>;
- `flex-grow`:项目占据的放大比例，默认为0。
  - flex-row:\<number>;
  - 计算：将所有不为0的值相加得到n，剩余的空间除以n，得到一份比例的基准值。用基准值乘以各个不为0的值就是多出要占据的空间m。m+项目初始值=元素占据的空间。
- `flex-shrink`：项目的缩小比例，默认为1。如果空间不足，项目会缩小。
  - flex-shrink:\<number>
  - 计算：将所有值相加得到n，所有元素总宽度减去总空间得到n。n / m得到基准值，元素宽度 - 基准值 * 缩小比例 = 最终元素宽度。
- `flex-basis`：设置项目占据的空间基准值
  - flex-basis:\<length> | auto; 默认auto
- `flex`: none | auto | [\<flex-grow> \<flex-shrik>  \<flex-basis>];默认 0 1 auto
  - none(0 0 auto);
  - auto (1 1 auto)
- `align-self`:设置单个项目的对齐方式,默认auto(继承align-items)
  - align-self:auto | flex-start | flex-end | center | baseline | stretch;
