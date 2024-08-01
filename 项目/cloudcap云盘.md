# 	前端

### 1.项目的初始化

安装npm init vue@latest





```js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import App from './App.vue'
import router from './router'
import "@/assets/iconfont/iconfont.css"
import "@/assets/iconfont/iconfont"

const app = createApp(App)
app.use(ElementPlus)
app.use(createPinia())
app.use(router)
app.mount('#app')
```



### 2.请求拦截器配置

```js
//定制请求的实例
import router from "@/router";
import axios from "axios"
import { ElMessage  } from "element-plus";
const baseurl = "/api";
const instance = axios.create({ baseURL: baseurl });//创建axios对象

//添加响应拦截器
//这是一个在响应被then或catch处理之前处理响应的函数
instance.interceptors.response.use(
    result => {
         
        /*
            请求成功执行的代码
            如果返回的是200/201就说明后端返回的数据没有问题 接着往上面返回
        */
        if (result.data.code === 200 || result.data.code == 201) {
            return result.data;
        }
         
        /*
        	如果代码返回的不是200/201
        	说明在后台拿数据时出现了错误，通常是数据不符合导致的
        */
        ElMessage({
            message: result.data.msg,
            type: 'warning',
        })
        //返回错误的Promise,在进行链式操作中.catch的回调函数就是返回的这个数据
        return Promise.reject(result.data);
    },
    err => {
        //请求失败执行的代码
        if (err.response.status === 401) {
            ElMessage({
                message: "请先登录！！",
                type: 'success',
              })
            router.push("/login")
        } else {
            alert("出现异常")
        }
        return Promise.reject(err);//异步转化为失败
    }
)

/*
	添加请求拦截器
	在发送请求之前做些什么
*/
instance.interceptors.request.use(
    config => {
    // 
    const jwt = localStorage.getItem('jwt');
        //有令牌，添加请求头上
        if (jwt != null) {
            config.headers.Authorization = jwt;
        }
        return config;
    },
    error => {
        // 对请求错误做些什么
        return Promise.reject(error);
    }
);

export default instance
```





### 3.路由配置

```js
//导入路由
import { createRouter, createWebHistory } from "vue-router";

//导入组件
import loginVue from '@/views/login.vue';
import frameworkVue from '@/views/framework.vue';
import mainVue from '@/views/menu/main.vue';
import recyclebinVue from '@/views/menu/recyclebin.vue';
import shareVue from '@/views/menu/share.vue';
import sharePageVue from '@/views/menu/sharePage.vue';
import uploadVue from '@/views/menu/upload.vue';
import finishVue from '@/views/menu/finish.vue';
import notfountVue from '@/views/codeVue/notfount.vue';

//定义关系
const routes = [
  { path: '/login', component: loginVue },
  {
    //redirect 如果用户访问的是/，默认跳转/main/all
    path: '/',component: frameworkVue,redirect: "/main/all",meta: {title: "首页"},
    children: [
      { path: '/main/:type', component: mainVue, props: true },
      { path: '/recyclebin', component: recyclebinVue },
      { path: '/main/folder/:folderName', component: mainVue, props: true },
      { path: '/share', component: shareVue },
      { path: '/tranmission/upload', component: uploadVue },
      { path: '/tranmission/finish', component: finishVue },
    ]
  },

  { path: '/sharePage/:url', component: sharePageVue },
  { path: '/404', component: notfountVue },
];

//创建路由器
const router = createRouter({
  history: createWebHistory(),
  routes: routes
});

//导出路由
export default router;
```





### 4.跨域的处理

跨域就是**不同协议**、**不同域名**、**不同端口**发出的**ajax请求**会失败！！！【在vite.config.js中修改】

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





### 5.qs转换路径

安装qs

```js
npm install qs
```

qs就是把js对象装换为路径参数的一个工具

```js
import qs from 'qs'; //在用的的页面上方引入
```

**示例**

```js
const fromData = ref({
  phone: "",
  password: "",
});

let result = await login(fromData.value);

export const login = (fromData) => {
    return request.post("/user/login", qs.stringify(fromData));
}

```



