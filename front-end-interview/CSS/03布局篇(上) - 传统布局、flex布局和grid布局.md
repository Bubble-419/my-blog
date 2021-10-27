1. #### position有哪些值？

   - **static静态定位**

     `static`是`position`的**默认值**，**静态定位就相当于没有定位**。此时元素仍处于标准流中，且对于元素`top`、`bottom`、`left`、`right`、`z-index`的设置都不起作用。

   - **relative相对定位**

     相对定位指的是元素的移动**相对于元素自身原本的位置**。声明了相对定位的元素不会脱离标准流，仍然保留其原本的位置。

   - **absolute绝对定位**

     绝对定位指的是元素的移动**相对于声明了定位的最近一级父元素的位置**，如果该元素没有父元素或父元素没有声明定位，则此时元素的移动是相对于浏览器而言的。

     声明了绝对定位的元素会脱标，不再占有原本的位置。

   - **fixed固定定位**

     固定定位指的是元素的移动**相对于浏览器的可视窗口**，可以理解为特殊的绝对定位，同样会脱离标准流。

     固定定位的应用常见于把元素固定在侧边栏。

   - **sticky粘性定位**

     粘性定位指的是**元素在最初的浏览器可视窗口区域内，其`top`、`bottom`、`left`、`right`的值都不起作用；而当元素到达设置的某个位置时，会转变为固定定位不再移动**。

   - **inherit继承定位**

     继承父元素定位，注意IE8之前的版本不兼容。

     

2. #### 为什么要清除浮动？

   清除浮动实际上是**清除浮动元素带来的影响**。

   浮动元素由于具有脱标特性，其脱离标准流后会影响到之后的块级元素的布局，且当父级元素没有设置高度时，会出现父级元素高度塌陷的情况。

   

3. #### 清除浮动有哪些方式，原理是什么？

   清除浮动有多种方式，但是其核心都围绕着两个原理的其中之一：** `clear`属性和创建BFC**。

   - **利用`clear`属性清除浮动**

     `clear`属性有三个值：`left`、`right`和`both`。根据CSS规范，`clear`属性指定**块级元素盒子**的哪一边不与其之前的浮动元素相邻，其中“之前的浮动元素”不包括盒子内部或其他BFC内的浮动元素。

     例如当元素声明`clear: left;`时，意味着**该元素盒子的左边不能与前面的浮动元素相邻，即其外边距必须在左浮动盒子的下方**，其他属性值同理。

     ![image-20210910143226935](C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210910143226935.png)

     因此可以**给浮动元素之后的块级元素声明`clear`属性，使之与浮动元素隔断，从而清除浮动元素对后面元素布局的影响**。

     常见的方法有：**额外标签法、伪元素清除法、直接清除法**。

     - **额外标签法**

       ```html
       <div class="last-float">
         我是最后一个浮动元素
       </div>
       <div class="clear">
           我是额外的div标签
       </div>
       
       <!-- CSS -->
       <style>
           .last-float {
               float: left;
           }
           
           .clear {
               clear: left;
           }
       </style>
       ```

       **优点**：简单好写；**缺点**：添加的标签无意义，不利于页面结构和后期维护。

     - **伪元素清除法**（最常用）

       **给浮动元素的父级元素添加伪元素，并利用`clear`属性清除浮动。**

       ```css
       /* 父元素声明clearfix类 */
       .clearfix:after {
           content:"";
           /* 伪元素默认为行内块，要使用clear属性需要声明为块级元素 */
           display: block;
           clear: both;
           height: 0;
           visibility: hidden;
       }
       
       .clearfix {
           /* 为了兼容IE6、7，需要触发haslayout */
           *zoom: 1;
       }
       ```

     - **直接清除法**：直接给浮动元素之后的块级元素清除浮动。

     

   - **创建BFC来清除浮动**

     BFC有隔绝内部元素和外部元素的特性，其内部布局不会受外部影响。因此可以利用浮动元素的父元素来创建BFC。

     通常会让父元素声明`overflow: hidden`来创建新的BFC，也可以让父元素声明浮动来创建，但是若父元素也浮动，同样会影响之后元素的布局。

     

