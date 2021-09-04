## 对象

### `new`操作符

1. #### `new`操作符做了什么事？

   (1) 在内存中创建一个新对象

   (2) 将新对象的`__proto__`属性（实际上是`[[prototype]]`特性）指向构造函数`constructor`的`prototype`属性，即原型对象

   (3) 将构造函数的`this`指向新对象

   (4) 执行构造函数

   (5) 构造函数返回了一个非空对象 ? 该非空对象 : 新对象

   简单概括，`new`操作符会**调用构造函数，与原型对象链接，并返回一个对象**，达到**创建实例对象**的效果。

   

2. #### `new`操作符返回不同类型时有什么表现？

   从`new`操作符行为的第五步可以知道：

   当构造函数**没有显式定义返回值为某个对象**时，`new`操作符会**返回它第一步创建的那个新对象**；

   而一旦构造函数**有返回一个非空对象**，那么即使中间那几步仍然会正常进行，也不会返回我们想创建的新对象，而是**返回这个非空对象**。

   在调用类构造函数时，如果把类构造函数的返回对象修改成另一个非空对象，会导致类与实例对象通过`this`而产生的连接断掉，所以此时再对实例对象使用`instanceof`操作，也不会检测出和父类有关联。

   

### 原型

1. #### 谈谈你对原型的理解。

   - **原型其实就是原型对象**，原型对象中**包含了可以被对象实例共享的属性和方法**，其中最重要的属性就是`constructor`，其指向与原型对象关联的构造函数。

   - （原型有什么用？）当我们希望所有对象实例共享一些方法时，如果写在构造函数里，所有实例都要创建相同作用的函数，导致浪费内存；如果写在全局作用域里再由构造函数引用，又会导致污染全局作用域。这时候就可以考虑把需要共享的方法（或属性）写在原型对象里。

   - 原型对象的`constructor`属性指向与之关联的构造函数，而构造函数的`prototype`属性又会指向该原型对象，这二者是循环引用的关系。

   - 所有实例对象都可以通过`__proto__`指针找到原型对象，说明实例和原型之间是有显性关系的，而实例和构造函数之间却没有。所有对象的顶层原型对象都是`Object`（原型链中`Object`的原型是`null`)。

     

### 继承

