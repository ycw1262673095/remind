# remind
笔记

后端接口 500 503 (报错)
# 1.闭包

原理：函数嵌套函数，内部函数是闭包   内部函数使用了外部函数的变量
特性：闭包被调用的时候,外部函数中的变量不会被销毁的   用来保护变量不被污染
使用：封装代码，使用闭包将他们封装在一个模块里面
 
缺点：性能问题容易造成内存泄漏  
      每写一个闭包就会有一个全局变量清理不掉，内存一直被占用，没法释放，导致内存泄漏(内存被占满了，性能问题)



function add(){

    var a=1;
    function fn(){
        return a++;
    }
    return fn  //返回的是函数不是结果
}
var c= add();
console.log(c())


函数作用域在函数创建的时候才创建  ， 调用结束就销毁
就比如a变量是局部变量，在使用函数完毕后a就会销毁
但是如果 在全局变量定义了一个变量c去使用函数add    a就不会被删除  原因是被全局变量使用的函数不会被销毁


# 2.原型

就是一个prototype对象

我们创建对象都有自己的原型，还有一些第三方库创建的，或者内置对象都有原型， 这些对象可以使用原型上面的属性和方法

利用原型添加对象的功能


# 3.js数组去重

es5写法   foreach indexof判断是否为-1   for搭配indexof

es6写法  new set()  使用方法如下   Array.from(new set(arr))  还可以用户计算并集 交集 差集 字符串去重

//数组去重  

let arr = [3, 5, 2, 2, 5, 5];
let setArr = new Set(arr)     // 返回set数据结构  Set(3) {3, 5, 2}  

//方法一   es6的...解构
let unique1 =  [...setArr ];      //去重转数组后  [3,5,2]

//方法二  Array.from()解析类数组为数组
let unique2 = Array.from(setArr )   //去重转数组后  [3,5,2]

//字符串去重

let str = "352255";
let unique = [...new Set(str)].join("");     // 352 


//实现并集  交集  差集

let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);

// 并集
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}

// （a 相对于 b 的）差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}

# 同步与异步问题
同步

同步是需要等第一个程序运行完毕才会接着运行下一个程序  需要按顺序来   优点，不会导致紊乱  缺点，运行速度不如异步

异步

优点:异步是接取一个任务，直接给后台，在接下一个任务，一直一直这样，谁的先读取完先执行谁的(总的来说就是接取任务给后台，谁先读取执行谁)， 缺点：没有顺序 ，谁先读取完先执行谁的 ，会出现上面的代码还没出来下面的就已经出来了，会报错；

# 5.异步编程方式 6种
 
//1.回调函数

setTimeout(function callback(){
    console.log('2秒后触发')
},2000)

//2.事件监听  比如click事件

另一种思路是采用事件驱动模式。任务的执行不取决于代码的顺序，而取决于某个事件是否发生。

还是以f1和f2为例。首先，为f1绑定一个事件（这里采用的jQuery的写法）。

f1.on('done', f2);

上面这行代码的意思是，当f1发生done事件，就执行f2。然后，对f1进行改写：

　　function f1(){

　　　　setTimeout(function () {

　　　　　　// f1的任务代码

　　　　　　f1.trigger('done');

　　　　}, 1000);

　　}

f1.trigger('done')表示，执行完成后，立即触发done事件，从而开始执行f2。这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以"去耦合"（Decoupling），有利于实现模块化。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。

//3.发布订阅

上一节的"事件"，完全可以理解成"信号"。我们假定，存在一个"信号中心"，某个任务执行完成，就向信号中心"发布"（publish）一个信号，其他任务可以向信号中心"订阅"（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做"发布/订阅模式"（publish-subscribe pattern），又称"观察者模式"（observer pattern）。这个模式有多种实现，下面采用的是Ben Alman的Tiny Pub/Sub，这是jQuery的一个插件。首先，f2向"信号中心"jQuery订阅"done"信号。


　　jQuery.subscribe("done", f2);

　　function f1(){

　　　　setTimeout(function () {

　　　　　　// f1的任务代码

　　　　　　jQuery.publish("done");

　　　　}, 1000);

　　}

jQuery.publish("done")的意思是，f1执行完成后，向"信号中心"jQuery发布"done"信号，从而引发f2的执行。

此外，f2完成执行后，也可以取消订阅（unsubscribe）。