### 6.iconfont矢量图

```js
//在main.js中引入样式
import "@/assets/iconfont/iconfont.css"
import "@/assets/iconfont/iconfont"
```

https://www.iconfont.cn/?spm=a313x.search_index.i3.2.4f863a81dhE331





### 7.element-plus的使用

**安装**

```
npm install element-plus --save
```

**main.js中引入**

```js
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import App from './App.vue'

const app = createApp(App)
app.use(ElementPlus)
app.mount('#app')
```



### 8.菜单操作

循环进行遍历

```vue
	<div class="none">
        <div
          @click="zimenu(item),updateIndex1(index)"
          v-for="(item, index) in menus"
          :key="index"
          :class="{ 'menu-backgroup-one': index === index1 }"
        >
          <span :class="['iconfont icon-'] + item.icon"></span>
          <div>
            {{ item.name }}
          </div>
        </div>
      </div>
```



定义的一级菜单数据

```js
const menus = [
  {
    icon: "yunpan",
    name: "首页",
    allshow: true,
    child: [
      {
        name: "全部",
        icon: "yunpan",
        url: "/main/all",
      },
      {
        name: "视频",
        icon: "shipin",
        url: "/main/shipin",
      },
      {
        name: "音频",
        icon: "yinpin1",
        url: "/main/yinpin",
      },
      {
        name: "图片",
        icon: "tupian",
        url: "/main/tupian1",
      },
      {
        name: "文档",
        icon: "wendang",
        url: "/main/wendang",
      },
      {
        name: "压缩",
        icon: "yasuobao",
        url: "/main/yasuobao",
      },
      {
        name: "其他",
        icon: "qitafuwu",
        url: "/main/weizhi",
      },
    ],
  },
  {
    icon: "chuanshu",
    name: "传输",
    allshow: true,
    child: [
      {
        name: "完成",
        icon: "wancheng",
        url: "/tranmission/finish",
      },
    ],
  },
  {
    icon: "fenxiang",
    name: "分享",
    allshow: true,
    child: [
      {
        name: "分享",
        icon: "fenxiang",
        url: "/share",
      },
    ],
  },
  {
    icon: "huishouzhan",
    name: "回收站",
    allshow: true,
    child: [
      {
        name: "回收站",
        icon: "huishouzhan",
        url: "/recyclebin",
      },
    ],
  },
];
```



进行路由的跳转

```js
const zimenu = (item) => {
  //获取点击的一级菜单，方便展示二级菜单的数据
  menu.value = item.child;

  //点击一级菜单时，修改二级菜单的路由
  console.log(menu.value);
  Router.push(menu.value[0].url);
};
```



### 9.动态的设置样式

```js
//menu-backgroup-two 条件达成后类名
:class="{ 'menu-backgroup-two': index === index2 }"

//动态的拼接
<span :class="'iconfont icon-' + item.icon" />
```



### 10.H5拖拽事件

```js
//钩子函数
onMounted(() => {
  // 处理拖放文件操作
  var dropZone = document.getElementById("dropZone");

  //拖拽到上方触发
  dropZone.addEventListener("dragenter", function (event) {
    event.stopPropagation();
    event.preventDefault();
    dropZone.style.padding = "0px";
    dropZone.style.border = "5px dashed #e9edee";
  });

  //拖拽到上方连续触发
  dropZone.addEventListener("dragover", function (event) {
    event.preventDefault();
  });

  //离开上方触发
  dropZone.addEventListener("dragleave", function (event) {
    event.preventDefault();
    event.stopPropagation();
    if (!dropZone.contains(event.relatedTarget)) {
      // 只有当鼠标完全离开dropZone及其子元素时，才会修改样式
      dropZone.style.border = "0px solid #e9edee";
      dropZone.style.padding = "5px";
    }
  });

  //放下文件触发
  dropZone.addEventListener("drop", function (event) {
    event.preventDefault(); // 阻止默认行为，否则浏览器将直接打开或下载文件
    var files = event.dataTransfer.files; // 获取拖放的文件列表
    uploadLength.value = uploadLength.value+files.length;
    // 这里可以处理一个或多个文件，如读取文件内容或上传文件
    for (var i = 0; i < files.length; i++) {
      addFile(files[i], 1);
    }

    dropZone.style.border = "0px solid #e9edee";
    dropZone.style.padding = "5px";
  });
});
```





