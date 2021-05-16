# 6. Vue Router

- 路由的基本使用步骤

  1. 定义或导入路由组件

  2. 定义路由

     ```js
     const routes = [
         {
             path:'/',
             component: ...
         },
     ]
     ```

  3. 创建路由实例

     ```js
     const router = VueRouter.createRouter({
         routes, // 等价于 routes: routes,
         ... // 其他配置
     })
     ```

  4. 创建并挂载根实例，声明支持路由（整个应用支持）

     ```js
     app.use(router);
     ```

## 6.1 路由匹配

### 6.1.1 静态路由匹配

`path`属性值是静态的，比如

```js
const routes = [
    {
        path: 'users/id',
        component: User
    },
]
```

### 6.1.2 动态路由匹配

1. **路径参数**

   - **`path`中的动态段即是路径参数**，其值会被保存在`$route`对象的params中，可以通过`this.$route.params.动态段名`访问

   - **定义动态段**：

     ```js
     const routes = [
         {
             // 动态段以冒号开始，可以单独定义或者接在静态段后面，如/users/user-:id
             path: '/users/:id',
             component: User
         },
     ]
     ```

   - 路径参数允许我们用不同的路径访问**相同的组件实例**，而不是销毁再创建

   - 相同的组件实例意味着组件的生命周期钩子函数不会再被调用，如果希望调用的话，可以**利用`watch`侦听params的变化**，再做出响应

2. **正则表达式**

   - 区别URL

     当出现这种情况时👇，在没办法区别路由的情况下，两者会匹配相同的URL。

     ```js
     const routes = [
         {
             path: '/:orderId',
         },
         {
             path:'/:productName',
         },
     ]
     ```

     **解决办法**：

     - 添加用于区分的静态部分，如`path: '/o/:orderId'`和`path:'/p/:productName'`

     - **利用正则表达式区分**，规定参数的匹配类型。因为此处orderId必须是数字，而productName可以是别的。

       `path: '/:orderId(\\d+)'`

   - 可重复参数

     **匹配具有多个部分的路由**

     ```js
     const routes = [
         // 匹配一个或多个，比如/one/two
         { path: '/:chapters+' },
         // 匹配零个或多个，比如/,/one/two
         { path: '/:chapters*' },
     ]
     ```

     如果和自定义正则表达式一起使用，**要放在右括号后面**

   - 可选参数

     **将参数标记为可选**：`path: '/user/:id?'`

      `?`修饰符和`*`都可以用来标记可选，但是`?`**不可重复**。

   - 捕获所有路由

     **匹配任意路径**：`path: '/:pathMatch(.*)*'`

## 6.2 嵌套与命名

### 6.2.1 嵌套视图

嵌套视图的效果：**一个页面由多层嵌套的组件构成**

<img src="C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210512133246162.png" alt="image-20210512133246162" style="zoom:80%;" />

**怎么在路由配置中表示出嵌套视图？**

1. **给主页面一个`<router-view>`出口，匹配到的嵌套`component`会被渲染在`<router-view>`里**

   ```html
   <template>
   	<div class='user'>
           <h2>User {{ $route.params.id }}</h2>
         	<router-view></router-view>
       </div>
   </template>
   ```

2. **在路由中配置`children`**：`children`只是一个路由数组，里面的元素也是路由对象

   ```js
   const routes = [
       {
           path: '/user/:id',
           component: User,
           // 把被嵌套的路由定义在children里
           children: [
               {
                   // 注意：URL也会被嵌套，因此不用我们手动嵌套URL
                   // path可以为空，即定义为''，此时可以在匹配不到嵌套路由的情况下把你想渲染的组件渲染进<router-view>
                   path: 'profile',
                   component: UserProfile,
               },
               {
                   path: 'posts',
                   component: UserPosts,
               }
           ]
       }
   ]
   ```



### 6.2.2 命名视图（同级展示）

同级展示命名视图的效果：**一个页面中有多个同级视图，而不是嵌套展示**。

比如把一个页面划分为左中右三个同级视图，为了区分开三个`<router-view>`，我们需要对视图进行命名。

1. 主页面部分

```html
<router-view name='left'></router-view>
<!-- 不写明即是默认name为default -->
<router-view></router-view>
<router-view name='right'></router-view>
```