jQuery.unsubscribe("done", f2);

这种方法的性质与"事件监听"类似，但是明显优于后者。因为我们可以通过查看"消息中心"，了解存在多少信号、每个信号有多少订阅者，从而监控程序的运行


在vue中
定义一个事件发布机制类
具有$emit方法用于发布事件，会执行。
具有$on方法用于订阅事件，可以多次订阅相同事件，订阅时不会执行方法

//注册
this.$emit('mounted',{})

//触发
this.$on('mounted',function(){

})

   // 注册事件
        $emit(eventType) {
					// 为了简便，不考虑参数的情况
          if (this.subs[eventType]) {
            this.subs[eventType].forEach((item) => {
              item();
            });
          }
        }
        // 触发事件
        $on(eventType, handler) {
          this.subs[eventType] = this.subs[eventType] || [];
          this.subs[eventType].push(handler);
        }
      }



————————————————
版权声明：本文为CSDN博主「晓萌的百宝袋」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/xiaozidewawa/article/details/108381583
作者：可乐要加冰o
链接：https://juejin.cn/post/6844903776780828685
来源：稀土掘金

//4.Promise

const p = new Promise((resolve,reject)=>{
    setTimeout(function callback(){
        console.log('2秒后触发')
    },2000)
})
p.then(param=>{
    console.log(param)
})


//5.Generators / yield  把代码精确的控制到每一个步骤

//6.主流  Async/Await

async function getData(){
    const result = await new Promise((resolve,reject)=>{
        setTimeout(function callback(){
            resolve('this is data')
        },2000)
    })

    console.log(result)
}

ES7提供了async函数，使得异步操作变得更加方便。async函数是什么？一句话，async函数就是Generator函数的语法糖。


var fs = require('fs');

var readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) reject(error);
      resolve(data);
    });
  });
};

var gen = function* (){
  var f1 = yield readFile('/etc/fstab');
  var f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};

写成async函数，就是下面这样。

var asyncReadFile = async function (){
  var f1 = await readFile('/etc/fstab');
  var f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};

一比较就会发现，async函数就是将Generator函数的星号（*）替换成async，将yield替换成await，仅此而已。

async函数对 Generator 函数的改进，体现在以下四点

（1）内置执行器。Generator函数的执行必须靠执行器，所以才有了co模块，而async函数自带执行器。也就是说，async函数的执行，与普通函数一模一样，只要一行。

（2）更好的语义。async和await，比起星号和yield，语义更清楚了。async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果。

（3）更广的适用性。 co模块约定，yield命令后面只能是Thunk函数或Promise对象，而async函数的await命令后面，可以是Promise对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。

（4）返回值是Promise。async函数的返回值是Promise对象，这比Generator函数的返回值是Iterator对象方便多了。你可以用then方法指定下一步的操作。

进一步说，async函数完全可以看作多个异步操作，包装成的一个Promise对象，而await命令就是内部then命令的语法糖。



作者：可乐要加冰o
链接：https://juejin.cn/post/6844903776780828685
来源：稀土掘金


# 6.vue2实现数据懒加载 


# 7.js  es6中的判断是否是数组  

Array.isArray(value)

# 8.深度克隆 数组

function deepClone(value){
    //非原始值的情况
    if(Array.isArray(value)){
       var clone=[];
       for(var i=0;i < value.length;i++){
           clone[i]=deepClone(value[i])
       }
       return clone
    }
    if(typeof value === 'object' && value!==null){ // null 的时候也会被认为是个对象
        var clone={};
        for(var key in value){
            clone[key] = deepClone(value[key]);
        }
        return clone
    }
    return value
}

console.log(deepClone(1))


# 9 重绘 和回流
重绘
几何属性发生改变的时候，页面需要重新建立    边距什么的     消耗的性能比较多
  
回流

几何属性不发生变化   页面不需要整个重新建立  不影响布局  只有重新渲染


# 10  路由守卫和token令牌   Chookie

token有效期  2小时  