### 11.哈希算法md5

哈希(hash)或者说散列，它是一种基础数据结构。

哈希算法是一种加密算法，通常用于密码加密，检验下载的文件是否正确

常见的哈希算法包括**MD5**、**SHA-1**、**SHA-256**等

**特点**：不可逆，唯一

```js
//计算md5
const CHUNK_SIZE = 5 * 1024 * 1024; // 定义每个分片的大小
const computeMd5 = (fromDataItem) => {
  let file = fromDataItem.file;

  let chunks = Math.ceil(file.size / CHUNK_SIZE);
  let currentChunk = 0;
  let spark = new SparkMD5.ArrayBuffer();
  let fileReader = new FileReader();

  function loadNext() {
    let start = currentChunk * CHUNK_SIZE;
    let end = start + CHUNK_SIZE >= file.size ? file.size : start + CHUNK_SIZE;
    fileReader.readAsArrayBuffer(file.slice(start, end));
  }

  loadNext(); // 开始读取第一个文件块
  return new Promise((resolve, reject) => {
    let FileByUid = getFileByUid(fromDataItem.uid);
    fileReader.onload = function (e) {
      spark.append(e.target.result); // 向 MD5 添加当前块的内容
      currentChunk++;
      if (currentChunk < chunks) {
        // 更新 MD5 进度
        let percent = Math.floor((currentChunk / chunks) * 100);
        FileByUid.md5Progress = percent;
        // 如果还有下一个块，继续读取下一个块
        loadNext();
      } else {
        // 文件读取完成
        let md5 = spark.end(); // 计算所有块的 MD5
        spark.destroy();
        FileByUid.state = status.uploading;
        FileByUid.md5Progress = 100;
        FileByUid.md5 = md5;
        resolve(FileByUid.uid);
      }
    };

    fileReader.onerror = function () {
      FileByUid.md5Progress = 0;
      FileByUid.state = status.init;
      alert("暂不支持上传文件夹！");
      console.warn("文件读取错误！");
      reject(new Error("文件读取错误！")); // 在出现错误时拒绝这个Promise
    };
  });
};
```





### 12.分片上传、秒传、断点续传

