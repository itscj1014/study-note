### 1. 基本概念

表单控件在实际开发中是非常常见的。特别是对于用户信息的提交，需要大量的表单。
Vue中使用v-model指令来实现表单元素和数据的双向绑定。

~~~javascript
<div id="app">
     输入：<input type="text" v-model="msg">

     <h2>{{msg}}</h2>
 </div>
 <script src="../js/vue.js"></script>

 <script>
     const app = new Vue({
         el:'#app',
         data:{
            msg:'hello world'
         },
         methods:{

         }
     })
 </script>
~~~



1.   当我们在输入框输入内容时，因为input中的v-model绑定了msg，所以会实时将输入的内容传递给msg，msg发生改变。
2. 当msg发生改变时，因为上面我们使用Mustache语法，将msg的值插入到DOM中，所以DOM会发生响应的改变。
3. 所以，通过v-model实现了双向的绑定



### 2. v-model原理

v-model 其实是一个语法糖，本质上包含了以下两个步骤：

- v-bind 绑定一个 value属性
- v-on 给当前元素绑定 input 事件

~~~javascript
<input type="text" v-model="msg">
等同于：
<input type="text" :value="msg" @input="msg=$event.target.value">
~~~



### 3. v-model:radio

~~~javascript
 <div id="app">
     <label for="male">
         <input type="radio" id="male" v-model="sex" value="男">男
     </label>
     <label for="female">
         <input type="radio" id="female"  v-model="sex" value="女">女
     </label>
 </div>
 <script src="../js/vue.js"></script>

 <script>
     const app = new Vue({
         el:'#app',
         data: {
             sex: '男',

         },
         methods:{

         }
     })
 </script>
~~~



###4. v-model:checkbox

复选框分为两种情况：单个勾选框和多个勾选框

- 单个勾选框
  - v-model即为布尔值
  - input中的value值并不影响v-model的值
- 多个勾选框
  - 当是多个复选框时，因为可以选中多个，所以对应的data中属性是一个数组。
  - 当选中某一个时，就会将input的value添加到数组中

~~~javascript
<div id="app">

     <p>单个勾选框</p>
     <label for="check">
         <input type="checkbox" id="check" v-model="checked">同意协议
     </label>
     <h2>{{checked}}</h2>

     <p>多个勾选框</p>
     <label><input type="checkbox" value="张三" v-model="users">张三</label>
     <label><input type="checkbox" value="李四" v-model="users">李四</label>
     <label><input type="checkbox" value="王五" v-model="users">王五</label>
     <label><input type="checkbox" value="赵六" v-model="users">赵六</label>

     <h2>选中的人有:{{users}}</h2>
 </div>
 <script src="../js/vue.js"></script>

 <script>
     const app = new Vue({
         el:'#app',
         data:{
            checked:false,
             users:[]
         },
         methods:{

         }
     })
 </script>
~~~





### 5. v-model:select

和checkbox一样，select也要分单选和多选两种情况。

- 单选
  - v-model绑定的是一个值
  - 当我们选择option中的一个时，会将当前选中的option的value中赋值到v-model绑定的参数
- 多选
  - v-model绑定的是一个数组
  - 我们可以选择多个option,并且会将选择的option中的value添加到v-model绑定的数组中

~~~javascript
<div id="app">
     <p>单选</p>
     <select name="user" id="user" v-model="selectUser">
         <option value="zhangsan">张三</option>
         <option value="lisi">李四</option>
         <option value="wangwu">王五</option>
         <option value="maliu">赵六</option>
     </select>

     <h2>{{selectUser}}</h2>

     <br>

     <p>多选</p>
     <select name="users" id="users" v-model="selectUsers" multiple>
         <option value="zhangsan">张三</option>
         <option value="lisi">李四</option>
         <option value="wangwu">王五</option>
         <option value="maliu">赵六</option>
     </select>

     <h2>{{selectUsers}}</h2>
 </div>
 <script src="../js/vue.js"></script>

 <script>
     const app = new Vue({
         el:'#app',
         data: {
             selectUser: '',
             selectUsers: []
         },
         methods:{

         }
     })
 </script>
~~~



### 6. 修饰符

#### 6.1 lazy修饰符

v-model默认情况下是在input事件中同步输入框数据的，但是lazy修饰符可以让数据在失去焦点或者回车时才会更新。

~~~javascript
<div id="app">
     请输入:<input type="text" v-model.lazy="message">

     <h2>数据是:{{message}}</h2>
 </div>
 <script src="../js/vue.js"></script>

 <script>
     const app = new Vue({
         el:'#app',
         data:{
            message:''
         },
         methods:{

         }
     })
 </script>
~~~



#### 6.2 number修饰符

默认情况下，在输入框中无论我们输入的是字母还是数字，都会被当做字符串类型进行处理。
但是如果我们希望处理的是数字类型，那么最好直接将内容当做数字处理。
number修饰符可以让在输入框中输入的内容自动转成数字类型

~~~javascript
 <div id="app">
     <input type="text" v-model.number="num">
     <h2>数字是:{{num}} 类型是:{{typeof num}}</h2>
 </div>
 <script src="../js/vue.js"></script>

 <script>
     const app = new Vue({
         el:'#app',
         data:{
             num: 0
         },
         methods:{

         }
     })
 </script>
~~~



#### 6.3 trim修饰符

如果输入的内容首尾有很多空格，通常我们希望将其去除
trim修饰符可以过滤内容左右两边的空格

~~~javascript
<div id="app">
     <input type="text" v-model="message">
     <h2>==={{message}}===</h2>
 </div>
 <script src="../js/vue.js"></script>

 <script>
     const app = new Vue({
         el:'#app',
         data:{
             message: ''
         },
         methods:{

         }
     })
 </script>
~~~







