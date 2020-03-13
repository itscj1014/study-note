## 1 编译的作用域

在学习插槽slot之前，我们先理解一个概念，编译的作用域。下面我们通过一个例子来说明：

我们定义一个组件，在组件里定义一个属性：isShow，对应的值为false,然后在Vue实例中也定义一个这样的属性，属性的值为true:

~~~javascript
<my-cpn v-show="isShow"></my-cpn>
~~~

最后我们看这行代码里面的内容能不能被渲染出来，代码如下:

~~~html
<div id="app">
    <my-cpn v-show="isShow"></my-cpn>
 </div>
 <script src="../js/vue.js"></script>
 <template id="cpn">
     <div>
         <h2>我是子组件内容</h2>
     </div>
 </template>

 <script>

     Vue.component('my-cpn',{
         template:'#cpn',
         data() {
             return{
                 isShow:false
             }
         }
     })

     const app = new Vue({
         el:'#app',
         data:{
             isShow: true
         }

     })
 </script>
~~~



最终结束是可以被渲染出来，也就是说使用的Vue实例中属性。

那是为什么呢？

官方给出了一条准则：父组件模板里的所有东西都会在父级作用域内编译，子组件模板里的所有内容都会在子级作用域内编译。而我们使用

~~~javascript
<my-cpn v-show="isShow"></my-cpn>
~~~

的时候，整个组件是在父组件中出现的，那么它的作用域就是父组件，使用的属性也是父组件中的属性。



## 2 插槽slot

### 2.1 定义

现实生活中，我们有很多地方用到插槽。例如我们电脑的usb插槽，电源的电线板插槽等等，它们的作用都是为了原有的设备有更多的扩展性。

那么组件中插槽也具有这样的功能：

它让我们封装的组件更加具有扩展性，可以让使用者决定内部该展示什么内容

### 2.2 封装思想

举个例子，移动网站的导航栏：

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200111173246.png)



像上面的导航栏，基本上每个页面都会有，但是又不完全一样，比如有的页面导航栏上左边是菜单，中间是搜索，右边是个人信息，但是我们在实际开发过程中，一般来说会把整个网站的导航栏封装成一个组件，但是因为每个页面的导航栏显示的内容和作用都不一样，那么这样的一个组件该怎么封装呢？

这里我们借用伟大的周总理提出的一个名词：求同存异。

简单来说，就是抽取共性，保留不同。

最好的封装方式就是将这些共性抽取到组件中，将不同暴露给插槽。

一旦我们预留了插槽，那么调用者就可以根据自己的需要来决定插槽中显示什么内容。

这也是我们学习插槽的重要原因。

### 2.3 slot基本使用

在子组件中，我们使用特殊的标签<slot> 来表示为当前组件开启一个插槽。

该插槽插入什么内容取决于父组件如何使用。

下面通过一个简单的例子，来给子组件定义一个插槽:

~~~html
<div id="app">

     <my-btn></my-btn>
    <my-btn>
        <h2>我是替换插槽里的内容</h2>
        <br>
        <span>我也是替换插槽里的内容</span>
    </my-btn>
 </div>
 <script src="../js/vue.js"></script>
<template id="tpn">
    <div>
        <slot>我是插槽里的内容</slot>
    </div>
</template>
 <script>

     Vue.component('my-btn',{
         template:'#tpn'
     })
     const app = new Vue({
         el:'#app',
     })
 </script>
~~~



显示结果如下：

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200111175415.png)

可以看出：<slot>中的内容表示，如果没有在父组件中插入任何其他内容，就默认显示该内容。如果在父组件中插入了内容，就显示父组件中插入的内容。这也是slot的基本使用。



### 2.4 具名插槽

当子组件的功能特别复杂时，可能并非只有一个插槽。

比如我们要封装一个导航栏的子组件，坑你就需要把导航栏分为左边、中间、右边，这样我们导航栏就需要定义三个插槽，那么我们在父组件中使用的时候如何区分这三个插槽从而插入对应的内容呢？

那这有个简单的方法：定义插槽的时候给插槽起一个名称，即定义一个**name属性**，这样的插槽就是**具名插槽**。

下面简单的演示一些具名插槽的使用:

~~~html
<div id="app">
     <h2>1.没有传入内容的情况</h2>
     <cpn></cpn>

     <h2>2.传入某一内容的情况</h2>
    <cpn>
        <p slot="center">搜索</p>
    </cpn>

     <h2>3.传入全部内容的情况</h2>
     <cpn>
         <button slot="left">返回</button>
         <input slot="center"></input>
         <h2 slot="right">我的</h2>
     </cpn>
 </div>
 <script src="../js/vue.js"></script>
<template id="cpn">
    //注意：模板中内容最好用div包裹作为root元素
    <div>
        <slot name="left">左边</slot>
        <slot name="center">中间</slot>
        <slot name="right">右边</slot>
    </div>
</template>
 <script>

     Vue.component('cpn',{
         template:'#cpn',
     })
     const app = new Vue({
         el:'#app',
     })
 </script>
~~~





### 2.5 作用域插槽

在没有说作用域插槽之前，我们先说一个需求：

在子组件中，我们有一组数据，但是这组数据需要以不同形式在多个页面展示，比如水平展示，列表展示等等。

对于这样一个**数据在子组件中，表现方式由父组件决定**的需求我们该怎么处理呢？

其实，这正是作用域插槽的作用：

父组件替换插槽的标签，但是内容由子组件提供。

对于实现这样的一个需求，我们先来看下子组件的写法：

~~~html
<template id="cpn">
    <div>
        <slot name="userSlot" :data="users"></slot>
    </div>
</template>

 Vue.component('cpn',{
         template:'#cpn',
         data() {
             return{
                 users: ['zhangsan', 'lisi', 'wangwu', 'mali']
             }
         }
     })
~~~



我们使用  **:data="传递的数据"** 这样的方式来将数据绑定到 slot上。

下面我们重点来看下父组件在使用我们子组件的时候是如何拿到我们slot上绑定的数据：

- 我们通过<template slot-scope="slotProps"></template>> 来获取到 **slotProps**属性
- 然后通过 slotProps.data就能获取到我们刚刚传入的data了。

具体代码如下：

~~~html
<div id="app">
    <cpn>
            <template slot-scope="slotProps" slot="userSlot">
                <ul>
                    <li v-for="user in slotProps.data">{{user}}</li>
                </ul>
            </template>
    </cpn>
     <cpn>
             <template slot-scope="slotProps" slot="userSlot">
                 <span v-for="user in slotProps.data">{{user}}</span>
             </template>

     </cpn>
 </div>

<script>
     const app = new Vue({
         el:'#app'
     })
 </script>
~~~



注意：其实我们在将数据绑定到slot上使用  **:data="传递的数据"** ，这里的绑定属性 :data 并不是一定要绑定 data，它可以是任意参数，比如上面例子中，我们改成  **:users="users"** ,到时候父组件使用 slotProps接受数据时，我们使用 **slotProps.users** 来接收即可，效果是一样的。