```js
//进行上传文件
const uploadFile = async (uid) => {
  const FileItem = getFileByUid(uid);
  // 分片大小设定，例如5MB一片
  const chunkCount = Math.ceil(FileItem.file.size / CHUNK_SIZE);
  // 总字符
  var totalLoadedBytes =0;
  //存放在本地的md5数值   
  const temporaryMd5 = JSON.parse(localStorage.getItem(FileItem.md5)) || { count: 0 };
  //存放在本地的上传字节
  const temporaryTotalSize = JSON.parse(localStorage.getItem(FileItem.md5 + "_totalSize"));
  
  //如果本地中有上传字节说明用户已经上传过一部分的数据了
  if(temporaryTotalSize){
    FileItem.fileSize = autoConvertMemory(FileItem.file.size- temporaryTotalSize) 
  }

  for (let count = temporaryMd5.count ; count < chunkCount; count++) {
    // 保存分片个数
    temporaryMd5.count = count + 1;
    localStorage.setItem(FileItem.md5, JSON.stringify(temporaryMd5));
   
    const chunk = FileItem.file.slice(count * CHUNK_SIZE, (count + 1) * CHUNK_SIZE);
    const formData = new FormData();
    formData.append("file", chunk);
    formData.append("chunkNumber", count + 1);
    formData.append("identifier", FileItem.md5);
    formData.append("fileName", FileItem.fileName);
    
    if (currentFolder.value.id != null && currentFolder.value.id != "") {
      formData.append("folderId", currentFolder.value.id);
    } else {
      formData.append("folderId", 0);
    }

    // 这里使用 Axios 发送 formData
    const uploadResult = await request.post("/file/addFile", formData, {
      headers: {
        "Content-Type": "multipart/form-data",
      },
      onUploadProgress: function (progressEvent) {
        // 已上传的数据量（KB）
        totalLoadedBytes += progressEvent.loaded;
        if (FileItem.file.size < totalLoadedBytes) {
          FileItem.uploadSize = autoConvertMemory(FileItem.file.size);
        } else {
          FileItem.uploadSize = autoConvertMemory(totalLoadedBytes);
        }

        // 计算当前分片的进度
        const chunkProgress =(progressEvent.loaded / progressEvent.total) * 100;

        // 计算总进度
        FileItem.uploadProgress = Math.ceil(
          ((count + chunkProgress / 100) / chunkCount) * 100
        );
        
         // 保存总大小
        localStorage.setItem(FileItem.md5 + "_totalSize", totalLoadedBytes);
      },
    });

    if (uploadResult.msg == "进行秒传操作") {
      length.value++;
      FileItem.uploadProgress = 100;
      FileItem.uploadSize = FileItem.fileSize;
      FileItem.state = status.upload_finish;
	 //上传成功删除数据
      localStorage.removeItem(FileItem.md5);
      localStorage.removeItem(FileItem.md5 + "_totalSize");
		
      if(length.value == uploadLength.value){
      if(!currentFileId.value){
        ElMessage({
          message: "全部文件上传完成！",
          type: 'success',
        }) 
        getfile1(1)
      }
    }
      return;
    }
  }

  // 所有分片上传完成后，请求合并分片
  const formData1 = new FormData();
  formData1.append("fileName", FileItem.fileName);
  formData1.append("identifier", FileItem.md5);
  formData1.append("chunkCount", chunkCount);
  formData1.append("fileSize", FileItem.fileSize);

  if (currentFolder.value.id != null && currentFolder.value.id != "") {
    formData1.append("folderId", currentFolder.value.id);
  } else {
    formData1.append("folderId", 0);
  }
   mergeChunksApi(formData1).then(result=>{
    length.value++;

    localStorage.removeItem(FileItem.md5);
    localStorage.removeItem(FileItem.md5 + "_totalSize");

    ElMessage({
        message: "文件"+FileItem.fileName+"上传完成！",
        type: 'success',
    })     

    FileItem.state = status.upload_finish;
    if(length.value == uploadLength.value){
      if(!currentFileId.value){
        ElMessage({
          message: "全部文件上传完成！",
          type: 'success',
        }) 
        getfile1(1)
      }
    }
   }).catch(err=>{
    console.log(err);
   })
};

```





### 13.响应式数组操作

##### 在数组第一位添加数据

```js
tableData.value.unshift({
    fileName: "",
    fileId: "",
    userId: "",
    fileType: "weibiaoti-_huabanfuben",
    fileSize: "",
    uploadAt: "",
    filePath: "",
    isNotOne: true,
  });

```



##### 根据uid查询数据

```java
const getFileByUid = (uid) => {
  let file = tableData.value.find((item) => {
    return item && item.file && item.uid == uid;
  });
  return file;
};
```



##### 根据uid查询下标

```js
const index = this.items.findIndex(item => item.id === idToFind);
```





### 14.computed的使用

```js
// 使用计算属性来根据 radio1 的值动态返回对应的数据数组
// 它的工作如下：
// 当 radio1.value 等于 '1' 时，计算属性 currentData 会返回 tableDate.value.one 的值。
// 当 radio1.value 等于 '2' 时，计算属性 currentData 会返回 tableDate.value.two 的值。
// 当 radio1.value 等于 '3' 时，计算属性 currentData 会返回 tableDate.value.three 的值。
// 如果 radio1.value 不是 '1'、'2' 或 '3'，计算属性 currentData 会返回一个空数组 []。
const currentData = computed(() => {
  switch (radio1.value) {
    case '1':
      return tableDate.value.one
    case '2':
      return tableDate.value.two
    case '3':
      return tableDate.value.three
    default:
      return []
  }
})
```



