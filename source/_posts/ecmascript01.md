title: JavaScript类型转换
date: 2015-12-19 20:59:54
tags: JavaScript
original: false
---
#### 一、类型介绍
 JavaScript中，共有6种类型，其中5种是基本类型，1种复杂类型。
   基本类型：number、boolean、string、null、undefined
   复杂类型：Object
   5种基本类型中，处理null、undefined外都有相应的包装类。
   除了5种基本类型之外，所有的类型都是Object类型的。
   typeof操作符是JavaScript中用以判断类型的操作符，返回6种全小写的字符串，分别是'number'、'boolean'、'string' 、'null'、'undefined'。
   其次 还有数组Array类型 实质上也是Object类型<!-- more -->
   其中null类型和Object类型的都返回'object'，function也是Object类型的，但是由于其在JavaScript中的特殊性，所以有必要单独区分！
   typeof操作的执行结果：
 {% asset_img typeof.png This is an example image %}
#### 二、其他类型转boolean
 **Boolean() 引起的转型**
   规则是：
 - 非0数字（含Infinity） --> true
 - Object对象            --> true
 - 非空字符串            --> true
 - 0/NaN                --> false
 - null                 --> false
 - undefined            --> false
 - ''                   --> false

   
 {% asset_img boolean.png This is an example image %}

**!! 引起的转型**

与使用Boolean()一致
 {% asset_img !!.png This is an example image %}

**! 引起的转型**

 {% asset_img !.png This is an example image %}
**if()引起的转型**

 {% asset_img if.png This is an example image %}

**&& 和 || 引起的转型**
包含两种情况：
 a、两边确实是boolean，经过逻辑运算，返回相应的值。
         
    true && false --> false，
    true || false --> true
 b、两边不是boolean
 下列写法，大家肯定会常常见到。
 
    var t = a && b ； //相当于 var t = a ? b : a;
    return a && b； //相当于 return a ? b : a;
    a && test()； //相当于 a ? test() : null;
    var t = a || b ;  //相当于 var t = a ? a : b;
#### 三、其它类型转number
转number共有3个函数：Number()、parseInt()、parseFloat()

    1、Number()引起的转型 
         规则是：
               true   --> 1
               false  --> 0

              如果字符串中只包含数字  如'123' --> 123
              如果字符串中包含有效的浮点数字  如'123.99' -->123.99
              如果字符串是有效的十六进制数字  如'0xFF'  --> 255
              如果字符串是空串 '' --> 0
              如果字符串包含上述格式之外字符  如'123a'  --> NaN
              如果是对象 则调用对象的valueOf方法，然后按照上述规则进行转换，
                 如果没有valueOf方法，则调用对象的toString方法，然后按照上述规则进行转换。
                 如果valueOf和toString都存在，则调用valueOf方法！！！

            注意：Number会忽略前导0

    2、parseInt/parseFloat引起的转型
        规则是：
             null,  undefined,  '', true, false  --> NaN
             如果字符串中只包含数字  如 '123' --> 123
             如果字符串中包含有效的浮点数字  如'123.99'  parseInt --> 123, parseFloat --> 123.99
             如果字符串是有效的十六进制数字  如
                        parseInt    '0xFF'  --> 255
                        parseFloat  '0xFF' --> 0
             解析字符串，直到某个字符不是数字为止 
                        parseInt    '123a' --> 123 
                        parseFloat   '123.3a' --> '123.3' 

            如果参数是Object类型，会调用toString()方法。注意：并不会调用valueOf方法！！！

    3、+, - ,*, /,%, ++, -- 都会引起类型转换，转换规则同1。
#### 四、其它类型转string
 1、String()引起的转型

       规则是：

           如果值有toString()方法，则调用该方法(无参数)，并返回相应的结果
           如果值是null，则返回'null'
           如果值是undefined， 返回'undefined'

     2、+ '' 引起的转型，规则同1。如
                    var str = null + ''     //  'null'
                    var str = undefined + ''    // 'undefined' 
                    var str = {toString:function(){return 'i am a object'}} + '' // 'i am a object'
#### 五、包装类型
    var num = new Number(123)
    转型规则同Number()
    var bool = new Boolean(true)
    转型规则同Boolean()
    var str   = new String('123a')
    转型规则同String()

   **包装类型转换大招：**
   
     var val = Object(arg)
     如果arg是Object类型，原样返回
     如果arg是number，boolean，string，则返回相应的包装实例
     如果arg是null，undefined，则返回 {}
     注意：var val = new Object(arg) 跟不带new的结果一致！！！
#### 六、toString() 和 valueOf() 调用问题

     +,-,*,/,%,++,--,Number,isNaN,isFinite
        会导致这两个方法被调用，valueOf存在就调用valueOf，不存在就调用toString，两者都存在，调用valueOf
        
    parseInt/parseFloat/String()
        会导致toString方法被调用。。并不会调用valueOf！！！
#### 七、>、>=、<、<=引起的转换
规则是：

    如果两个操作数都是数值，则执行数值比较

    如果两个操作数都是字符串，则比较两个字符串对应的字符编码值

    如果一个操作数是数值，则将另外一个操作数转换成一个数值，然后执行比较 如 2 > '1' ，会把'1' 转成 1，然后和2比较

    如果一个操作数是对象，则调用这个对象的valueOf方法，用得到的结果，按照前3条的规则转换之后执行比较，如果对象没有valueOf方法，则调用toString方法，用得到的结果，按照前3条的规则转换之后执行 比较。
    如果一个操作数是boolean值，则先将七转换为数值，然后执行比较。true --> 1、false --> 0。
#### 八、== 引起的转型

   规则是：

    如果有一个操作数是NaN，马上返回false。

    如果有一个操作数是boolean值，则在比较相等性之前，先转成数值，false转0，true转1。

    如果一个操作数是字符串，另一个操作数是数值，在比较相等性之前先将字符串转为数值。

       如果一个操作数是对象，另一个操作数不是，则调用对象的valueOf方法，用得到的基本类型值按照前面的规则进行比较。
    null == undefined --> true

    如果两个操作数是对象，则比较它们是不是同一个对象，如果两个操作数都指向同一个对象，则相等操作符返回true，否则，返回false

   参考原文：http://www.imooc.com/article/2382

    