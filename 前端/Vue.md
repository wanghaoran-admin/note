# 第一章 vue概念

### 1.简介

1. Vue.js是一个构建用户界面的渐进式框架。



### 2.主要构成部分

- Vue实例：是Vue.js应用程序中的主要构建块。每个Vue应用程序都会启动一个根 Vue 实例，可以将它看作一个“ComponentTree”，所有组件都是这个根实例下的子根实例。
- 模板：Vue.js使用基于HTML的模板语法，允许开发者声明式地将DOM绑定至底层Vue实例的数据。
- 组件：组件是Vue.js最强大的功能之一，组件可以扩展HTML元素，封装可重用的代码。





# 第二章 vue入门

### 1.入门案例

```html
<script type="module">//使用js的导入导出
    //导入vue模块
    import {createApp,ref} from "./vue3.js";
    //创建vue实例
    createApp({
        //同等脚手架中<script setup>
        setup(){
            const count = ref(100)
            function dian() {
                alert("111111")
            }
            return { //无论是属性还是方法都需要返回
                count,dian
            }
        },
    }).mount("#app") //指定被vue管理的元素范围，页面中有id为app的元素
</script>
```



# 第三章 常用指令

### 1.v-for

**作用**：这个指令用于根据数组循环渲染一个元素。

**语法**：v-for = "(item,index) in items"

**参数说明**：

​		**items** 为遍历的数值  

​		**item** 为遍历出来的元素 

​		**indexs** 为下标，从0开始；可以省略，省略语法  v-for = "item in items"

**示例**：	

在需要循环的元素中添加v-for指令

```html
<tr v-for="(article,index) in articleList">
     <td>{{article.title}}</td>
     <td>{{article.category}}</td>
     <td>{{article.time}}</td>
     <td>{{article.state}}</td>
     <td>
         <button>编辑</button>
         <button>删除</button>
     </td>
</tr>
```



在data中创建数组

```html
    <script type="module">
        //导入vue模块
        import { createApp} from 
                'https://unpkg.com/vue@3/dist/vue.esm-browser.js'
        //创建应用实例
        createApp({
            data() {
                return {
                    articleList:[
                        {
                            title:"医疗反腐绝非砍医护收入",
                            category:"时事",
                            time:"2023-09-5",
                            state:"已发布"
                        },
                        {
                            title:"中国男篮缘何一败涂地？",
                            category:"篮球",
                            time:"2023-09-5",
                            state:"草稿"
                        },
                        {
                            title:"华山景区已受大风影响阵风达7-8级，未来24小时将持续",
                            category:"旅游",
                            time:"2023-09-5",
                            state:"已发布"
                        }
                    ]
                }
            }
        }).mount("#app")//控制页面元素
    </script>
```





### 2.v-bind

**作用**：这个指令用于绑定一个属性值

**语法**：v-bind:属性名="属性值"

**简化**：属性名="属性值"

**示例**：

使用v-bind进行属性的绑定【href中的url要和vue实例return中的键一致】

```html
    <div id="app">
      <!-- v-bind:href="url" 可写成 :href="url"-->   
        <a v-bind:href="url">黑马官网</a>
    </div>
```



vue中只需要定义url这个属性

```html
    <script type="module">
        //引入vue模块
        import { createApp} from 
                'https://unpkg.com/vue@3/dist/vue.esm-browser.js'
        //创建vue应用实例
        createApp({
            data() {
                return {
                   url:"https://www.baidu.com/" 
                }
            }
        }).mount("#app")//控制html元素
    </script>
```





### 3.v-if 、v-else-if、v-else

**作用**：这指令，都是用来控制元素显示与隐藏的

**语法**：v-if="表达式"  表达式为true显示 false隐藏

**和v-show的区别**：v-if适用于一个元素要么出现要么不出现，show适用于频繁切换的场景比如鼠标访导航条上展示对应的信息。

**示例**：

如果消费等级小于1就展示9.9 小于2就展示19.9 否则29.9

```html
    <div id="app">
        手链价格为:  <span v-if="level<1">9.9</span>
                         <span v-else-if="level<2">19.9</span>
                    <span v-else>29.9</span>
    </div>
```

​		

两个变量 name and level

```html
	<script type="module">
        //导入vue模块
        import { createApp} from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

        //创建vue应用实例
        createApp({
            data() {
                return {
                    name:"张三",
                    level:2
                }
            }
        }).mount("#app")//控制html元素
    </script>
```





### 4.v-on

**作用**：这个指令用于监听DOM事件

