title: JS中特殊的对象arguments
date: 2016-01-24 16:58:11
tags: JavaScript
original: false
---
**arguments对象**
在函数代码中，才可使用特殊对象 arguments，arguments对象不能显式创建。函数的 arguments 对象并不是一个数组，但是访问单个参数的方式与访问数组元素的方式相同。访问函数中的第n个参数 则可以使用arguments[n-1] 
<!-- more -->
**通过索引访问函数参数**
一个简单的add函数中实践一下arguments对象(以下代码都是在chrome浏览器中*非严格模式*运行)
![](http://7xqeyw.com1.z0.glb.clouddn.com/argument1.png)
由上图可看出 在add函数中通过arguments访问参数的方式与访问数组元素的方式相同
 **arguments是函数中的一个对象**
对add函数进行简单修改如下图
![](http://7xqeyw.com1.z0.glb.clouddn.com/argument2.png)
由上图运行结果 则说明arguments的确是一个对象
**arguments对象的一些属性**
再次修改add函数 查看关于arguments对象的其他属性如下图
![](http://7xqeyw.com1.z0.glb.clouddn.com/argument3.png)
从运行结果中看到 arguments对象喊包含length 和 callee这样的属性
    
    arguments.length 是函数接受参数的个数（实参个数）
    arguments.callee 则代表当前正在执行的函数 它可以在匿名函数中通过callee递归调用自身
图中的蓝色框线中看到 callee还包含有其他属性 
输出arguments.callee.length长度是2 和 arguments.length 输出的3 不同

    arguments.length 是实参长度
    arguments.callee.length 是形参长度
arguments.callee还有一个属性caller 
    
    arguments.callee.caller 保存着调用当前函数的函数的引用
    由于当前函数是在全局作用域中运行 所以看到 caller是null
看下图 arguments.callee.caller则是调用当前函数的函数的引用
修改代码 如下
![](http://7xqeyw.com1.z0.glb.clouddn.com/argument4.png)
在outFun函数中调用add函数则arguments.callee.caller则指向了outFun函数
    
    关于caller  函数对象也存在一个caller属性 通函数名称调用 含义与 arguments.callee.caller 都是保存着调用当前函数的函数的引用
![](http://7xqeyw.com1.z0.glb.clouddn.com/argument5.png)
上图 funName函数中输出funName.caller  在outFun函数中调用funName函数  执行outFun函数 输出的是 图中红色框线部分输出的是outFun  即调用funName函数的函数引用 

**注意**
关于使用arguments通过索引获取函数参数 在非严格模式与严格模式下 稍有不同 
图中在**非严格模式**下更改 函数参数 x 的值时arguments[0]的值随其改变 同样更改arguments[0] 的值 x的值也随其改变
图中在**严格模式**下更改 函数参数 x 的值时arguments[0]的值并不会改变 同样更改arguments[0] 的值 x的值也不会随其改变
如图
![](http://7xqeyw.com1.z0.glb.clouddn.com/argument6.png)