4. #### 讲讲flex布局，以及常用的属性。

   > 回答思路：**flex布局概念、flex布局结构、flex容器的常见属性及作用、flex项目的常见属性及作用。**

   #### 一. flex布局概念

   flex布局是CSS3新增的一种布局方式，弹性布局能给盒子模型的布局提供很大的灵活性。flex是一种一维的布局方式。

   #### 二. flex布局结构

   一旦一个元素盒子声明为弹性盒子以后，其作为**flex容器**，内部存在一条**主轴**和一条**垂直于主轴的交叉轴**，主轴默认为水平线，则交叉轴默认为水平线的垂线。容器内部的所有子元素都默认成为**flex项目**，简称项目。

   #### 三.flex容器的常见属性及作用

   1. **结构属性：**

   - ** `flex-direction`**

     > flex-direction: row | row-reverse | column | column-reverse

     `flex-direction`属性可以**改变主轴的方向**，默认为`row`（水平从左向右）。

   - ** `flex-wrap`**

     > flex-wrap: nowrap | wrap | wrap-reverse

     `flex-wrap`属性**决定了容器是单线排列的还是多线排列的**（single-line or multi-line）。其默认值为`nowrap`，即默认情况下（假设`flex-direction`值为`row`），flex项目会顺着主轴排列成一行且不会换行，项目会自动缩小以适应容器在主轴上的宽度。如果声明flex容器为多线排列，则项目不会缩小，宽度不够就会自动换行排列，此时应该把每一行都看作一个新的flex容器。

     当`flex-wrap`的值为`wrap-reverse`时，可以**改变交叉轴的方向**。

   - ** `flex-flow`**

     > flex-flow: \<flex-direction>  || \<flex-wrap>

     `flex-flow`是`flex-direction`和`flex-wrap`的简写属性，决定了flex容器的总体结构。


   2. **布局属性**：

   - ** `justify-content`**

     > justify-content: flex-start | flex-end | center | space-between | space-around

     `justify-content`负责排列主轴上的flex项目，默认值是`flex-start`，项目在主轴上的排列是默认没有间距的。

   - ** `align-items`**

     > align-items: stretch | flex-start | flex-end | center | baseline

     `align-items`负责排列交叉轴上的项目，默认值是`strecth`，要明白`align-items`是**在交叉轴方向上移动项目**，比如`flex-start`是把项目顶端和交叉轴的起点对齐，`center`是把项目中心点和交叉轴中点对齐。

   - ** `align-content`**

     `align-content`属性负责排列multi-line的flex容器的多条轴线，因此对于single-line的flex容器不起作用。其默认值是`strecth`，意味着**flex容器对于项目在交叉轴上的布局是默认占满整个容器的**，也就是说**如果项目本身具有高度，flex容器会自动调整交叉轴上项目之间的距离**。

   #### 四.flex项目的常见属性及作用

   - ** `order`**

     `order`属性决定了flex项目的排列顺序，可以理解为项目的权重。每个项目默认的`order`值为0，`order`值越高则项目在容器中越靠后，越低项目在容器中越靠前。

   - ** `flex-grow`**

     `flex-grow`属性定义了项目在主轴方向上的增长系数，即**当主轴方向上存在可利用剩余空间时，项目的增长比例值**，其默认值为0，即使存在剩余空间也不会放大。

     可以理解为`flex-grow`定义的是项目对**剩余空间的分配比**，比如把`flex-gorw`值为1的元素增长的空间看作1时，值为2的元素增长的空间就是前者的两倍。

   - ** `flex-shrink`**

     和`flex-grow`对应的`flex-shrink`属性定义的是项目的收缩系数，每个项目的`flex-shrink`值都默认为1，意味着当空间不足时各个项目会等比例缩小，这也解释了为什么没有改变`flex-wrap`默认值的情况下，flex项目可以动态缩小。

     和`flex-grow`类似，当每个项目的`flex-shrink`值不相同时，它们的收缩值是成比例的。

   - ** `flex-basis`**

     flex项目占据的主轴空间叫做`main size`，交叉轴空间叫做`cross size`，浏览器会根据项目的`main size`判断是否有剩余可利用空间。`main size`和`cross size`的默认值是项目的宽度和高度，如果想对其进行修改，可以声明`flex-basis`属性，用于修改项目的`main size`。`flex-basis`的默认值为`auto`。

     设置`flex-basis`的值并不意味着该盒子的大小是固定的，只是盒子的`main size`发生了变化。如果盒子有声明`flex-grow`或`flex-shrink`的值，盒子大小一样会改变。

   - ** `flex`**

     > flex: \<flex-grow> \<flex-shrink> || \<flex-basis>

     `flex-grow`,`flex-shrink`和`flex-basis`的复合属性，后两个属性可选，默认值为`0 1 auto`。通常我们不会分开定义这三个属性，而是只定义`flex`属性。

   - ** `align-self`**

      `align-self`属性允许某个项目和其他项目在交叉轴上的排列方式不同，其可选值同`align-items`。默认值为`auto`，表示继承flex容器父元素的`align-items`值，声明后可覆盖`align-items`值。

     

5. #### 补充知识点：grid布局



