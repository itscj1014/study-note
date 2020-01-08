### 1. 简介

#### 1.1 什么是axios

axios是一个基于Promise的Http库，可以用在node.js环境和浏览器中

#### 1.2 功能特点

- 在浏览器中发送XMLHttpRequests
- 在node.js中发送Http请求
- 支持Promise
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换json数据
- 客户端支持防御XSRF

#### 1.3 安装

这里就简单的介绍一种安装方式:npm安装

~~~shell
npm install axios
~~~

还是其他两种方式：bower安装和cdn安装



### 2. axios的基本使用

#### 2.1 发送get 请求

~~~javascript
import axios from 'axios'

//第一种方式
axios.get("http://127.0.0.1:8000/home/data?type=sell&page=1")
    .then(res=>{console.log(res)
})
	.catch(err=>{
    console.log(err)
})

//第二种方式
axios.get("http://127.0.0.1:8000/home/data",{
          params:{
          type:'sell'
          page:1         
          }
          }).then(res=>{
              console.log(res.data)
          }).catch(err=>{
              console.log(err)
          })
~~~



#### 2.2 发送post请求

~~~javascript
import axios from 'axios'


axios.post("http://127.0.0.1:8000/home/data",{id:1,username:'zhangsan',age:18})
	.then(res=>{
    console.log(res)
})
	.catch(err=>{
    console.log(err)
})
~~~



#### 2.3 发送并发请求

有时候我们可能需要同时发送多个请求来获取结果，此时就可以使用axios.all([])，可以放入多个请求的数组

axios.all([])返回的是一个数组对象

~~~javascript
import axios from 'axios'

axios.all([axios.get("http://127.0.0.1:8000/home/multidata"),
    axios.get("http://127.0.0.1:8000/home/data",{
      params:{
        type: 'sell',
        page:1
      }
    })
    ]).then(res=>{
      console.log(res[0]);
      console.log("======================");
      console.log(res[1]);
    })
~~~



### 3. 默认设置

我们可以设置一些默认设置，这些设置将在以后每次的请求中生效。比如我们的请求路径、请求头参数等等

~~~javascript
	axios.defaults.baseURL = 'http://127.0.0.1:8100/test';
    axios.defaults.headers.contentType = "application/json;charset=UTF-8";
    axios.defaults.headers.common['Authorization'] = 'AUTH_TOKEN'
~~~



### 4. axios实例

#### 4.1 为什么要创建axios实例

当我们从axios模块中导入对象时，使用的实例时默认实例。当给该实例设置一些默认的配置时，这些配置就被固定下来了。但是，当后续开发中，某些配置可能不一样。比如，某些请求需要特定的baseURL或者请求头参数等，这个时候，我们就需要创建新的实例，并且传入属于该实例的配置信息。



#### 4.2 基本使用

~~~javascript
import axios from 'axios'

//创建axios实例
const axiosInstance = axios.create({
      baseURL:'http://127.0.0.1:8100',
      headers:{'Content-Type':'application/json'}
      timeout:5000
    })

//使用axios实例发送请求
axiosInstance.get('/data',{
      params:{
        type:'sell',
        page:2
      }
    })
~~~



#### 4.3 axios封装

我们可以在一个单独的js文件中，封装很多个axios实例，然后导出方便在项目开发中应对不同的需求而需要的不同axios实例。

![](.\assets\Snipaste_2020-01-06_01-43-54.png)



### 5. 拦截器

#### 5.1 使用

axios提供了拦截器，用于我们在发送每次请求或者得到响应后，进行对应的处理。

配置请求和响应拦截：

![](.\assets\Snipaste_2020-01-06_02-00-45.png)

~~~javascript
import axios from 'axios';

/**
 * 封装axios
 * @param config
 * @returns {AxiosPromise}
 */
export default function request(config) {

  const instance = axios.create({
    baseURL: 'http://127.0.0.1:8100/test',
    timeout: 5000
  });

  instance.interceptors.request.use(config=>{
    console.log('请求成功的拦截器中');
    return config
  },error => {
    console.log('请求错误的拦截器中');
    return error;
  })

  instance.interceptors.response.use(config=>{
    console.log('响应成功的拦截器中');
    return config;
  },error => {
    console.log('响应失败的拦截器中');
    return error;
  })

  return instance(config);

}

~~~

在其他组件中使用：

~~~javascript
import request from './network/request';

request({
     url:'/user',
     method:'post',
     data:{id:1,username:'lisi',age:19}
   }).then(res=>{
     console.log(res);
   }).catch(err=>{
     console.log(err);
   })
~~~



结果：

![](.\assets\35.png)



#### 5.2 作用

- 请求拦截中可以做的事
  - 当发送网络请求时，可以在页面中添加一个loading组件，作为动画
  - 某些请求要求用户登录，判断用户是否有token,没有token则跳转到登录页面
  - 对请求的参数进行序列化
- 响应拦截中可以做的事
  - 在成功的响应中可以对数据进行过滤
  - 在失败的响应中可以根据status判断报错的错误状态码来跳转到不同的错误提示页面