**语法**：v-on:事件名="方法" 

**简写**：@事件名="方法"

**示例**：

在vue实例中创建methods定义方法，方法格式【方法名:function (){方法内容}】

```html
    <script type="module">
        //导入vue模块
        import { createApp} from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

        //创建vue应用实例
        createApp({
            data() {
                return {
                    //定义数据
                }
            },
            methods:{
                money:function (){
                    alert("送你一钱！！")
                },
                hua:function (){
                    alert("送你一朵花！！")
                }
            }
        }).mount("#app");//控制html元素

    </script>
```



```html
    <div id="app">
        <button v-on:click="money">点我有惊喜</button> &nbsp;
        <button @click="hua">再点更惊喜</button>
    </div>
```







### 5.v-model

**作用**：这个指令创建了双向数据绑定，当表单输入和应用状态之间需要互动时，非常有用。

**语法**：v-model="属性名" 

**示例**：

```html
    <script type="module">
        //导入vue模块
        import { createApp } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'
        //创建vue应用实例
        createApp({
            data() {
                return {
                    //定义数据
                    name:"",
                    state:"",

                    articleList: [{
                        title: "医疗反腐绝非砍医护收入",
                        category: "时事",
                        time: "2023-09-5",
                        state: "已发布"
                    },
                    {
                        title: "中国男篮缘何一败涂地？",
                        category: "篮球",
                        time: "2023-09-5",
                        state: "草稿"
                    },
                    {
                        title: "华山景区已受大风影响阵风达7-8级，未来24小时将持续",
                        category: "旅游",
                        time: "2023-09-5",
                        state: "已发布"
                    }]
                }
            },methods:{
                clear:function(){
                    //this代表vue实例
                    this.name = ""
                    this.state = ""
                }
            }

        }).mount("#app")//控制html元素
    </script>
```



 

```html
    <div id="app">

        文章分类: <input type="text" v-model="name"/><span>{{name}}</span>
        发布状态: <input type="text" v-model="state"/><span>{{state}}</span>

        <button @click="clear">搜索</button>

        <br />
        <br />
        <table border="1 solid" colspa="0" cellspacing="0">
            <tr>
                <th>文章标题</th>
                <th>分类</th>
                <th>发表时间</th>
                <th>状态</th>
                <th>操作</th>
            </tr>
            <tr v-for="(article,index) in articleList">
                <td>{{article.title}}</td>
                <td>{{article.category}}</td>
                <td>{{article.time}}</td>
                <td>{{article.state}}</td>
                <td>
                    <button>编辑</button>
                    <button>删除</button>
                </td>
            </tr>
        </table>
    </div>
```











### 6.常用指令列表

| 指令                    | 说明                                                         |
| ----------------------- | ------------------------------------------------------------ |
| v-for                   | 这个指令用于根据数组循环渲染一个元素。                       |
| v-bind                  | 这个指令用于绑定一个属性，或者一个对象的属性到元素的属性。   |
| v-if  v-else-if  v-else | 这是一个条件指令，用于根据表达式的值在DOM中渲染或销毁元素。  |
| v-show                  | 另一个条件指令，与v-if相似，但无论条件是真或假，都会渲染元素（将元素设置为`display:none`或`display:block`）。 |
| v-model                 | 这个指令创建了双向数据绑定，当表单输入和应用状态之间需要互动时，非常有用。 |
| v-on                    | 这个指令用于监听DOM事件，有一个简写是"@"                     |
| v-html                  | 这个指令用于输出HTML代码，而不仅仅是文本。                   |
| v-text                  | 与插值表达式（{{ }}）类似，更新元素的 textContent。          |
| v-once                  | 这个指令渲染一次性的静态内容。在之后的重新渲染中，元素及其所有的子节点将被视为静态内容并跳过。 |
| v-pre                   | 跳过这个元素和它的子元素的编译过程。可以用来显示原始的Mustache标签。 |





# 第四章 axios的使用

### 1.简介

Axios 提供了一种简单的方式去发送 HTTP 请求，使得我们在前端应用中获取、发布或者更新数据变得更加容易。



### 2.语法

**data**中的数据是**json格式**的

```java
	   axios.请求方式(url,data,config).then(result=>{
            console.log(result.data);
        }).catch(err=>{
            console.log(err);
        })
```





# 第五章 vue工程的创建

### 1.工程目录