router.beforeEach((to,from,next)=>{}


to: 即将要进入的目标 用一种标准化的方式
from: 当前导航正要离开的路由 用一种标准化的方式

next()  继续往下执行  next({name:'xx'})   指的是前往xx的路由

router.beforeEach((to,from,next)=>{
  store.commit('getToken')
  const token =store.state.user.token
  if(!token && to.name !== 'login'){
    next({name:'login'})
  }else if(token && to.name === 'login'){
      next({name:'home'})
  }else{
    next()
  }
})
//通过Chookie 来获取修改 token   项目中的token是用mock模拟的
import Cookie from 'js-cookie'
export default{
    state:{
        token:''
    },
    mutations:{
        setToken(state,val){
          state.token = val
          Cookie.set('token',val)
        },
        clearToken(state){
            state.token=''
            Cookie.remove('token')
        },
        getToken(state){
           state.token = state.token || Cookie.get('token')
        }
    }
}



# 11  this.$set用法 触发更新视图 用于更改添加数据  主要记住三个参数  因为 Vue 无法探测普通的新增属性 (比如 this.myObject.newProperty = 'hello，ningzaichun'）  普通新增控制台可以打印  如果视图没有显示则需要用到set

调用方法：this.$set( target, key, value )

target：要更改的数据源(可以是对象或者数组)

key：要更改的具体数据

value ：重新赋的值

例子
<template>
    <div id="app2">
        <p v-for="item in items" :key="item.id">{{item.message}}</p>
        <button class="btn" @click="handClick()">更改数据</button>    
    </div>
</template>

<script>
export default {
    data() {
        return {
            items: [
                { message: "one", id: "1" },
                { message: "two", id: "2" },
                { message: "three", id: "3" }
            ]
        };
    },
    mounted(){
        //此时对象的值更改了，但是视图没有更新
        this.items[0]={message:"测试",id:"4"};  
        // 您还可以使用 vm.$set实例方法，这也是全局 Vue.set方法的别名
        this.$set(this.items,0,{message:"测试",id:"4"})； //$set可以触发更新视图
        // 通过Vue.set方法
        Vue.set(this.items,0,{message:"测试",id:"4"})； //$set可以触发更新视图
        console.log(this.items)
    },
    methods: {
        // 调用方法：Vue.set( target, key, value )

        // target：要更改的数据源(可以是对象或者数组)

        // key：要更改的具体数据

        // value ：重新赋的值
        handClick() {
            //Vue methods中的this 指向的是Vue的实例，这里可以直接在this中找到items
            this.$set(this.items, 0, { message: "更改one的值", id: "0" });
        },
    }
};
</script>

<style>
</style>



# 12  webpack
一、Webpack是什么、为什么要使用它

简单来说，Webpack是一个打包工具。

站在2018年的角度，成为一个优秀的前端工程师，除了要会写页面样式和动态效果之外，还需要会用主流的单页面框架、Node.js、简单的前端的性能优化等等。加上现在一部分服务器的逻辑移到了前端上，所以实际上前端的复杂度也是提升了很多。

而Webpack可以帮助我们完成一些任务。比如js压缩、css压缩、编译模板文件等等，从而减少前端的工作量。当然，Webpack功能很强大，能帮我们完成的工作远远不止这些。

下面，让我们慢慢了解Webpack吧~

# 13  1rem、1em、1vh、1px各自代表的含义

rem

rem是全部的长度都相对于根元素

元素。通常做法是给html元素设置一个字体大小，然后其他元素的长度单位就为rem。

em

子元素字体大小的em是相对于父元素字体大小
元素的width/height/padding/margin用em的话是相对于该元素的font-size
vw/vh

全称是 Viewport Width 和 Viewport Height，视窗的宽度和高度，相当于 屏幕宽度和高度的 1%，不过，处理宽度的时候%单位更合适，处理高度的 话 vh 单位更好。

px

px像素（Pixel）。相对长度单位。像素px是相对于显示器屏幕分辨率而言的。

一般电脑的分辨率有{1920*1024}等不同的分辨率

1920*1024 前者是屏幕宽度总共有1920个像素,后者则是高度为1024个像素



# 14 vue生命周期钩子
beforeCreate,created,beforeMount,mounted

beforeCreate 创建之前  没有数据没有方法

created 创建完毕  有数据有方法  但是没挂载  也就是没dom

beforeMount  挂载之前  没 dom  有数据

mounted  挂载后  有dom  有数据


# 15 vue2.0的响应式原理  （简练）主要通过发布订阅 Object.defineProperty  给getter 收集依赖  我们去获取的时候就会去通知他的依赖更新  后面就是异步更新


第一步  数据劫持  也就是数据拦截

我们通过 Object.defineProperty  来把他每一个属性转成 setter getter   这样就能触发setter   就知道什么属性被改了

第二 个是依赖收集  我们在渲染视图的时候 要将观察者(watcher)和具体的属性结合起来 通过发布订阅的方式  将数据的改变更加精准的去更新视图

收集依赖的过程 
 挂载前 对data对象进行遍历   进行对应的下载getter 在getter里面维护了一个 用来收集依赖的对象
 挂载的时候 template/el 把他转成这个 render函数   第二 创建了一个updateComponent的函数 调用 render函数 
 给每一个组件 或者vue实例 new一个watcher 这个watcher 创建updateComponent的函数
 挂载之后 我们的用户去更新数据的时候触发watcher   更新视图
 收集依赖过程放在 new watcher 的时候  在构造器的内部会调用一次 render函数 是由template/el编译过来的 在里面访问data属性  触发getter   在getter 内部 完成依赖收集


 重复收集 怎么办  调用 watcher的时候会加入唯一id  在添加依赖的时候会先判断 这个id有没有  就可以避免重复的收集
Object.defineProperty  有三个参数 

object	需定义或修改属性的对象
property	需定义或修改的属性
descriptor	属性的描述符，包含存取描述符和数据描述符，以对象方式传入。

let obj = {}
Object.defineProperty(obj, 'name', {
	value: 'alice'
});
obj.name ='tom'
console.log(obj.name)//alice


# 16 vue生命周期

beforeCreate,created,beforeMount,mounted,beforeUpdate,updated,beforeDestroy,destroyed

beforeCreate 创建之前  没有数据没有方法

created 创建完毕  有数据有方法  但是没挂载  也就是没dom

beforeMount  挂载之前  没 dom  有数据

mounted  挂载后  有dom  有数据  一般是在这里进行:开启定时器、发送网络请求、订阅消息、绑定自定义事件

this.$nextTick(()=>{
  //会在dom更新之后调用
})

beforeUpdate  在dom更新前调用  数据是新的  页面是旧的

updated  在dom更新后调用 数据是新的  页面也是新的


beforeDestroy 实例销毁前调用 这一步实例完全可用 方法 指令什么的都可用调用 一般在这里:关闭定时器、取消订阅消息、解绑自定义事件

destroyed 实例销毁后 方法 指令什么的都不能调用



# 17 vue组件间的参数传递  桥接文件


组件中 通信  如果是两个子组件之间的通信  就需要 创建桥接文件   用订阅发布功能

桥接文件 

import Vue from 'vue'
export default new Vue()


$emit('事件名',fn) ----触发自定义事件
<button @click="sendStudentName">把学生名给App</button>
sendStudentName() {
  // 触发Student组件实例身上的atguigu事件
  this.$emit("atguigu", this.name, 66, 58, 85);
  // this.$emit("demo");
  // this.$emit("click");
},

unbind() {
  this.$off("atguigu"); //一个事件解绑一个自定义事件
  // this.$off(["atguigu", "demo"]); //解绑多个自定义事件
  // this.$off(); //解绑所有自定义事件
},



$on('事件名',fn)-----添加并监听自定义事件  

<Student ref="student" @click.native="show"></Student>

getStudentName(name, ...params) {
  console.log("App收到了学生名", name, params);
  this.studentName = name;
},

mounted() {
  this.$refs.student.$on("atguigu", this.getStudentName);
},
 

# 18 less的用法

需要下载 less依赖  
<style lang="less" scoped>

</style>


如果直接往下写就相当于是后代
less写法
ul{
    width: 100%;
    li {
         width: 100%;
         text-align: center;
         height: 50px;
         line-height: 50px;
         border-bottom: 1px solid gray;
         border-right: 1px solid gray;
         a{
           color: black;
         }
    }
}
相当于
ul{
    width: 100%;
}
ul li{
        width: 100%;
        text-align: center;
        height: 50px;
        line-height: 50px;
        border-bottom: 1px solid gray;
        border-right: 1px solid gray;
}
ul li a {
    color: black;
}

从现有的样式中添加属性

less写法
.oneColor(){
  background-color: red;
  border:1px solid red;
  color: red;
}

#item {
    .oneColor;
    font-size: 20px;
}
#menu {
    .oneColor;
    line-height: 50px;
}


