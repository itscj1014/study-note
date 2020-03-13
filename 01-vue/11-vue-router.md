# 1.路由

## 1.1 定义

**路由（Routing）:通过网络把源地址的信息传递的目的地址的活动**

这句话怎么理解呢？

举一个我们生活中的例子：路由器

路由器提供了两种机制：**路由和转发**

**路由**是决定数据包从来源到目的地的路径

**转发**是将数据从输入端转移到合适的输出端



## 1.2 前端路由规则

1. **URL的hash**

URL的hash，也就是锚点(#)，本质上是改变windows.location的href属性，我们可以直接通过location.hash来改变href，但是页面不刷新。

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200119222953.png)

	2. **HTML5的history模式**

a: pushState()

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200119223059.png)



b: relaceState()



c: go()



# 2. vue-router使用

vue-router是基于路由和组件的

路由用于设定访问路径，将路径和组件映射起来

在vue-router的单页面应用中，页面的路径的改变就是组件的切换



## 2.1 基础使用

安装vue-router

~~~shell
npm install vue-router --save
~~~



使用vue-router的步骤：

- 导入路由对象，并在Vue中注册路由（Vue.use(VueRouter)）

~~~js
import Vue from 'vue';
import VueRouter from "vue-router";

Vue.use(VueRouter);
~~~



- 创建路由实例，并且传入**路由映射配置**

~~~javascript
import Vue from 'vue';
import VueRouter from "vue-router";

import Home from "../components/Home";
import About from "../components/About";

Vue.use(VueRouter);

const routes = [
  {
    path:'/home',
    component: Home
  },
  {
    path: '/about',
    component: About
  }
]

const router = new VueRouter({
  routes
})

export default router;
~~~



- 在Vue实例中挂载创建的路由实例

~~~javascript
import Vue from 'vue'
import App from './App'
import router from "./router";

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  render: h => h(App)
});
~~~



- 使用路由：通过<router-link> 和 <router-view>

~~~javascript
  <div id="app">
    <h2>我是最上面</h2>
    <router-link to="/home">首页</router-link>
    <router-link to="/about">关于</router-link>
    <h2>我是最下面</h2>
    <router-view></router-view>
  </div>
~~~



效果：

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200119231030.png)



## 2.2 路由的默认路径

~~~javascript
const routes = [
  //将根路径重定向到/home路径下
  {
    path: '/',
    redirect: '/home'
  }
~~~



## 2.3 HTML5的history模式

默认情况下，路由的改变是使用URL的history模式，我们可以通过如下配置改为history模式。

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200119232545.png)



## 2.4 router-link的其他属性

-  to： 用于指定跳转的路径；
- tag：可以指定 `<router-link>` 之后渲染成什么组件, 比如上面的代码会被渲染成一个<button>元素, 而不是<a>；
- replace：replace不会留下history记录, 所以指定replace的情况下, 后退键返回不能返回到上一个页面中；
- active-class: 当<router-link>对应的路由匹配成功时, 会自动给当前元素设置一个router-link-active的class，设置active-class可以修改默认的名称

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200119233512.png)



## 2.5 路由代码跳转

有时候页面的跳转需要执行某些js代码，此时，就需要另外一种跳转方式了

~~~javascript
<template>
  <div id="app">
    <h2>我是最上面</h2>
    <button @click="linkToHome">首页</button>
    <button @click="linkToAbout">关于</button>
    <h2>我是最下面</h2>
    <router-view></router-view>
  </div>
</template>

<script>


export default {
  name: 'App',
  components: {

  },
  methods:{
    linkToHome() {
      this.$router.push('/home');
    },
    linkToAbout() {
      this.$router.push('/about');
    }
  }
}
~~~



## 2.6 动态路由

某些情况下，一个页面的路径可能是不确定的，比如我们进入用户界面是，希望的路径是：/user/123，/user/44

这种类型除了前面有固定的路径，后面还跟了参数，这种path和component的匹配关系，我们称之为动态路由。

路由配置：

~~~javascript
 {
    path:'/user/:id',
    component: User
  }
~~~



组件配置：

~~~javascript
<template>
    <div>
      <h2>{{$route.params.id}}</h2>
    </div>
</template>

<script>
    export default {
        name: "User"
    }
</script>

<style scoped>

</style>
~~~



使用：

~~~javascript
<router-link to="/user/123">用户</router-link>
<router-view></router-view>
~~~



# 3. 路由懒加载

