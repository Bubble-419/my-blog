# JavaScript面试基本功知识点总结-中级篇

# Map和Set

1. #### 谈谈Map和Set。

   > **什么是Map？**

   Map是ES6为了规范键值对的存储机制而定义的一种集合类型，与Object只能用字符串、数字、Symbol值当作键（属性名）的存储形式不同，**Map的键可以是任何数据类型**。

   **对Map的实例进行初始化时，传参可以是任何一个具有Iterator接口，且每个成员都具有双元素的数据结构**。Map的**基本API**包括用`set()`进行新增键值对，用`get()`和`has()`进行查询操作，用`delete()`和`clear()`进行删除操作。

   Map用ES内部的SameValueZero比较操作来判断键是否匹配，这个**比较操作类似严格相等运算符，但是Map把`NaN`视作相等**。

   ```js
   const a = 0/""; // NaN
   const b = 0/""; // NaN
   
   const m = new Map([
       [a, 'a'], [b, 'b']
   ]);
   
   console.log(m.size); // 1
   // 因为a和b会被判断为是同一个键，所以值会被覆盖为'b'
   ```

   **Map会维护键值对的插入顺序，并且在迭代时遵循这个顺序**。Map的迭代器可以通过`entries()`方法或`Symbol.iterator`属性获得，还可以通过`keys()`和`values()`方法获得键和值的迭代器。

   在迭代过程中，**如果键值对中的键或值是基本数据类型值，那么对键或值的修改不会影响到Map中该键值对的值**；而**如果键或值是对象，Map无法修改键或值对对象的引用，但是对对象内部的修改可以反映到Map上**。

   ```js
   // 1. 当键或值是基本数据类型值时
   const m1 = new Map([
       ['a', 'abc']
   ]);
   
   // values同理
   for(key of m1.keys()){
       if(key === 'a') key = 'b';
       // 可以任意修改key值
       console.log(key); // b
       // 但是这个修改不会反映在Map上
       console.log(m1.get(key)) // undefined
       console.log(m1.get('b')) // undefined
       console.log(m1.get('a')) // 'abc'
   }
   
   // 2. 当键或值是对象时
   const objKey = {
     id: 1,
     myName: 'Jhon'
   };
   const objKey2 = {
     id: 2,
     myName: 'Tom'
   };
   const objValue = {
     id: 3,
     myName: 'Mary'
   };
   
   const m2 = new Map(
     [
       [objKey, 'cde'], ['Mary', objValue]
     ]
   );
   
   for(key of m2.keys()){
     // 直接修改键的引用时，不生效
     if(key === objKey) key = objKey2;
     console.log(m2.get(objKey2)); // undefined
   }
   
   for(val of m2.values()){
     // 修改对象的内部属性，可以反映回Map
     if(val === objValue) val.id = 4;
     console.log(m2.get('Mary').id); // 4
   }
   ```

   > **什么是Set？**

   Set是ES6新增的一种集合类型数据结构，Set的键和值是相等的，所以可以看作只有值，且**值可以是任何数据类型**。

   **对Set的实例进行初始化时，传参可以是任何一个具有Iterator接口的数据结构**。Set的**基本API**包括用`add()`进行新增值，用`get()`和`has()`进行查询操作，用`delete()`和`clear()`进行删除操作。

   Set的值具有去重性，即**Set的成员不会存在重复**，**Set用于检查值的匹配性的方法和Map一致**，Set的迭代器也和Map类似。

   由于Set的值的去重性，再利用扩展运算符把Set转换为数组，很容易利用Set实现并集、交集和差集。

   ```js
   const s1 = new Set([1,2,3]);
   const s2 = new Set([2,4,6]);
   
   // s1 ∪ s2
   const union = new Set([...s1, ...s2]);
   console.log(union); // Set(5) {1, 2, 3, 4, 6}
   
   // s1 ∩ s2
   const intersect = new Set([...s1].filter(x => s2.has(x)));
   console.log(intersect); // Set(1) {2}
   
   // s1 - s2
   const diff = new Set([...s1].filter(x => !s2.has(x)));
   console.log(diff); // Set(2) {1, 3}
   ```



2. #### WeakMap / WeakSet 和 Map / Set 有什么区别？

   - **WeakMap的键和WeakSet的值只能是`null`以外的对象**。
   - “Weak”指的是弱引用，即**垃圾回收机制不会考虑WeakMap和WeakSet对对象的引用**，一旦WeakMap和WeakSet以外没有地方引用这个对象，那么垃圾回收机制就会把这个对象回收。
   - WeakMap和WeakSet都是**不可遍历**的，因为弱引用机制使得WeakMap和WeakSet的成员是随时可变的，同理也**不存在`size`属性**。
   - WeakMap和WeakSet可以用于储存DOM节点及信息，使得DOM节点被删除时不用手动释放内存，防止内存泄漏。

