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



# vue生命周期钩子



# vue2.0的响应式原理



