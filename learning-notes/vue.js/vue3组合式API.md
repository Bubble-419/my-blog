# 5. 组合式API

1. 什么是组合式API？

   Vue2中采用的是选项式API的方式，即一个组件实例由多个选项组成，如data、computed、methods等等。Vue3新增了组合式API，即允许我们像写函数一样自由地组合逻辑和代码，最后组合形成一个组件。

2. 组合式API能做什么？

   利用`setup`选项**将与同一个逻辑关注点相关的代码配置在一起**，使我们更加关注组件的作用

## 5.1 `setup`选项

1. `setup`是一个函数，传参是`props`和`context`，返回值是要暴露给组件其余部分的内容。
2. `setup`选项是在创建组件之前执行的，所以没有`this`，也不能访问`props`以外的组件内部定义的属性，如data选项、计算属性等等。

### 5.1.1 `setup`选项的**参数**

1. **`props`**

   - `props`是响应式的，所以当我们需要对其进行解构时，不能使用ES6的解构方式，而是要使用`toRefs`或`toRef`方法。

   - 使用`toRefs`（生成）：把props响应式对象转化为普通对象，其结果对象的每个property都指向原始对象相应property的ref对象。

     使用`toRef`（创建）：当props的某个prop是可选的，我们不确定是否有值传入，如果没有值传入的话，`toRefs`是不会为该prop生成ref的，此时就要使用`toRef`为其创建一个`ref`。

2. **`context`**

   - `context`不是响应式的，只是一个普通的js对象，包含`attrs`、`slots`、`emit`三个property。

   - 我们可以对`context`进行直接解构，如：

     ```js
     export default{
         setup(props, { attrs, slots, emit }){
             ...
         }
     }
     ```

     但是要注意，`attrs`和`slots`是**非响应式**的**有状态对象**，因此我们要避免对其解构。

### 5.1.2 `setup`选项的返回值

通常我们会返回一个对象，模板是可以访问`setup`选项返回的对象property的，但是要注意**返回的`refs` 会被自动解开，所以无需再使用`.value`来访问值**了。

```vue
<!-- 官文里的例子 MyBook.vue -->
<template>
  <div>{{ readersNumber }} {{ book.title }}</div>
</template>

<script>
  import { ref, reactive } from 'vue'

  export default {
    setup() {
      const readersNumber = ref(0)
      const book = reactive({ title: 'Vue 3 Guide' })

      // expose to template
      return {
        readersNumber,
        book
      }
    }
  }
</script>
```

### 5.1.3 举例说明`setup`选项的使用步骤（以官文为例）

- **确定我们关注的一个逻辑点**

  > 从假定的外部 API 获取该用户名的仓库，并在用户更改时刷新它

- **确定`setup`选项要做什么**

  - 仓库列表
  - 更新仓库列表的函数
  - 返回列表和函数，以便其他组件选项可以访问它们

- **划分功能模块文件**

  ```js
  // 本文件：src/composables/useUserRepositories.js
  // 一个独立的组合式函数
  
  // 导入
  import {
    fetchUserRepositories
  } from '@/api/repositories';
  import {
    ref, onMounted, watch
  } from 'vue'
  
  export default function useUserRepositories(user) {
      // 使用ref函数使得选项内的变量是响应式的，ref函数接收一个值并把值封装进ref对象里
      // 1. 仓库列表
      const repositories = ref([]); // 即：此处repositories = { value: [] }
        
      // 2. 更新仓库列表的函数
      const getUserRepositories = async () => {
        repositories.value = await fetchUserRepositories(props.user);
      };
      // 在生命周期钩子函数中调用该函数
      onMounted(getUserRepositories); // 在 `mounted` 时调用 `getUserRepositories`
      // 监听prop，在用户数据改变时做出响应
      watch(user, getUserRepositories); // (监听对象， 回调函数)
        
      // 3. 返回列表和函数
      return {
        repositories,
        getUserRepositories
      }
    }
  ```

- **在`setup`选项中使用定义好的功能模块，即组合式函数**

  ```vue
  <!-- 本文件：src/components/UserRepositories.vue -->
  
  <template>
  	...
  </template>
  
  <script>
      // 导入组合式函数
      import useUserRepositories from '@/composables/useUserRepositories'
      import { toRefs } from 'vue'
      
      export default {
          components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
          props: {
              user: {
                  type: String,
                  required: true
              }
          },
          
          // setup选项开始
          setup(props){
              // 1. 获取user对象
              // toRefs可以用来为源响应式对象上的某个 property 新创建一个 ref，比如此处props对象的user property。该ref对象可以被传递，且会与源property形成响应式连接
              const { user } = toRefs(props, 'user');
              
              // 2. 获取组合式函数的返回值
              const { repositories, getUserRepositories } = useUserRepositories(user);
              
              // 3. 返回数据给组件实例的其他部分
              return {
                  getUserRepositories
              }
          }
          // setup选项结束
          
          // 组件其他部分 ...
  </script>
  
  <style scopled>
  	...
  </style>
  ```

## 5.2 生命周期钩子

1. 和选项式API的区别：

   <img src="C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210511164005019.png" alt="image-20210511164005019" style="zoom: 80%;" />

2. 注意钩子函数是在`setup`选项里直接调用的

3. 不需要显式定义`beforeCreate`和`created`是因为`setup`选项里包含的代码本身就是在这两个生命周期钩子运行的

## 5.3 `provide`&`inject`

1. 当我们需要从父组件传递数据到子组件时，我们通常会想到利用`props`，但是`props`的传递是逐级的，如果有很多层组件结构的话，数据传递起来会很麻烦。

   于是此时我们可以使用`provide`&`inject`来进行数据的传递，可以把这个注入的过程看成“long range props”。

2. 使用`provide`&`inject`，父组件不需要知道哪些子组件使用了它`provide`的property，子组件也不知道自己`inject`  的property来自哪个父组件。

### 5.3.1 响应式地使用

1. 为了增加 provide 值和 inject 值之间的响应性，我们可以在 provide 值时使用`ref`或`reactive`。

2. 示例：

   ```js
   // 父组件 src/components/MyMap.vue js部分
   
   // 1. 导入provide方法，使我们能利用provide定义变量
   import { provide, reactive, ref } from 'vue';
   
   export default {
       // 省略组件其它部分
       setup() {
           // 2. 声明响应式变量
           const location = ref('North Pole');
           const geolocation = reactive({
               longitude: 90,
               latitude: 135
           });
           // 3. 使用provide
           // 两个参数分别为property的name和value
           provide('location', location);
           provide('geolocation', geolocation);
       }
   }
   
   ```

   ```js
   // 子组件 src/components/MyMarker.vue js部分
   
   // 1. 导入inject方法
   import { inject } from 'vue';
   
   export default {
       setup() {
           // 2. 使用inject
           // 两个参数分别为 要inject的property名称 和 赋予property的默认值（可选）
           const userLocation = inject('location', 'The Universe');
           const userGeolocation = inject('geolocation');
           
           return {
               ...
           }
       }
   }
   ```

3. **修改响应式property**

   - 当对property有修改需求时，应当尽可能在声明provide的组件中进行修改，即**父组件可以在`methods`选项中定义一个对property的修改函数**

   - 如果我们需要在被注入数据的子组件中更新inject的数据，我们可以**在父组件中把修改函数provide出去，子组件再用inject接收**，从而达到修改的目的

   - 如果我们不希望provide出去的数据被修改，可以使用`readonly`

     ```js
     provide('location', readonly(location));
     ```

     