相当于

.oneColor(){
  background-color: red;
  border:1px solid red;
  color: red;
}
#item {
    background-color: red;
    border:1px solid red;
    color: red;
    font-size: 20px;
}
#menu {
    background-color: red;
    border:1px solid red;
    color: red;
    line-height: 50px;
}

带参数
less 写法

.oneColor(@color:#0094ff){
      background-color: @color;
      border:1px solid @color;
      color: @color;
}

/* 带参数的引用 */
#item {
  .oneColor(#fff);
}
/* 不带参数的引用（会采用默认值） */
#menu {
  .oneColor();
}

相当于

/* 由于带有参数，会修改内部的值，变为yellow */
#item {
      background-color: #ffffff;
      border:1px solid #ffffff;
      color: #ffffff;
}
#menu {
      background-color: #0094ff;
      border:1px solid #0094ff;
      color: #0094ff;
}

嵌套  

less写法

.main{
     width: 100%;
     height: 40px;
     /* 利用&符号来替代元素名 */
     &:hover{
         height: 90px;
     }
     &::before {
        content: '';
        position: absolute;
        width: 20px;
        height: 20px;
        background-color: red;
        border-radius: 10px;
    }
}


相当于

.main {
     width: 100%;
     height: 40px;
}
.main:hover {
    height: 90px;
}
.main::before {
    content: '';
    position: absolute;
    width: 20px;
    height: 20px;
    background-color: red;
    border-radius: 10px;
}