2. 路由对象的配置

   ```js
   const router = createVouter({
       routes: [
           {
               path: '/',
               // 注意是同级视图，没有children数组，要用到的组件就放在components里，名字和<router-view>一致
               components: {
                   default: Home,
                   // 名字与组件名一致时的缩写
                   LeftSidebar,
                   RightSidebar,
               }
           }
       ]
   })
   ```

   

### 6.2.3 嵌套命名视图

嵌套命名视图的效果：**页面中同时包含同级视图与嵌套视图**，比如下面这个，一个页面有两个同级视图nav和main，main视图里又嵌套了email或profile视图

<img src="C:\Users\胖可丁\AppData\Roaming\Typora\typora-user-images\image-20210512234621565.png" alt="image-20210512234621565" style="zoom:80%;" />
**怎么在路由配置中利用命名视图表示出同级视图？**

1. UserSettings是顶级的视图组件，为了表示出上述结构，其`<template>`标签应当大致如下

   ```html
   <template>
   	<div>
           <h1>User Settings</h1>
           <!-- 常规组件nav -->
           <NavBar />
           <!-- 默认的view出口 -->
           <router-view />
           <!-- 与name匹配的view出口，即命名视图 -->
           <router-view name="preview" />
       </div>
   </template>
   ```

2. 配置路由对象

   ```js
   const routes = [
       {
           path: '/settings',
           component: UserSettings,
           children: [
               {
                   path: 'emails',
                   component: UserEmailsSubscriptions,
               },
               {
                   path: 'profile',
                   // 此处配置连接view视图
                   components: {
                       default: UserProfile,
                       preview: UserProfilePreview
                   }
               }
           ]
       }
   ]
   ```

   

## 6.3 导航

### 6.3.1 命名路由

1. 顾名思义，路由对象可以声明name属性

   ```js
   const routes = [
       {
           path: '...',
           component: ...,
           name: 'user',
       }
   ]
   ```

2. 当路由对象具有name时，我们可以在定义导航链接时**直接用name链接到该路由**，而不是指定URL path

3. 命名路由会**自动编码、解码params**，我们可以不用硬编码URL

### 6.3.2 定义导航链接

1. **编程式导航**：使用路由示例的`push`方法导航到不同的URL，这个方法会向 history 栈添加一个新的记录

   ```js
   // 字符串路径
   router.push('/user/Jhon');
   
   // 路由对象
   // 非命名路由：带有path，query参数等等。注意path和params不能共存
   router.push({ path:'query', query={ name:'Jhon' }, });
   // 命名路由：不用指定URL，传入params参数
   router.push({ name:'user', params={ username:'Jhon' },});
   ```

2. **声明式导航**：使用`<router-link>`创建a标签来定义导航链接

   ```html
   <!-- 通过指定to来指定链接，可以指定一个path或命名路由对象 -->
   <router-link to='/user'>Go to User</router-link>
   <router-link to='{ name:'User', params:{ username: 'Jhon', } }'>Go to User</router-link>
   ```

   当点击`<router-link>`时，内部会调用`router.push()`方法，因此属性 `to` 与 `router.push` 的规则完全相同

### 6.3.3 导航守卫

1. 导航守卫可以理解为路由跳转过程中的一个个钩子函数，通过跳转或取消的方式守卫导航

2. **导航解析流程**

   > 1. 导航被触发
   > 2. 失活组件调用`beforeRouteLeave`守卫
   > 3. 调用全局前置守卫`beforeEach`
   > 4. 重用组件调用`beforeRouteUpdate`守卫
   > 5. 路由配置调用路由独享`beforeEnter`守卫
   > 6. 解析异步路由组件
   > 7. 在被激活的新组件里调用`beforeRouteEnter`守卫
   > 8. 调用全局解析守卫`beforeResolve`
   > 9. 导航被确认
   > 10. 调用全局后置`afterEach`钩子
   > 11. 触发DOM更新
   > 12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入