| 目录          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| node_modules/ | 存储所有的项目依赖                                           |
| npm install   | 安装的所有包都会被放在这个目录下。                           |
| public/       | 这个文件夹包含了不会被Webpack处理的静态资源                  |
| src/          | 这个目录存储你的源代码                                       |
| src/main.js   | 这是应用的入口文件，负责创建Vue实例。                        |
| src/App.vue   | 这是一个根级别的Vue组件                                      |
| src/main.js   | 入口文件                                                     |
| components/   | 这个目录用于存储应用中小型且通常可复用的组件。               |
| assets/       | 这个目录用于存储你需要的静态资源，如`images/`目录存放图片、`styles/`目录存放样式文件等。 |
| router/       | 如果你的项目使用了 `vue-router`，`router` 目录的 `index.js` 文件用于配置路由。 |
| store/        | 如果你的项目使用了 `vuex`，那么 `store` 目录的 `index.js` 文件通常包含着 `Vuex` `store` 的文件。 |
| views/        | 这个目录用于存放应用的页面级别的组件。                       |
| package.json  | 这个文件包含了项目的元数据，如项目的名称、版本、描述、作者等信息，还包含了项目的依赖信息和一些可执行的脚本。 |
| vue.config.js | 这个文件是可选的，包含了对vue CLI 的项目进行专门配置的一些代码，如修改webpack配置、增加CSS预处理器等。 |





### 2.api风格

Vue.js**有两种不同**的API风格，分别被称为**选项式API**和**组合式API**。



**选项式API**: Vue 2.0在提供开发功能时采用的是选项式API

**缺点**：虽然这对于较小的项目可能简单易理解，但是随着项目复杂度的增长，一个组件的代码可能会在不同的选项中散布，这使得逻辑的维护和重用变得困难。



**组合式API**: Vue 3.0新增了组合式API

**优点**：好处是可以让代码的组织更加灵活，代码的逻辑结构更加清晰





### 3.vue项目中使用axios

**npm指令**npm install axios

这条指令会把axios安装到**node_modules目录下**

进行导入:

```html
<script setup>
   import axios from "axios"
</script>

<template>
</template>

<style scoped>
</style>
```





### 4.vue项目中使用ref

**简介**：`ref`函数在Vue.js中的作用主要是创建可响应的数据。

**语法**：import { ref } from 'vue'





### 5.入门案例

在App.vue把自己编写的**文件导入**

```html
<script setup>
  import Artice from './Artice.vue';
</script>

<template>
  <Artice/>
</template>

<style scoped>

</style>
```



**自己编写**的vue文件

```html
<script setup>
    	//导入axios
    import axios from "axios"
     //引入ref
    import {ref} from "vue"
     //创建数组用来接收数据
    const list = ref([]);
     //分页条件
    const tiao = ref({
        "category":"",
        "state":""
    })
    //搜索方法
    const sou = function() {
        axios.get("http://localhost:8080/article/search",{
            params:{...tiao.value}
        }).then(result=>{
            list.value = result.data
        }).catch(err=>{
            
        })
    }
    //获取数据list
    axios.get("http://localhost:8080/article/getAll").then(result=>{
        list.value = result.data
    }).catch(err=>{
        
    })
</script>

<template>
<div>
文章分类: <input type="text" v-model="tiao.category">
发布状态: <input type="text" v-model="tiao.state">
<button @click="sou">搜索</button>
<br />
<br />
<table border="1 solid" colspa="0" cellspacing="0">
    <tr>
        <th>文章标题</th>
        <th>分类</th>
        <th>发表时间</th>
        <th>状态</th>
        <th>操作</th>
    </tr>
    <tr v-for="f in list">
        <td>{{ f.title }}</td>
        <td>{{ f.category }}</td>
        <td>{{ f.time }}</td>
        <td>{{ f.state }}</td>
        <td>
            <button>编辑</button>
            <button>删除</button>
        </td>
    </tr>
</table>
</div>
</template>

<style scoped>

</style>
```





### 6.简化案例

request.js文件

```java
//定制请求的实例
import axios from "axios"
const baseurl = "/api";
const instance = axios.create({ baseURL: baseurl });//创建axios对象

//添加响应拦截器
//这是一个在响应被then或catch处理之前处理响应的函数
instance.interceptors.response.use(
    result => {
        return result.data
    },
    err => {
        alert("出现异常")
        return Promise.reject(err);//异步转化为失败
    }
)

export default instance
```



artice.js文件

```java
//导入request.vue文件
import request from "@/utils/request"

//获取所有文章数据
export function getallservice() {
    //发送异步请求
    return request.get("/article/getAll")
}

//根据文章内容搜索的数据
export function searchservice(shu) {
    //发送异步请求
    return request.get("/article/search", { params: shu })
}
```





