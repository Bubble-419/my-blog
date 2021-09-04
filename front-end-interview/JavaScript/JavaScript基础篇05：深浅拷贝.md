## 深浅拷贝

1. #### 什么是深拷贝和浅拷贝？浅拷贝和赋值有什么区别？

   **浅拷贝**：创建一个新对象，其**拷贝了原对象的第一层引用**。即当原对象的属性值是**基本数据类型**时，**直接拷贝其值**；当原对象的属性值是**对象**时，将**拷贝指向该对象的指针**作为属性值。

   因此，拷贝后对象若改变类型为引用值的属性值，会影响到原对象。

   浅拷贝和赋值的区别在于，对对象的赋值并不会创建一个新对象，其规则等同变量复制。

   <img src="C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210819094005471.png" alt="image-20210819094005471" style="zoom: 67%;" />

   **深拷贝**：**创建一个新对象，将原对象从内存中完整地拷贝一份出来。拷贝后对象不会对原对象造成影响。**

   <img src="C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210819094557482.png" alt="image-20210819094557482" style="zoom:67%;" />

   

2. #### 怎么实现浅拷贝？

   - **Object.assign()**

     `Object.assign()`可以把任意多个源对象的可枚举属性值拷贝给目标对象，并返回拷贝后的目标对象。

     其原理相当于以下代码：

     ```js
     function shallowCopy(target, source) {
       for (let key in source) {
         target[key] = source[key];
       }
       return target;
     }
     ```

   - **扩展运算符**

     利用扩展运算符实现浅拷贝时，可以取出参数对象的可枚举属性值拷贝给目标对象。

     ```js
     let source = {};
     let target = {...source};
     ```

   - **数组方法：Array.slice()和Array.concat()**

     当调用`slice()`和`concat()`而不传参时，相当于对数组进行浅拷贝。

     

3. #### 怎么实现深拷贝？

   - **JSON.parse(JSON.stringify(obj))**

     可以用JSON方法实现深拷贝，但是JSON并不支持JavaScript的所有类型，也无法解决循环引用问题。

   - 自己实现深拷贝方法

     ```js
     function deepCopy(source, map = new WeakMap()) {
       // 1. 对源对象进行类型判断，从而确定返回的拷贝后对象的类型
       if (source instanceof Object) {
         // 2. 解决循环引用导致的爆栈问题，如果map中已有该属性对象，则无需再遍历
         if (map.has(source)) return map.get(source);
         else if (source instanceof Array) {
           target = [];
         } else if (source instanceof Function) {
           return function () {
             return source.apply(this, arguments)
           };
         } else if (source instanceof RegExp) {
           // 拼接正则
           return new RegExp(source.source, source.flags);
         } else if (source instanceof Date) {
           return new Date(source);
         } else {
           target = {}
           // 用map保存遍历过的属性对象
           map.set(source, target);
           for (let key in source) {
             target[key] = deepCopy(source[key], map);
           }
         }
         return target;
       } else return source;
     }
     ```

     