加减乘除

less写法
/* 定义一个变量 */
@width: 100px;
.plus {
    /* 运算时，可以不用加上单位，最终将得到宽度加10 */
    width: @width+10;
}

相当于

.plus {
    width: 110px;
}

定义变量的格式 

less写法

@jdRed:rgba(201,21,35,0.8);

/* 设置了body，h3，ul的背景颜色均为一样 */
body {
  background-color: @jdRed;
}
h3 {
  background-color: @jdRed;
}
ul {
  background-color: @jdRed;
}

# 19 scoped


在样式中使用  如果在单个组件中使用的化 别的组件就影响

vue组件中的style标签标有scoped属性时表明style里的css样式只适用于当前组件元素

不会去影响其他的组件

缺点

scoped CSS里每个样式的权重加重了，理论上我们可以修改某一个样式，但是却需要更高的权重去覆盖这个样式；
无论父组件样式的权重多大，也可能无法修改子组件的样式，除了子组件的根节点。
使用标签选择器时scoped会严重降低性能，而使用class或id则不会。



# 20 Promise用法
  
  promise.all  要等所有all()参数里的promise方法执行完成了 才会执行.then里面的方法  返回的是一个数组

  promise.race  有一个race的参数promise方法执行完成了 race方法就结束了  其他异步方法继续执行




# 21 then()用法
then()方法是异步执行。

意思是：就是当.then()前的方法执行完后再执行then()内部的程序，这样就避免了，数据没获取到等的问题。



# 22 promise 和 async、await的区别
async函数是一种兼顾了基于Promise的实现和「生成器」（也就是ES6的新特性Generator）的同步写法。


在 定义一个函数前面添加async这个函数的返回值 他会返回一个promise   请求前面调用 await 只会获取成功  如果不成功他下面的代码也不会执行   try catch 获取失败 catch 捕捉失败的状态

let fs = require('fs').promises  //让fs里方法都Promise化，不用自己转
//先看async 函数怎么写
async function read(){  
     let path = awit fs.readFile('a.txt', 'utf-8') 
     let result = awit fs.readFile(path, 'utf-8')  
     return result
}
let res = read() //res是一个Promise对象
res.then(data => {    //data就是成功后最后的结果 
     console.log(data)
})


# 23 js三大特性




# 23 keep-alive



# 24在发送请求拿数据的时候

一般会写在create构造函数里面 进入页面发送请求  也可以写在mouted


# 25v-for

v-for 中一般会写到key key的作用是为了更好的渲染 在数据变化后，虚拟dom会进行比对，没有key的话，就会重新全部选安然，有的话，就单独对那个操作，其他不用重新渲染，就是提高复用

1.vue中列表循环需加:key="唯一标识" 唯一标识尽量是item里面id等，因为vue组件高度复用增加Key可以标识组件的唯一性，为了更好地区别各个组件 key的作用主要是为了高效的更新虚拟DOM。