Artice.vue文件【这是vue文件】

```java
<script setup>
    import {getallservice,searchservice} from "@/api/artice"
    import {ref} from "vue"
    const list = ref([]);
	//使用async,await开启同步
    const getall = async function(){
        let data = await getallservice();
        list.value = data;
    }
    getall();
    const tiao = ref({
        "category":"",
        "state":""
    })
    const sou = async function() {
        let data = await searchservice({...tiao.value})
        list.value = data
    }
</script>

<template>
<div>
文章分类: <input type="text" v-model="tiao.category">
发布状态: <input type="text" v-model="tiao.state">
<button @click="sou">搜索</button>
<br />
<br />
<table border="1 solid" colspa="0" cellspacing="0">
    <tr>
        <th>文章标题</th>
        <th>分类</th>
        <th>发表时间</th>
        <th>状态</th>
        <th>操作</th>
    </tr>
    <tr v-for="f in list">
        <td>{{ f.title }}</td>
        <td>{{ f.category }}</td>
        <td>{{ f.time }}</td>
        <td>{{ f.state }}</td>
        <td>
            <button>编辑</button>
            <button>删除</button>
        </td>
    </tr>
</table>
</div>
</template>

<style scoped>

</style>
```





# 第六章 npm指令

### 1.列表

| 指令                            | 用处                      |
| ------------------------------- | ------------------------- |
| npm init vue@latest             | 开始创建vue工程           |
| npm run dev                     | 启动vue项目，默认端口5173 |
| npm install axios               | vue项目安装axios          |
| npm install element-plus --save | 安装element-plus          |
| npm install vue-router@4        | 安装路由                  |
| npm install                     | 安装vue依赖               |
| npm install qs                  | 安装qs                    |





# 第七章 跨域问题

### 1.跨域是什么？

跨域就是**不同协议**、**不同域名**、**不同端口**发出的**ajax请求**会失败！！！



### 2.具体实现步骤

在vite.config.js文件中配置以下代码

```js
 server: {
    proxy: {
      '/api': {//获取路径中包含api的请求
        target: "http://localhost:8080",//后台服务所在的源
        changeOrigin: true,//是否修改源
        rewrite: (path) => path.replace(/^\/api/, '')//修改后的路径
      }
    }
  }
```

【注意以上代码都是紧跟defineConfig配置的】





# 第八章 路由

### 1.简介

它会根据当前的 URL 或其他条件，判断应该显示哪一个视图或组件。一种常见的方法是使用URL的路径部分来决定，比如 "/home" 对应主页，"/user" 对应用户页面等等。



### 2.使用

在src目录下创建router文件夹，在router文件中创建index.js,编写以下代码

```js
//导入路由
import { createRouter, createWebHistory } from "vue-router"

//导入组件
import LoginVue from '@/views/Login.vue';
import LayoutVue from '@/views/Layout.vue';

//定义关系
const routes = [
    { path: '/login', component: LoginVue },
    { path: '/', component: LayoutVue }
]

//创建路由器
const router = createRouter({
    history: createWebHistory(),
    routes: routes
})

//导出路由
export default router 
```



在main.js文件中配置

```js
//路由文件的位置
import router from '@/router/index.js'
app.use(router)
```



再根组件中配置

```vue
<script setup>
   
</script>

<template>
<!--使用router-vier组件-->
  <router-view/>
</template>

<style scoped>

</style>
```





### 3.子路由

假设我们有一个用户列表页，点击列表项后展示用户详情页，这时候用户详情页就可以作为用户列表页的子路由。

在router文件夹中index.js文件配置children

```js
//定义关系
const routes = [
    { path: '/login', component: LoginVue },
    {							//redirect:默认的路径
        path: '/', component: LayoutVue, redirect: "/artice/manager", children: [
            		//子路由
            { path: '/artice/category', component: ArticleCategoryVue },
            { path: '/artice/manager', component: ArticleManageVue },
            { path: '/user/info', component: UserInfoVue },
            { path: '/user/avatar', component: userAvatarVue },
            { path: '/user/resetPassword', component: UserResetPasswordVue }
        ]
    }
]
```



配置子路由

```html
 <el-menu-item index="/artice/category">
       <el-icon>
       	<Management />
       </el-icon>
       <span>文章分类</span>
</el-menu-item>
```



### 4.useRouter、useRoute