3. **导航守卫方法**

   - 传参

     - `to`：要进入的目标路由

     - `from`：当前导航要离开的路由

     - `next`：用于连接钩子函数的回调，是**可选**参数。但是**一旦传入了`next`参数，要确保`next`在导航守卫中被严格调用一次**，否则路由的连接会被中断。

       `next()`的传参可以是`path`字符串、路由对象、error实例。

   - 返回值

     - `true`&`undefined`：导航有效，调用下一个导航守卫
     - `false` ：取消导航，充值到`from`对应的地址
     - 一个路由地址：跳转到一个不同的地址

#### 6.3.3.1 全局守卫

路由实例上的钩子函数

```js
const router = createRouter({ ... })

router.beforeEach((to, from) => {
  // ...
  return false
})
```

1. **全局前置守卫`beforeEach`**

   - 在守卫方法中检查`meta`属性的`requiresAuth`字段来验证权限

   > **路由元信息 `meta`属性**：用于定义路由的附加信息
   >
   > 当定义:`meta: { requiresAuth: true }`时，意味着访问该组件需要身份验证。
   >
   > **怎么访问`meta`属性？**
   >
   > - 遍历路由示例的路由记录数组`$route.matched`来检查`meta`
   > - 利用`$route.meta`方法（非递归合并**所有 `meta`** 字段）

2. **全局解析守卫`beforeResolve`**

   可以在用户无法进入页面的情况下获取数据或进行任何其他你想避免的操作

3. **全局后置`afterEach`钩子**

   - 不接受`next`函数
   - 可以结合`meta`属性修改`title`

#### 6.3.3.2 路由独享守卫`beforeEnter`

路由配置上定义的守卫，**只在进入路由时触发**

```js
const routes = [
  {
    path: '/users/:id',
    component: UserDetails,
    beforeEnter: (to, from) => {
      // ...
      return false
    },
  },
]
```

#### 6.3.3.3 组件内守卫

路由组件内定义的守卫

```js
const UserDetails = {
    template: `...`,
    beforeRouteEnter(to, from) {
        // ...
    }
}
```

1. `beforeRouteEnter`：**在渲染组件对应的路由前调用**，此时新的组件实例还没有被创建，因此该守卫不能通过`this`获取组件实例
2. `beforeRouteUpdate`：**路由改变，但是组件被复用**时调用，如`path: '/user/:userId'`，不同的userId会改变路由，但是渲染的组件是一样的
3. `beforeRouteLeave`：**在离开渲染组件对应的路由时调用**

## 6.5 数据获取

1. **导航完成之后获取**：先完成导航，再**利用组件生命周期钩子函数**获取数据，可以显示加载中
2. **获取数据后再导航**，导航完成前**利用路由守卫函数**获取数据，获取成功后再导航，获取时用户会停留在当前页面，可以显示进度条

## 6.6 组件与路由

### 6.6.1 路由组件传参

1. 在组件中使用`$route.params.xxx`可以获取路由的传参数据来进行展示，但是会导致组件与路由耦合度高

2. 使用`props`来替换`$route`

   - 布尔模式：路由中把`props`设置为`true`，即默认把路由params设置为prop

     ```js
     const User = {
         props: ['id'],
         template: `<div>User {{ id }}</div>`
     };
     const routes = [{
         path: '/user/:id',
         component: User,
         props: true,
     }];
     ```

     当结合命名视图使用时，必须为每个命名视图定义`props`

     ```js
     const routes = [{
         path: '/user/:id',
         component: { default: User, sidebar: Sidebar },
         props: true,
     }];
     ```

   - 对象模式：常规地为组件设置`props`

   - 函数模式：允许改变参数值再传入、结合路由参数传入静态数据等等

     `props: route => ({ query: route.query.q });`

### 6.6.2 组合式API与路由

1. **访问路由示例和当前路由**

   不能直接访问 `this.$router` 或 `this.$route`

   ```js
   const router = useRouter();
   const route = useRoute();
   ```

   `route`对象是响应式的，可以监听它的属性。

2. **导航守卫**

   Vue Router 将`beforeRouteUpdate`和`beforeRouteLeave`守卫作为**组合式 API 函数**公开

   ```js
   import { onBeforeRouteLeave, onBeforeRouteUpdate } from 'vue-router';
   
   export default {
       setup() {
           onBeforeRouteLeave( (to,from) =>{
               
           })
       }
   }
   ```

   