2.key主要用来做dom diff算法用的，diff算法是同级比较，比较当前标签上的key还有它当前的标签名，如果key和标签名都一样时只是做了一个移动的操作，不会重新创建元素和删除元素。

3.没有key的时候默认使用的是“就地复用”策略。如果数据项的顺序被改变，Vue不是移动Dom元素来匹配数据项的改变，而是简单复用原来位置的每个元素。如果删除第一个元素，在进行比较时发现标签一样值不一样时，就会复用之前的位置，将新值直接放到该位置，以此类推，最后多出一个就会把最后一个删除掉。

4.尽量不要使用索引值index作key值，一定要用唯一标识的值，如id等。因为若用数组索引index为key，当向数组中指定位置插入一个新元素后，因为这时候会重新更新index索引，对应着后面的虚拟DOM的key值全部更新了，这个时候还是会做不必要的更新，就像没有加key一样，因此index虽然能够解决key不冲突的问题，但是并不能解决复用的情况。如果是静态数据，用索引号index做key值是没有问题的。

5.标签名一样，key一样这时候就会就地复用，如果标签名不一样，key一样不会复用。



# 26 原型链

我们创建的每个函数都有一个==prototype==（原型）属性，prototype属性指向原型对象。通过该函数创建的实例对象会共享原型对象上的所有属性和方法。


用我们自己的话来说原型链 

如果有一个函数 他创建了一个实例化对象  这个对象就有他这个函数的prototype的所有属性和方法
const a = new Person();


 并且  a.__proto__ ===Person.prototype   所有对象都会有隐式原型(__proto__) 函数既有隐式原型 又有显式原型(prototype)   原型链也可以这么表示  
   a.__proto__ ===Person.prototype 且 
   a.__proto__.__proto__ ===Person.prototype.__proto__ ===Object.prototype

Object在往上就没有了 
 同时我们可以对原型指向进行改变 比如

  Person.prototype  = new Animal()   
  
  var p = new Person()
  
  p中就可以用到Animal中 的属性和方法
  

 在constructor中返回的结果是构造函数
 比如  a.__proto__.constructor 返回的就是 Person的构造函数

 



# 27列表过滤  filter indexOf(this.keyword) !== -1

  filPerons() {
                return this.persons.filter((p) => {
                    // 关键字不存在就过滤   输入keyword 判断 不等于-1 也就是 不存在这个关键字的字找出来进行过滤
                    return p.name.indexOf(this.keyword) !== -1

                })
            }

# 28升降序 sort


 //判断一下是否需要排序
  // 如果返回的是  正数就不变  负数 就是后面的放到前面去
  if (this.sortType) {
      arr.sort((p1, p2) => {
          return this.sortType === 1 ? p2.age - p1.age : p1.age - p2.age
      })
  }



# 29更新数组对象的内容   splice 三个参数
 
  // index:数组开始下标       

  // len: 替换/删除的长度       

  // item:替换的值，删除操作的话 item为空
  this.persons.splice(0, 1, {
      id: '001',
      name: '马老师',
      age: 50,
      sex: '男'
  })



  # 30 css 三大特性



  # 31 全局的过滤器 Vue.filter(name,callback)
   //全局的过滤器
    Vue.filter('mySlice', function (value) {
        return value.slice(0, 4)
    })             


 # 32   v-cloak指令(没有值) :
1.本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删掉v- cloak属性。
2.使用css配合v-cloak可以解决网速慢时页面展示出{kKxx}}的问题。


 # 33 v-html指令:
1.作用:向指定节点中渲染包含html结构的内容。
2.与插值语法的区别:
(1).v-html会替换掉节点中所有的内容，{{xx}}则不会。
(2).v- html可以识别htm1结构。
3.严重注意: v-html有安全性问题! ! ! !
(1).在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击。
(2).-定要在可信的内容上使用v-html,永不要用在用户提交的内容上!


# 34 v-once指令:
1.v-once所在节点在初次动态渲染后，就视为静态内容了。
2.以后数据的改变不会引起v- once所在结构的更新，可以用于优化性能


# 35 v-pre

v-pre指令是填充原始信息，在Vue中跳过了表达式的编译过程，显示原始数据。  也就是说

  <div id="root">
        <h2 v-pre>Vue其实很简单</h2>

        <h2 v-pre>当前n的值是：{{n}}</h2>
        <button v-pre @click="n++">点我n+1</button>
    </div>

    当前n的值是：{{n}}  中的 {{n}}  不会被编译 在页面还是显示的{{n}}