### 15.element引入

1.安装 npm install element-plus --save

2.在main.js添加

```js
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'

app.use(ElementPlus)
```



### 16.路由的跳转【js的方式】

```js
import router from "@/router/index";
router.push("show")
```











# 后端

### 1.maven依赖

```xml
	<dependencies>
        <!--第三方库-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        
        <!--生成get set-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        
        <!--web开发-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--数据库驱动-->
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <version>8.0.33</version>
        </dependency>

        <!--分页-->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.4.6</version>
        </dependency>

        <!--验证-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>

        <!--JSON处理-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>
        
        <!--测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        
        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>3.0.3</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>

        <!--日志实现-->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>


        <!--validation验证依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.72</version>
        </dependency>

        <!--JWT令牌依赖-->
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>4.4.0</version>
        </dependency>

        <dependency>
            <groupId>io.minio</groupId>
            <artifactId>minio</artifactId>
            <version>8.5.8</version>
        </dependency>
    </dependencies>
```





### 2.md5加密工具类

```java
package com.example.cloudcap_api.utils;


import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class Md5Util {
    /**
     * 默认的密码字符串组合，用来将字节转换成 16 进制表示的字符,apache校验下载的文件的正确性用的就是默认的这个组合
     */
    protected static char hexDigits[] = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};

    protected static MessageDigest messagedigest = null;

    static {
        try {
            messagedigest = MessageDigest.getInstance("MD5");
        } catch (NoSuchAlgorithmException nsaex) {
            System.err.println(Md5Util.class.getName() + "初始化失败，MessageDigest不支持MD5Util。");
            nsaex.printStackTrace();
        }
    }

    /**
     * 生成字符串的md5校验值
     *
     * @param s
     * @return
     */
    public static String getMD5String(String s) {
        return getMD5String(s.getBytes());
    }

    /**
     * 判断字符串的md5校验码是否与一个已知的md5码相匹配
     *
     * @param password  要校验的字符串
     * @param md5PwdStr 已知的md5校验码
     * @return
     */
    public static boolean checkPassword(String password, String md5PwdStr) {
        String s = getMD5String(password);
        return s.equals(md5PwdStr);
    }


    public static String getMD5String(byte[] bytes) {
        messagedigest.update(bytes);
        return bufferToHex(messagedigest.digest());
    }

    private static String bufferToHex(byte bytes[]) {
        return bufferToHex(bytes, 0, bytes.length);
    }

    private static String bufferToHex(byte bytes[], int m, int n) {
        StringBuffer stringbuffer = new StringBuffer(2 * n);
        int k = m + n;
        for (int l = m; l < k; l++) {
            appendHexPair(bytes[l], stringbuffer);
        }
        return stringbuffer.toString();
    }

    private static void appendHexPair(byte bt, StringBuffer stringbuffer) {
        char c0 = hexDigits[(bt & 0xf0) >> 4];// 取字节中高 4 位的数字转换, >>>
        // 为逻辑右移，将符号位一起右移,此处未发现两种符号有何不同
        char c1 = hexDigits[bt & 0xf];// 取字节中低 4 位的数字转换
        stringbuffer.append(c0);
        stringbuffer.append(c1);
    }
}
```



### 3.ThreadLocal工具类

```java
package com.example.cloudcap_api.utils;

import java.util.HashMap;
import java.util.Map;

/**
 * ThreadLocal 工具类
 */
@SuppressWarnings("all")
public class ThreadLocalUtil {
    //提供ThreadLocal对象,
    private static final ThreadLocal THREAD_LOCAL = new ThreadLocal();

    //根据键获取值
    public static <T> T get(){
        return (T) THREAD_LOCAL.get();
    }

    //存储键值对
    public static void set(Object value){
        THREAD_LOCAL.set(value);
    }

    //清除ThreadLocal 防止内存泄漏
    public static void remove(){
        THREAD_LOCAL.remove();
    }
}
```



