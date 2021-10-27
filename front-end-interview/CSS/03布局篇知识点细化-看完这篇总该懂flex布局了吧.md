> 写在前面
>
> 掌握flex布局的思路就在于掌握这四个部分：
>
> 一. flex布局的概念，知道flex布局大概是用来干嘛的
>
> 二. flex布局的结构，当元素成为弹性盒子后会发生什么？
>
> 三. **针对flex容器**的常见属性及其作用，包括结构属性和布局属性
>
> 四. **针对flex项目**的常见属性及其作用

### 一. flex布局概念

flex布局是CSS3新增的一种布局方式，弹性布局能给盒子模型的布局提供很大的灵活性。把一个元素盒子的`display`值声明为`flex`后，该盒子就是弹性盒子，作为flex容器，且盒子内的子元素都会成为flex项目。

flex是一种一维的布局方式，即一次只能移动一行或一列的元素，可以理解为国际象棋的王后。

### 二. flex布局结构

一旦一个元素盒子声明为弹性盒子以后，其作为**flex容器**，内部存在一条**主轴**和一条**垂直于主轴的交叉轴**，主轴默认为水平线，则交叉轴默认为水平线的垂线。容器内部的所有子元素都默认成为**flex项目**，简称项目。

<img src="C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210910224904195.png" alt="image-20210910224904195" style="zoom:80%;" />

### 三.flex容器的常见属性及作用

1. #### **结构属性：**

- ** `flex-direction`**

  > flex-direction: row | row-reverse | column | column-reverse

  `flex-direction`属性可以**改变主轴的方向**，默认为`row`（水平从左向右）。

- ** `flex-wrap`**

  > flex-wrap: nowrap | wrap | wrap-reverse

  `flex-wrap`属性**决定了容器是单线排列的还是多线排列的**（single-line or multi-line）。其默认值为`nowrap`，即默认情况下（假设`flex-direction`值为`row`），flex项目会顺着主轴排列成一行且不会换行，项目会自动缩小以适应容器在主轴上的宽度。如果声明flex容器为多线排列，则项目不会缩小，宽度不够就会自动换行排列，此时应该把每一行都看作一个新的flex容器。

  注意当`flex-wrap`的值为`wrap-reverse`时，可以**改变交叉轴的方向**。

  ![image-20210911132511328](C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210911132511328.png)

- ** `flex-flow`**

  > flex-flow: <flex-direction>  || <flex-wrap>

  `flex-flow`是`flex-direction`和`flex-wrap`的简写属性，决定了flex容器的总体结构。

2. #### **布局属性**：

- ** `justify-content`**

  > justify-content: flex-start | flex-end | center | space-between | space-around

  `justify-content`负责排列主轴上的flex项目，默认值是`flex-start`，项目在主轴上的排列是默认没有间距的。

  <img src="C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210911134300065.png" alt="image-20210911134300065" style="zoom: 67%;" />

- ** `align-items`**

  > align-items: stretch | flex-start | flex-end | center | baseline

  `align-items`负责排列交叉轴上的项目，默认值是`strecth`。

  可以和下面的`align-content`属性做对比，把`align-items`属性理解为其排列的交叉轴项目是存在于single-line的flex容器中的（如果容器是multi-line的，应把每一行看作一个flex容器）。

  正是因为`align-items`对应着`single-line`的flex容器，所以它看上去有点难理解，容易让人产生还是在排列一行项目的既视感。但是要明白`align-items`是**在交叉轴方向上移动项目**，比如`flex-start`是把项目顶端和交叉轴的起点对齐，`center`是把项目中心点和交叉轴中点对齐。

  <img src="C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210911140457959.png" alt="image-20210911140457959" style="zoom:67%;" />

- ** `align-content`**

  `align-content`属性负责排列multi-line的flex容器的多条轴线，因此对于single-line的flex容器不起作用。其默认值是`strecth`，意味着**flex容器对于项目在交叉轴上的布局是默认占满整个容器的**，也就是说**如果项目本身具有高度，flex容器会自动调整交叉轴上项目之间的距离**。

  <img src="C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210911151721320.png" alt="image-20210911151721320" style="zoom:67%;" />

  

### 四.flex项目的常见属性及作用

- ** `order`**

  `order`属性决定了flex项目的排列顺序，可以理解为项目的权重。每个项目默认的`order`值为0，`order`值越高则项目在容器中越靠后，越低项目在容器中越靠前。

- ** `flex-grow`**

  `flex-grow`属性定义了项目在主轴方向上的增长系数，即**当主轴方向上存在可利用剩余空间时，项目的增长比例值**，其默认值为0，即使存在剩余空间也不会放大。

  ![image-20210911165026868](C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210911165026868.png)

  当多个项目都定了`flex-grow`值时，才能理解到`flex-grow`定义的**比例值**的概念：

  ![image-20210912104817485](C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210912104817485.png)

  可以理解为`flex-grow`定义的是项目对**剩余空间的分配比**，3号盒子分配到的剩余空间是2号盒子的两倍。

- ** `flex-shrink`**

  和`flex-grow`对应的`flex-shrink`属性定义的是项目的收缩系数，每个项目的`flex-shrink`值都默认为1，意味着当空间不足时各个项目会等比例缩小，这也解释了为什么没有改变`flex-wrap`默认值的情况下，flex项目可以动态缩小。

  和`flex-grow`类似，当每个项目的`flex-shrink`值不相同时，它们的收缩值是成比例的。

  ![image-20210912144206726](C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210912144206726.png)

- ** `flex-basis`**

  flex项目占据的主轴空间叫做`main size`，交叉轴空间叫做`cross size`，浏览器会根据项目的`main size`判断是否有剩余可利用空间。`main size`和`cross size`的默认值是项目的宽度和高度，如果想对其进行修改，可以声明`flex-basis`属性，用于修改项目的`main size`。`flex-basis`的默认值为`auto`。

  设置`flex-basis`的值并不意味着该盒子的大小是固定的，只是盒子的`main size`发生了变化。如果盒子有声明`flex-grow`或`flex-shrink`的值，盒子大小一样会改变。

- ** `flex`**

  > flex: <flex-grow> <flex-shrink> || <flex-basis>

  `flex-grow`,`flex-shrink`和`flex-basis`的复合属性，后两个属性可选，默认值为`0 1 auto`。通常我们不会分开定义这三个属性，而是只定义`flex`属性。

- ** `align-self`**

   `align-self`属性允许某个项目和其他项目在交叉轴上的排列方式不同，其可选值同`align-items`。默认值为`auto`，表示继承flex容器父元素的`align-items`值，声明后可覆盖`align-items`值。