# 36
v-bind ; 单向绑定解析表达式，可简写为:XXX

v-model :双向数据绑定

V- for;遍历数组/对象/字符串

v-on:绑定事件监听，可简写为@

v-if:条件渲染(动态控制节点是否存存在)

v-else;条件渲染(动态控制节点是否存存在)

v-show : 条件渲染(动态控制节点是否展示)

v-text指令:

1.作用:向其所在的节点中渲染文本内容。
2.与插值语法的区别: v-text会 替换掉节点中的内容，{{xx}则不会。


# 36 自定义指令 

定义语法:
(1).局部指令:
new Vue({
  directives:{指令名，配置对象}
  )}
  或者
  new Vue({
  directives:{指令名，回调函数}
  )}
(2).全局指令:
Vue. directive(指令名，配置对象)或Vue .directive(指令名,回调函数)

二、配置对象中常用的3个回调:
(1).bind:指令与元素成功绑定时调用。
(2).inserted:指令所在元素被插入页面时调用。
(3).update:指令所在模板结构被重新解析时调用。
三、备注:
1.指令定义时不加V-，但使用时要加v-;
2.指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名。




//big函数何时会被调用?  1.指令与元素成功绑定时(一上来) 2.指令所在的模板被重新解析时

<h2>当前n的值是： <span v-text="n"> </span></h2>
<h2>放大十倍的n值是： <span v-big="n"> </span></h2>
<h2>放大十倍的n值是： <span v-big-number="n"> </span></h2>
<button @click="n++">点我n+1</button>
<br />
<input type="text" v-fbind:value="n" autofocus>



directives: {
    //element是当前的dom节点  binding是对象 这里绑定的是n
    'big-number'(element, binding) {
        // console.log('big')
        element.innerText = binding.value * 10
    },
    big(element, binding) {
        console.log('big', this); // 此处的this是window
        // console.log('big')
        element.innerText = binding.value * 10
    },
    fbind: {
        //指令与元素成功绑定时(一上来)
        bind(element, binding) {
            element.value = binding.value
        },
        //指令所在元素被插入时
        inserted(element, binding) {
            element.focus()
        },
        //指令所在的模板被重新解析时
        update(element, binding) {
            element.value = binding.value
            element.focus()
        }
    }

}



# 36 订阅和发布

import pubsub from "pubsub-js";  引入依赖

//订阅
 this.pubid = pubsub.subscribe("hello", (msgname, data) => {
      console.log(this);
      // console.log("有人发布了hello消息,hello消息的回调执行了", msgname, data);
    });



//发布
 sendStudentName() {
      pubsub.publish("hello", 666);
    },


# 37    @click.native 是子组件点击后触发  绑定的事件



# 38 vuex中mutations和actions的区别

action  提交异步操作  也可以用 来提交mutation    比如promise和async/await 等异步操作 

mutataion 提交同步操作   也可以异步但建议使用action 因为 vue官方插件devtools你用 了mutation异步的话你观察不到值的变化所以建议action写异步



# 怎么水平居中



# 项目  

项目使用vuex 来实现的左侧折叠

commit  同步操作 将数据提交给 mutation

axios 
1.是promise的api 避免回调地狱 

2.请求和响应前做一个拦截

3.支持防御XSRF的攻击


// map返回一个新数组 返回的是后面函数的值
            data: order.data.map((item) => item[key]),

//    splice数组删除
closeTag(state,val){
  const result = state.tabsList.findIndex(item=>item.name === val.name)

  state.tabsList.splice(result,1)
},

// mapMutations 和commit 都可以将mutation中的数据注入进组件
...mapMutations({
  close: "closeTag", //别名
}),


 delUser(row) {
      // 通知栏组件 $confirm
      this.$confirm("此操作将永久删除该组件，是否继续?", "提示", {
        confirmButtonText: "确认",
        cancelButtonText: "取消",
        type: "warning",
      }).then(() => {
        const id = row.id;
        this.$http
          .post("user/del", {
            params: { id },
          })
          .then(() => {
            this.$message({
              type: "success",
              message: "删除成功",
            });
            this.getList();
          });
      });
    },



    //路由的动态添加
            menuArray.forEach(item => {
                router.addRoute('Main',item)
            })