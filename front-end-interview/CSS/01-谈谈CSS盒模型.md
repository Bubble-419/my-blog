1. ### 谈谈CSS盒模型。

   CSS把所有html元素都看成一个盒子，因此有了**盒模型**这个说法。

   盒模型包括** `content`、`padding`、`border`、`margin` **四个部分，其中`margin`外边距部分不算作盒子宽度以内。

   ![image-20210906183922195](C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210906183922195.png)

   盒模型又根据`width`和`height`包含的部分不同而分为**W3C标准盒模型**和**IE盒模型**两种。

   有两个宽度和高度需要注意：

   1. **CSS定义的`width`属性和`height`属性**

      在标准盒模型中，`width`属性定义的是`content`的宽度，同理`height`属性定义的是`content`的高度。

      在IE盒模型中，`width`属性定义的是`content`的宽度+`padding`的宽度+`border`的宽度，`height`同理。

   2. **盒子的尺寸宽度和高度**

      **无论哪个盒模型，盒子尺寸宽度始终包括`content`的宽度+`padding`的宽度+`border`的宽度**。

      因此在标准盒模型中，`width`属性定义的只是盒子尺寸宽度的一部分，所以会出现`padding`值和`border`值变化，盒子尺寸宽度（盒子大小）也会变化的情况。

      而在IE盒模型中，`width`属性定义的就是盒子尺寸宽度，因此若改变`padding`值和`border`值，盒子尺寸也不会改变，只是内部的`content`尺寸会受影响。

   ![image-20210906203607775](C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210906203607775.png)

   **可以通过元素的`box-sizing`属性改变元素的盒模型，属性值`content-box`对应标准盒模型，属性值`border-box`对应IE盒模型**。

