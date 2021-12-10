## 函数

### `this`

1. #### `this`的指向规则

   - **箭头函数**

     箭头函数的`this`指向的是**声明函数时的上下文**

   - **默认规则**

     普通函数的`this`指向把函数当成方法来调用的**执行上下文**。

     但是要注意，** `this`是函数的内部对象**，不能再向内传递，因此当存在闭包时，闭包的携带作用域里是不会存在上级作用域的`this`对象的。

     ```js
     var name = 'window';
     let a = {
       name: 'Jhon',
       getName: function(){
         console.log(this.name); // Jhon
         return function(){
           console.log(this.name); // window
         }
       }
     }
     a.getName()();
     ```

   - **bind**

     利用`bind`修改函数的`this`指向，要注意调用`bind()`返回的是一个函数。

     ```js
     function b(){
       console.log(this);
     }
     // 第一种方式
     var c = b.bind(a);
     c();
     // 第二种方式
     b.bind(a)();
     ```

   - **apply/call**

     利用`apply`或`call`也可以修改函数`this`的指向，修改后会直接调用，但是不会影响到调用原函数时的`this`对象。

     ```js
     function b(){
       console.log(this);
     }
     b.apply(2); // 2
     b(); // window
     ```

   - **new**

     箭头函数之外的函数都可以作为`new`关键字的构造函数，此时函数中的`this`一定是创建的新对象。

   **注意**：

   1. 当应用多条绑定规则时，优先级是**：`new` > `bind` > `apply/call` > 默认规则**
   2. 注意函数名只是指向函数的指针
   3. 一定要留意函数的执行上下文对`this`指向的影响
   4. 注意全局对象上的方法（比如`setTimeout`）的回调方法的`this`指向的是全局对象
   5. 如果把`null`或`undefined`或空传入 `bind` 或 `apply/call` 方法中，相当于把`this`指向全局对象



### 闭包

1. #### 谈谈对闭包的理解。

   > **什么是闭包？**

   **闭包是指能够访问外部函数作用域，引用了外部变量的函数。**

   既然引用了外部函数的变量，那么当这个外部函数执行完毕后，其执行上下文会被销毁，但是活动对象（变量对象）中**被引用的部分变量会被保留下来，并被打包成`closure`闭包对象**，存储在函数的`[[scope]]`属性里。因此对于函数来说，`[[scope]]`属性相当于创造了一个执行该函数所需要的外部环境，里面保存了函数运行所需的变量。

   要注意的是，**外部作用域中不需要的变量不会被打包成闭包对象**，而是会随着外部函数执行上下文的销毁而被释放。

   ```js
   function func1() {
     const a = 'a';
       // func1中的变量d没有被func3使用，就不会存在于closure对象里
     let d = 'd';
     console.log(d);
   
     function func2() {
       const b = 'b';
       return function func3() {
         console.log(a + b);
       }
     };
     return func2();
   }
   console.dir(func1());
   ```

   ![image-20211210170353803](C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20211210170353803.png)

   > **闭包有什么作用？**

   **回调函数**是最常见的闭包，闭包还**可以用来保护函数的私有变量（定义在函数或块中的变量）**；**用来实现模块化**等。

   > **闭包的缺陷是什么？**

   因为闭包保存了对外部函数的变量对象的引用，所以只有闭包调用结束后，`[[scope]]`里保存的外部作用域的变量对象才会被释放。

   如果有一个很大的对象，本来外部函数调用完毕后就能销毁，却因为被闭包引用而保存在了堆里，且在闭包函数被调用之前都得不到释放，就会一直占用堆内存，严重可能导致内存泄漏问题。