### 4.jwt令牌工具类

用户在登录成功后使用map存放数据，genToken方法生成令牌

```java
package com.example.cloudcap_api.utils;

import com.auth0.jwt.JWT;
import com.auth0.jwt.algorithms.Algorithm;

import java.util.Date;
import java.util.Map;

public class JwtUtil {
    private static final String KEY = "qingniao";
	
	//接收业务数据,生成token并返回
    public static String genToken(Map<String, Object> claims) {
        return JWT.create()
                .withClaim("claims", claims)
                .withExpiresAt(new Date(System.currentTimeMillis() + 1000 * 60 * 60 * 12))
                .sign(Algorithm.HMAC256(KEY));
    }

	//接收token,验证token,并返回业务数据
    public static Map<String, Object> parseToken(String token) {
        return JWT.require(Algorithm.HMAC256(KEY))
                .build()
                .verify(token)
                .getClaim("claims")
                .asMap();
    }
}
```



### 5.拦截器

```java
package com.example.cloudcap_api.config;

import com.example.cloudcap_api.Interceptor.LoginInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Autowired
    private LoginInterceptor loginInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //登录和注册不拦截
        registry.addInterceptor(loginInterceptor).excludePathPatterns("/user/login","/user/register");
    }
}

```



```java
package com.example.cloudcap_api.Interceptor;

import com.example.cloudcap_api.utils.JwtUtil;
import com.example.cloudcap_api.utils.ThreadLocalUtil;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

import java.util.Map;

@Component
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //获取令牌
        String authorization = request.getHeader("Authorization");

        //验证令牌
        try {
            Map<String, Object> stringObjectMap = JwtUtil.parseToken(authorization);
            ThreadLocalUtil.set(stringObjectMap);
            return true;
        } catch (Exception e) {
            //设置状态码
            e.printStackTrace();
            response.setStatus(401);
            return false;
        }
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        ThreadLocalUtil.remove();
    }
}

```





### 6.minio使用

在yml中配置

```yml
# 配置minio文件上传的几个核心参数
minio:
  endpoint: http://localhost:9000  #配置访问路径
  accessKey: minioadmin  #账号
  secretKey: minioadmin  #密码
```



编写配置类

```java
@Configuration
public class MinioConfig {
    @Value("${minio.endpoint}")
    private String minioEndpoint;

    @Value("${minio.accessKey}")
    private String minioAccessKey;

    @Value("${minio.secretKey}")
    private String minioSecretKey;

    @Bean
    public MinioClient minioClient() {
        return MinioClient.builder()
                .endpoint(minioEndpoint)
                .credentials(minioAccessKey, minioSecretKey)
                .build();
    }
}
```



使用自动注入

```java
@Autowired
private MinioClient minioClient;
```



### 7.全局异常处理器

```java
package com.example.cloudcap_api.exception;

import com.example.cloudcap_api.utils.Result;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalException {
    @ExceptionHandler(Exception.class)
    public Result exception(Exception exception){
        exception.printStackTrace();
        return new Result(200,"有异常",exception.getMessage());
    }
}
```





### 8.枚举的使用

```java
public enum DaysOfWeek {
    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY,
    SUNDAY;
}
```



### 9.jwt令牌验证【思路】

```asciiarmor
用户登录成功后生成jwt并且返回出去存入本地
前端使用拦截器在发送请求时给权限添加上jwt
后端拦截器在接受请求前解析jwt使用异常的方式
如果没有出错就说明前端是有jwt如果没有就说明没用登录
如果有jwt就把jwt的数据存入到本地线程ThreadLocal中去以便后期操作的应用

如果解析为空就在catch中设置响应的状态response.setStatus(401)
如果后端出错前端拦截器就会进入到error的回调方法中
在error的回调方法中可以判断响应的状态是否是401如果是401就是
```