`useRoute` 和 `useRouter` 是 Vue Router 提供的两个API，用于在 Vue 中访问路由信息和路由对象。

```js
import {useRouter,useRoute} from "vue-router"
const router = useRouter();
const route = useRoute();
```





#### useRoute常用方法

**useRoute**：`useRoute` 是一个函数，它返回一个包含当前路由信息的响应式对象

```js
import { useRoute } from 'vue-router';

const route = useRoute();

route.path:  获取当前路径

route.params: 获取当前路由的参数。

route.query: 获取当前路由的查询参数。

route.meta: 获取当前路由的元信息。
```



#### useRouter常用方法

```js
import { useRouter } from 'vue-router';

router.push: 导航到一个新的 URL。

router.replace: 替换当前路由。

router.go: 前进或后退历史记录中的指定步数。

router.back: 后退一步。

router.forward: 前进一步。

router.currentRoute: 获取当前活动路由的路由信息对象。
```



### 5.带参数的路由

在路由配置中，使用 `:` 来表示动态参数。例如：

```js
 path: '/xiang/:id', // 使用 :id 作为动态参数
```





# 第九章 pinia状态管理库

### 1.简介

pinia是vue的专属管理库，他允许你跨组件或页面共享状态



### 2.使用

定义pinia

```js
import { ref } from 'vue'
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', () => {
  //定义属性
  const isCollapse = ref(false)

  //定义方法
  function setCollapse(value) {
    isCollapse.value = value
  }
    
  //一定要返回出去
  return { isCollapse,setCollapse }
})
```



使用pinia

```js
import { useCounterStore } from '@/stores/counter'
const counter = useCounterStore()

// 使用 computed 确保 isCollapse 是响应式的
const isCollapse = computed(() => counter.isCollapse)
```







# 第十章 函数的使用

### 1.computed函数

`computed` 是 Vue 3 提供的一个功能，当依赖的数据发生变化时，计算属性会重新计算。它们可以提高性能，并让你以声明式的方式处理数据。

```js
import { computed, ref } from 'vue';

const count = ref(10);
const doubledCount = computed(() => count.value * 2);
```

在这个例子中：

- `count` 是一个响应式数据。
- `doubledCount` 是一个计算属性，它的值是 `count` 值的两倍。`doubledCount` 会自动更新，当 `count` 改变时，它的值会重新计算。





### 2.watch函数

监视单一数据源

```js
import { ref, watch } from 'vue';

const count = ref(0);

watch(count, (newValue, oldValue) => {
  console.log(`count changed from ${oldValue} to ${newValue}`);
});
```

在这个例子中：

- `count` 是一个响应式数据。
- `watch` 侦听 `count` 的变化。
- 当 `count` 发生变化时，回调函数会被调用，`newValue` 和 `oldValue` 分别是变化后的值和变化前的值。



监视多个数据源

```js
import { ref, watch } from 'vue';

const count = ref(0);
const name = ref('Alice');

watch([count, name], ([newCount, newName], [oldCount, oldName]) => {
  console.log(`count changed from ${oldCount} to ${newCount}`);
  console.log(`name changed from ${oldName} to ${newName}`);
});

```

在这个例子中：

- `watch` 侦听 `count` 和 `name` 两个响应式数据。
- 回调函数会在其中任意一个数据源发生变化时被调用。
- 



# 第章 qs的使用

qs就是把js对象装换为post请求数据的一个工具



### 1.使用

引入qs

```js
import qs from 'qs';
```



进行装换

```js
 qs.stringify(fromData)
```







# 第十一章 iconfont矢量图

https://www.iconfont.cn/?spm=a313x.search_index.i3.2.4f863a81dhE331



### 1.使用

找到想要的资源加入购物车 

齐全了之后点击下载代码

解压出来拖进vue工程中的public目录或src中的assets中

在main.js中引入 import "@/assets/iconfont/iconfont.css"

根据下载包中的示例进行操作





# 第十二章 父组件向子组件传递数据

### 父组件

```vue
<template>
    <div>
      <h2>父组件</h2>
      <ChildComponent :shu="shu" /> 
    </div>
</template>
  
<script setup>
  import ChildComponent from './ChildComponent.vue';
  const shu = "这是父组件的数据";
</script>
```



### 子组件

```vue
<template>
    <div>
      <h3>子组件</h3>
      <p>{{ shu }}</p>
    </div>
</template>
  
<script setup>
    // 在子组件中声明 props
    const props = defineProps({
        shu: String // 假设 shu 是字符串类型的数据
    });
</script>
```

​	
