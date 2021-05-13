# 3. Vue组件

1. 组件是对结构的抽象和拆分，以达到可复用的目的。每一个应用都可以看成一个组件树。
2. 组件的作用域是独立的

## 3.1 组件注册

### 3.1.1 组件名

1. 根据连线符命名法，字母全小写且用连线符连接，如`my-component-name`，不建议使用单个单词
2. 由于html是大小写不敏感的，采用驼峰命名法注册组件时，引用 `<my-component-name>` 和 `<MyComponentName>` 都是可行的

### 3.1.2 全局注册

1. 示例：

   ```js
   const app = Vue.createApp({});
   app.component('component-a',{
       // ...
   });
   app.component('component-b',{
       // ...
   });   
   ```

2. 全局注册的组件可以在任何新建的组件实例中被引用，包括其子组件

3. **全局组件即使不被使用也会在打包时被包含在最终的构建结果中**，这是我们不希望的

### 3.1.3 局部注册

1. 示例：

   ```js
   const componentA = {
       //...
   };
   
   const app = Vue.createApp({
       //...
       components: {
           'component-a': componentA,
       }
   });
   ```

2. 局部注册的组件只能在限定的实例里使用

3. 因此**局部注册的组件在其子组件中不可用**，如果想使用需要在子组件里注册

## 3.2 prop

1. prop可以看作外界向组件内部传递数据的渠道，包括父组件向子组件传递，静态或动态数据等
2. 定义prop需要尽可能详细，至少需要指定其类型

### 3.2.1 传入数据

1. props可以约束传入数据的类型

   ```js
   props {
       title: string,
       likes: number,
       ...
   }
   ```

2. 传入静态数据：

   ```js
   const app = Vue.createApp({})
   
   app.component('blog-post', {
     props: ['title'],
     template: `<h4>{{ title }}</h4>`
   })
   ```

   ```html
   <blog-post title='my title'></blog-post>
   ```

3. 传入动态数据（传入与父组件的data选项里的数据）

   ```js
   const blogPost = {
   	props: ['title'],
   	template: `<h4>{{ title }}</h4>`
   };
   
   const app = Vue.createApp({
       data(){
           return {
               blogList:[
                   {
                       id:'01',
                       title:'blog-1',
                   },
                   {
                       id:'02',
                       title:'blog-2',
                   }
               ]
           }
       },
       components: {
           'blog-post':blogPost,
       }
   })
   ```

   ```html
     <blog-post
       v-for="post in posts"
       :key="post.id"
       :title="post.title"
     ></blog-post>
   ```

4. **单向数据流**：props**只负责从父到子的数据传递**，即我们不能在props中改变父组件的数据。尤其是通过引用传入的数组和对象，在子组件里修改会影响父组件的数据状态。

   如果需要把父组件的数据纳为己用，需要可以在data选项或利用计算属性声明。

   例如：

   ```js
   prop: ['initValue'],
   // 1. data选项声明这个数据为组件内部数据
   data(){
       return {
           initValue: this.initValue
       }
   }
   ```

   ```js
   prop: ['initValue'],
   // 2. 当需要对数据转化或修饰时，用computed
   computed: {
       normalizedValue: function(){
           return this.size.trim().toLowerCase()
       }
   }
   ```

### 3.2.2 prop验证

1. 类型验证：**可以验证数据是否为js中的原生数据类型或者自定义类**

2. prop验证是针对外部传入的数据进行验证，对于组件内部的数据（如data选项等）是不会验证的

3. **基础类型检查**：

   ```js
   props:{
       propA: String,
       // 可以为多个数据类型
       propB: [String, Number]
   }
   ```

4. **数据默认值**

   ```js
   props: {
       propC:{
           type: Number,
           default: 100,
       },
       // 当数据默认值为一个对象或数组时，需要利用工厂函数获取
       propD: {
           type: Object,
           default: function() {
           return { message: 'hello' }
         	}
       },
       // 当数据默认值为一个函数时，此时的函数不是工厂函数
       propE: {
         type: Function,
         default: function() {
           return 'Default function'
         }
       }
   }
   ```

5. **数据是否必需**

   ```js
   props: {
       propF:{
           type: Number,
           required: true,
       }
   }
   ```

6. **自定义验证函数**

   ```js
   props: {
       propG: {
           type: String,
           validator: function(){
               //...
           }
       }
   }
   ```

## 3.3 $emit

### 3.3.1 监听子组件事件(父组件视角)

1. 由于prop是单向数据流，当子组件需要父组件改变传递数据时，可以**利用`$emit`来向父组件发射事件**，**父组件监听**到子组件的事件后做出处理即可

2. 示例：

   ```html
   <div id="app">
       <!-- 注意事件监听在这里，如果不写传参，默认传入对象 -->
       <cpn @item-click='itemClick'></cpn>
   </div>
   
   <template id="cpn1">
   	<div>
           <button v-for='item in items' @click='btnClick(item)'>
               {{item}}
           </button>
       </div>
   </template>
   
   <script>
       const cpn1 = {
           template: 'cpn1',
           data(){
               return {
                   items:['111','222','333']
               };
           },
           methods: {
               btnClick(item){
                   // 向父组件通信，发射自定义事件，传参为事件名和对象
                   this.$emit('item-click', item);
               }
           }
       }
   	const app = new Vue({
           el:'#app',
           data: {
               
           },
           components: {
               cpn
           },
           methods: {
               itemClick(item){
                   console.log('item');
               }
           }
       })
   </script>
   ```

