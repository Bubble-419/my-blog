## 数据类型

1. #### **JavaScript有哪些数据类型？**

   7种基本数据类型：`Undefined`, `Null`, `Boolean`, `String`, `Number`, `Bigint`, `Symbol`；

   1种复杂数据类型：`Object`.

   注意`Bigint`是新增的数据类型，可以以任意精度表示整数，可以安全地存储和操作大整数；`Number`则是基于IEEE754标准的双精度64位二进制格式值。

   

2. #### **原始值和引用值是什么？有什么区别？**

   变量有按值访问和按引用访问两种方式。

   当声明**基本数据类型**的变量并赋值时，变量里存储的值都是**原始值**，即我们**访问的是变量的实际值**。

   但是声明**对象**并对对象进行操作时，我们**访问的是对象的引用值**。因为JavaScript不允许访问对象的内存位置，所以我们只能访问对象的引用值而不是对象本身。

   **区别**：

   - **保存位置不同**：原始值保存在全局作用域的栈内存中，引用值实际上是一个指针，指向存储在堆内存的对象中。

   - **传递方式不同**：当进行变量复制的操作时，原始值会被复制一份副本赋给新变量，而引用值则是复制一个指向同一对象的指针再赋给新变量，内存中的对象不变。

     > 函数的参数传递规则等同于变量复制，即**参数传递是值传递的方式**。
     >
     > 因此对于引用值的值传递，就是传递一份复制的指针；而引用传递是直接传递原指针。

   

3. #### `undefined`和`null`的区别是什么？

   - **意义不同**：`undefined`是未初始化变量，`null`是对象空指针。

     当变量被声明却没有初始化时，会默认其值为`undefined`，而无需显式声明。

     但是当对象被声明，而当下还没有合适的变量值时，我们会选择给对象赋值为`null`。

   - **类型不同**：对未声明的变量和未初始化变量调用`typeof`时，得到的结果都是`undefined`，

     但是对空指针对象调用`typeof`时，得到的结果是`object`.

     

4. #### **不同类型与布尔值之间的转换规则是什么？**

   要弄清楚除了布尔型的`false`以外，其他数据类型还有哪些值是`falsy`的（假值）：

   | 数据类型  | falsy值        |
   | --------- | -------------- |
   | String    | “”（空字符串） |
   | Number    | **0**、NaN     |
   | Undefined | undefined      |
   | Object    | null           |

   > 控制流语句会自动进行其他数据类型和布尔型之间的转换。

   

5. #### **不同类型与数值类型之间的转换规则是什么？**

   首先要知道，不同类型转换为数值类型有三种方式**：`Number()`转型函数、`parseInt()`、`parseFloat()`.**

   **Number()可以处理任何数据类型**：

   | 数据类型  | 数值                                                         |
   | --------- | ------------------------------------------------------------ |
   | Null      | 0                                                            |
   | Undefined | NaN                                                          |
   | Boolean   | true --> 1, false --> 0                                      |
   | String    | ①空字符串 --> 0；②纯数值字符（有效整数、浮点数、十六进制数） --> 十进制数值； ③其他字符串 --> NaN |
   | Object    | 先调用`valueOf()`，不是NaN --> 直接返回；是NaN --> 调用`toString()`，根据String的转换规则处理 |

   **后两个方法是针对`String`类型的**：

   从第一个非空格字符开始，依次检测每一个字符，直到遇见字符串结尾或者非数值字符。

   如“blue1234”会返回1234.

   二者区别：

   - `parse`方法遇到空字符串会返回NaN，而`Number()`会返回0；

   - `parse`方法可以解析十六进制。

     

6. #### **什么是`NaN`？如何判断？**

   `NaN`，即“Not a Number”，不是数字，但**是一个特殊的数值**。

   **原本要返回一个数值但是操作失败**时，返回值会是`NaN`。

   当我们需要判断某个值是否是`NaN`时，可以**使用`isNaN()`方法**：

   首先我们需要知道，`NaN`是一个数值，因此要判断某个值是否是`NaN`时，**第一步是进行数据类型的转换，第二步才是判断是否为`NaN`。**

   如果一个值可以转换为数值，则`isNaN()`会返回false，反之则true。