1. #### JavaScript如何实现继承？

   - ##### **原型链继承**

     > **（1） 什么是原型链？**

     ​		实例对象的`__proto__`指针指向原型对象，那么如果实例对象的原型对象是另一个类的实例对象，就形成了链式关系。

     > **（2） 怎么利用原型链实现继承？**

     ```javascript
     // 父类构造函数
     function SuperType(superPro){
       this.superProperty = superPro;
     }
     
     // 父类原型对象上的方法
     SuperType.prototype.getSuperProperty = function() {
       return this.superProperty;
     }
     
     // 子类构造函数
     function SubType(subPro){
       this.subProperty = subPro;
     }
     
     // **把子类的原型对象重写为父类的一个实例对象，调用父类的构造函数，相当于把子类的原型对象修改为一个__proto__指针指向Supertype.prototype的对象
     SubType.prototype = new SuperType();
     
     // 子类原型对象上可以再定义自己的方法，也可以重写父类方法
     SubType.prototype.getSubProperty = function() {
       return this.subProperty;
     }
     ```

     > **（3） 需要注意的是：**

     ​	**子类实例对象的原型对象，实际上是父类的一个实例对象**。

     > **（4）原型链继承的缺点：**

     ​			对父类构造函数中定义的**引用值属性**的修改操作会造成多个子类实例对象属性值被污染（牵一发而动全身）；

     ​			子类在实例化时不能给父类的构造参数传参，因为子类实例化时无法访问到父类的构造函数。

     

   - ##### 经典继承（盗用构造函数）

     > **怎么实现？**

     **通过`call`或`apply`方法修改父类构造函数的执行上下文对象**，达到劫持构造函数的效果。

     ```javascript
     // 父类定义同上
     
     // 子类构造函数
     function SubType(subPro){
       // **利用call方法把当前对象作为上下文来执行父类构造函数，并传参
       SuperType.call(this, 'super');
       this.subProperty = subPro;
     }
     
     // 测试
     let instance = new SubType('sub');
     
     console.log('subPro:' + instance.getSubProperty()); // 输出sub
     console.log(`superProperty:${instance.subProperty}`); // 输出super，说明子类实例对象中存在superProperty
     // console.log('getSuperProperty:' + instance.getSuperProperty()); 报错，无法访问父类原型中定义的方法
     
     console.log(instance);
     // 输出结果显示该实例对象含有subProperty和superProperty两个属性，但是其原型对象是Object
     ```

     由此可以总结出盗用构造函数实现继承的特点。

     - **直接继承父类构造函数中定义的属性，并内化成自己的属性**，解决了修改引用值属性导致的属性污染问题。

     - 子类的构造函数中盗用了父类的构造函数，使得**子类实例化时可以访问父类构造函数**，也就可以给父类构造函数传参。

     - **没有对子类的原型对象进行修改操作，因此子类的原型还是`Object`，子类实例也就无法访问父类原型对象定义的方法了**。

       

   - ##### 组合继承（原型链+盗用构造函数）

     > **怎么实现？**

     结合使用**原型链继承来继承父类方法，盗用构造函数来继承父类属性**。

     ```javascript
     // 父类构造函数
     function SuperType(superPro){
       this.superProperty = superPro;
       this.colors = ['black','red'];
     }
     
     // 父类原型对象上的方法
     SuperType.prototype.getSuperProperty = function() {
       return this.superProperty;
     }
     
     // 子类构造函数
     function SubType(subPro){
         // **继承父类的属性
       SuperType.call(this, 'super');
       this.subProperty = subPro;
     }
     
     // **继承父类方法
     SubType.prototype = new SuperType();
     ```

     

   - ##### 原型式继承

     > **怎么实现？**

     两种方式如下：

     ```javascript
     let animal = {
       name:'pig',
       friends: ['dog','cat'],
       location: 'forest',
     }
     
     // **1. 创建一个临时构造函数，把作为传参的对象当作该临时构造函数的原型对象，返回这个临时构造函数的实例
     function object(obj){
       function f(){};
       f.prototype = obj;
       return new f();
     }
     let anotherAnimal = object(animal);
     
     // **2. ES5通过Object.create()对原型式继承进行了规范化，但他们的作用是一样的，即将传参对象作为新建对象的原型对象
     let anotherAnimal2 = Object.create(animal);
     // 可以定义或覆盖属性
     let anotherAnimal3 = Object.create(animal， {
                                        name: {
                                       	value: 'cat', 
                                        }
                                       });
     ```

     原型式继承适用于**不自定义类型（即不自定义构造函数），通过原型的方式来实现对象之间的信息共享**的情况。

     和原型链继承相同的是，原型式继承把一个实例对象作为另一个对象的原型对象，可以继承该对象的属性和方法，

     但是同样会有“牵一发而动全身”的情况，即原型对象中的引用值属性一旦被修改，就会污染所有实例对象的属性值。

   

   - ##### 寄生式继承

     > **怎么实现？**

     **在原型式继承的基础上，对创建的新对象进行增强后返回**。

     ```javascript
     function createAnimal(origin) {
       let clone = Object.create(origin);
       // 增强对象
       clone.eatable = true;
       return clone;
     }
     ```

     寄生式继承基于原型式继承的方式、构造函数创建对象的思路，因此它**同样适用于不关注类型和构造函数、而更关注对象的信息共享的情况**。

     它的缺点也很明显，和构造函数创建对象一样会导致对象里的函数难以重用，和原型式继承一样会污染引用值属性。

     

   - ##### 寄生式组合继承

     > **为什么要使用寄生式组合继承？**

     **为了减少调用父类构造函数产生的开销**。

     这个开销有两方面：第一，用组合继承来实现继承时，**会调用两次父类的构造方法**；第二，调用父类构造函数来创建实例对象作为子类原型对象时，**这个原型对象的属性实际上是不必要的**，因为我们已经用盗用构造函数来继承了父类的属性了。

     > **怎么实现？**

     寄生式组合继承的**核心**就在于**用寄生式继承的方式来继承父类的方法**，而不是用创建父类实例对象的方式，这样就只会在继承属性时调用一次父类的构造函数，也不会在原型对象上产生多余的属性，同时还保留了原型链。

     ```js
     // **继承父类方法
     
     // 组合式继承：调用父类构造函数，创建一个实例对象来当作子类的原型对象
     SubType.prototype = new SuperType();
     
     // 寄生式继承：
     let prototype = Object.create(SuperType.prototype);
     prototype.constructor = SubType();
     SubType.prototype = prototype;
     ```

     

   - **ES6：`extends`关键字**

     > **使用`extends`时，父类可以是什么？**

     `extends`不仅可以继承一个类，还能继承一个单独的构造函数。这是因为`extends`本**质上可以继承任何含有构造函数（`[[construct]]`）和原型的对象**。

     > ** `extends`关键字继承了父类的什么？**

     同样是继承父类的属性和方法。

     但是要明白，**ES6中类的定义能很有效地分割原型对象和实例对象**。这是因为类块中定义的方法都会被当作原型对象上的方法，而类中用`this`关键字声明的属性又都属于不同的实例对象。

     > **使用ES6的`extends`关键字和使用ES5的其他方法来实现继承有什么不同？**

     （1）首先要明白，ES6的`extends`其实是一个语法糖，其背后依然是原型链。

     （2）ES5实现继承虽然方法各异，但是都是直接处理构造函数和`prototype`对象；而`extends`可以避免我们直接操作这二者。

     （3）使用`extends`继承父类后，子类可以通过`super`关键字来访问父类的构造函数和静态方法。当子类没有显式定义自己的构造函数时，会默认继承父类的构造函数；若显式定义了构造函数，则必须引用`super()`或返回一个对象。