### 3.3.2 自定义事件（子组件视角）

1. 对于子组件来说，对`$emit`的规范，即是规划好一个子组件的功能：**明确这个组件能做什么**

2. **定义自定义事件**

   - 在组件的emits选项中要定义好组件已发出的事件，更好地记录组件的工作

   ```js
   app.components('custom-form',{
       ...
       // 如果定义了原生事件（如click），则组件中的事件处理函数会替代原生事件监听器
       emits: ['infocus', 'submit'],
   })
   ```

   - 可以在emits选项中定义一个函数来验证自定义事件，返回值为布尔型

     ```js
     app.component('custom-form', {
       emits: {
         // 没有验证
         click: null,
     
         // 验证submit 事件
         submit: ({ email, password }) => {
           if (email && password) {
             return true
           } else {
             console.warn('Invalid submit event payload!')
             return false
           }
         }
       },
       methods: {
         submitForm() {
           this.$emit('submit', { email, password })
         }
       }
     })
     ```

3. **自定义事件与`v-model`指令**

   - `v-model`的原理是利用`v-bind`与data选项的某个数据绑定，而在组件中数据是依赖于props的，所以组件中的`v-model`**原理**是**使用 `modelValue` 作为 prop 和 `update:modelValue` 作为事件**，即在props选项中要定义 `modelValue` ，在emits选项中要定义 `update:modelValue` 事件，达到明确组件作用的目的

   - 示例：

     ```js
     app.component('my-component',{
         props: {
             title: String
         },
         emits: ['update:title'],
         template: `<input type='text' :value='title' @input="$emit('update:title', $event.target.value)"`
     })
     ```

     ```html
     <my-component v-model='title'></my-component>
     ```

   - 由于组件的复用性，在某些情况下为了明确组件的作用，可以向`v-model`传入参数来修改名称，比如

     ```html
     <my-component v-model:title='bookTitle'></my-component>
     ```

     

## 3.4 插槽slot

1. 抽取共性放在组件，个性放在插槽标签

2. ```html
   <template id="cpn">
   	<!-- 共性 -->
       <h1>
           我是标题
       </h1>
       <p>
           我是内容
       </p>
       <!-- 可自由改变 -->
       <!-- 可以设置默认值，比如此处默认为一个button -->
       <slot>
       	<button>
               按钮
           </button>
       </slot>
   </template>
   
   <body>
       <cpn>
       	<!-- 使用时插入的标签会替换成插槽，如果不写的话默认为插槽默认的内容，如果有多个值可以同时放入 -->
           <span>我是插入的内容</span>
       </cpn>
   </body>
   ```

3. **具名插槽**：如果要标识某个需要替换的插槽，就要给插槽name属性值，因为替换时默认替换的是无名插槽

   ```html
   <template id="cpn">
       <h1>
           我是标题
       </h1>
       <p>
           我是内容
       </p>
       <slot name='left'></slot>
       <slot name='right'></slot>
   </template>
   
   <body>
       <cpn #left>
       	<button>
               我要替换左边
           </button>
       </cpn>
   </body>
   ```

4. **作用域插槽**：父组件替换子组件的标签，标签内容由子组件提供

   ```html
   <body>
     <div id="app">
       <!-- 1）默认方式列表展示 -->
       <cpn></cpn>
       
       <!-- 2）以‘-’分隔展示 -->
       <cpn>
         <!-- 注意如果直接这样写是获取不到planguage数组的，因为这个数组定义在子组件内部，只有组件内部才能访问，在vue实例里只能访问vue自己的数据对象（编译作用域） -->
         <!-- <span v-for='lan in planguage'>{{lan}}</span> -->
         
         <!-- 2.作用域插槽使用第二步：给组件绑定v-slot属性（#），等号后面跟着的是slot的域名，域名可以自定义，用于访问域里的属性 -->
         <span #lang='slot'>{{slot.data.join('-')}}</span>
       </cpn>
       
       <!-- 3）以数组形式展示 -->
       <cpn>
         <span #lang='slot'>{{slot.data}}</span>
       </cpn>
     </div>
   
     <template id="cpn">
       <div>
         <!-- 1.作用域插槽使用第一步：给插槽绑定动态属性，属性名自定义，属性值为需要给父组件使用的数据 -->
         <slot :data='planguage' name='lang' >
           <ul>
             <li v-for='lan in planguage'>{{lan}}</li>
           </ul>
         </slot>
     </div>
     </template>
   
     <script>
       const cpn = {
         template: '#cpn',
         // 注意数据内容定义在子组件中
         data() {
           return {
             planguage: ['javascript', 'java', 'python', 'C++']
           }
         },
       };
   
       const app = new Vue({
         el: '#app',
         components: {
           cpn,
         }
       })
     </script>
   </body>
   ```

## 3.5 父子组件互相访问(?)