7. #### 基本数据类型中的`Symbol`有什么用？

   `Symbol`**值是唯一、不可变的值。**

   用处：

   - **可以作为对象独一无二的属性名**

     对象的属性名可以是字符串或者`Symbol`，凡是`Symbol`的属性名，都不会与其他属性产生冲突。

   - **可以用于定义对象非私有但仅用于内部的方法**

     本质上还是定义对象的唯一属性名，但是因为`Symbol`值作为对象的属性名时不会被常规方法遍历得到，所以可以用于定义仅用于内部的方法。

   注意点：

   - `Symbol`值没有包装类，直接使用`Symbol()`声明
   - `Symbol()`的参数仅用于对`Symbol`值的描述，不会影响`Symbol`值的唯一性
   - 如果需要复用`Symbol`值，需要在全局符号注册表中用`Symbol.for()`注册



8. #### 隐式的类型转换有哪些情况？

   - **四则运算符中的隐式转换**：

     - 加法操作中如果有一个操作数是字符串，就会把另一个操作数的类型转换为字符串
     - 其他操作中只要有一个操作数是数值，就会把另一个操作数转换为数值

   - **等于`(==)`和不等于`(!=)`操作符中的隐式转换**：

     等于和不等于操作符会在比较前进行类型转换（全等和不全等操作符不会强转类型）。

     - 有布尔，转数值

     - 字符和数值，转数值

     - `null`和 `undefined`，不能转，但是`null == undefined`

     - `NaN`不用转，和谁比都是不相等

     - 有一个是对象，调用`valueOf()`比较原始值

       两个都是对象，比较是否是同一个对象



## 数据类型判断

1. #### 类型判断有哪几种方式？

   - `typeof`操作符

     `typeof`通常用于**基本数据类型的判断**，因为原始值使用`typeof`来判断类型是很方便的，但是引用值使用`typeof`时，只会得到`function`或`object`的结果，并不能很直接的判断出引用值的类型。

     `typeof`操作符有几个注意点：

     （1） **不能判断`null`类型**。如果对`null`使用`typeof`，结果会是`object`，这是因为null的意义就是空指针对象。

     （2） `typeof`**操作符是未声明变量的唯一有效操作**，返回结果是`undefined`。

   - `instanceof`操作符

     `instanceof`通常用于**判断原型和实例之间的关系**，即通过原型链的方式来判断对象的类型。

     `instanceof`的原理是检测构造函数的`prototype`属性是否出现在实例对象的原型链上，具体原理见第二道题。

   - `Object.prototype.toString.call()`

     **能判断的方式最完整**。

     ```javascript
     console.log(Object.prototype.toString.call([])); // [object Array]
     ```

     但是要注意，这个方式没办法判断出实例对象和原型的关系。

     ```js
     /*
     父类SuperType 子类SubType
     let instance = new SubType();
     */
     
     console.log(Object.prototype.toString.call(instance)); // [object Object] 打印结果与父类无关
     console.log(instance instanceof SuperType); // true
     ```

   - ** `isXXX` 的API**

     **判断特定类型的API**，比如`isArray()`、`isNaN()`等。

     

2. #### `instanceof`操作符的原理是什么？

   首先要知道`instanceof`的语法是：`object instanceof constructor`，即 `instanceof`检测的是**实例对象和构造函数**二者的关系，再进一步讲，它**检测构造函数的`prototype`属性是否出现在实例对象的原型链上**。

   那么 `instanceof`是怎么知道`constructor`的原型对象有没有在`object`的原型链上出现过的呢？

   ```js
   // 模拟instanceof的查找过程
   function instance_of(obj, constructor){
     // 实例对象的原型链
     let obj_proto = obj.__proto__;
     // 构造函数的原型对象    
     let prototype = constructor.prototype;
       
     // 循环遍历obj的原型链
     while(obj_proto !== null){
       // 说明constructor的原型对象有出现在obj的原型链上，返回true
       if(obj_proto === prototype) return true;
       obj_proto = obj_proto.__proto__;
     }
       
     // obj_proto为null时仍未返回true，则说明找不到
     return false;
   }
   ```

   