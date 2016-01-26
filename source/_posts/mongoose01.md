title: mongoose简单总结
date: 2015-11-16 10:25:40
tags: mongoose
original: false
---

工作一年多了,一直再用node.js和PHP写后端. node.js写后端 数据库用mongodb ,第三方数据库操作模块就用的mongoose,在此总结一下mongoose的基本使用

#### Mongoose是什么
Mongoose是MongoDB的一个对象模型工具，是基于node-mongodb-native开发的MongoDB nodejs驱动，可以在异步的环境下执行。同时它也是针对MongoDB操作的一个对象模型库，封装了MongoDB对文档的的一些增删改查等常用方法，让NodeJS操作Mongodb数据库变得更加灵活简单。
<!-- more -->

#### Mongoose能做什么
Mongoose，因为封装了对MongoDB对文档操作的常用处理方法，让NodeJS操作Mongodb数据库变得easy、easy、So easy!

#### Mongoose使用
##### 安装mongoose
    npm install mongoose
##### 引用mongoose
    var mongoose = require("mongoose");
##### 使用"mongoose"连接数据库
    var db = mongoose.connect("mongodb://user:pass@localhost:port/database"); 
执行下面代码检查默认数据库test，是否可以正常连接成功

    var mongoose = require("mongoose");
    var db = mongoose.connect("mongodb://127.0.0.1:27017/test");
    db.connection.on("error", function (error) {
    console.log("数据库连接失败：" + error);
    });
    db.connection.on("open", function () {
    console.log("------数据库连接成功！------");
    }); 

#### 名词解释 Schema,Model,Entity

**Schema** —— 一种以文件形式存储的数据库模型骨架，无法直接通往数据库端，也就是说它不具备对数据库的操作能力，仅仅只是数据库模型在程序片段中的一种表现，可以说是数据属性模型(传统意义的表结构)，又或着是“集合”的模型骨架。
那如何去定义一个Schema，请看示例：

    var mongoose = require("mongoose");
    var TestSchema = new mongoose.Schema({
              name : { type:String },//属性name,类型为String
              age  : { type:Number, default:0 },//属性age,类型为Number,默认为0
              time : { type:Date, default:Date.now },
              email: { type:String,default:''}
        });

**Model** —— 由Schema构造生成的模型，除了Schema定义的数据库骨架以外，还具有数据库操作的行为，类似于管理数据库属性、行为的类。
通过Schema来创建Model，如下示例：

    var db = mongoose.connect("mongodb://127.0.0.1:27017/test");
        //……
       // 创建Model
    var TestModel = db.model("test1", TestSchema);
拥有了Model，在后面就可以使用Model来进行增删改查的具体操作。
    
    var TestSchema = new mongoose.Schema({
           name : { type:String },//属性name,类型为String
           age  : { type:Number, default:0 },//属性age,类型为Number,默认为0
           time : { type:Date, default:Date.now },
            email: { type:String,default:''}
      });
    var TestModel = db.model("test1", TestSchema);

**Entity** —— 由Model创建的实体，使用save方法保存数据，Model和  Entity都有能影响数据库的操作，但Model比Entity更具操作性。
使用Model创建Entity，如下示例：
    
    var TestEntity = new TestModel({
           name : "jaak",
           age  : 25,
           email: "jaak@qq.com"
    });
    console.log(TestEntity.name); // jaak
    console.log(TestEntity.age); // 25

#### Schema Model Entity 完整示例

    var mongoose = require("mongoose");
    var db = mongoose.connect("mongodb://127.0.0.1:27017/test");
    var TestSchema = new mongoose.Schema({
        name : { type:String },
        age  : { type:Number, default:0 },
        email: { type:String },
        time : { type:Date, default:Date.now }
    });
    var TestModel = db.model("test1", TestSchema );
    var TestEntity = new TestModel({
        name : "helloworld",
        age  : 28,
        email: "helloworld@qq.com"
    });
    TestEntity.save(function(error,doc){
      if(error){
         console.log("error :" + error);
      }else{
         console.log(doc);
      }
    });
  - Schema：数据库集合的模型骨架，或者是数据属性模型传统意义的表结构。
  - Model ：通过Schema构造而成，除了具有Schema定义的数据库骨架以外，还可以具体的操作数据库。
  - Entity：通过Model创建的实体，它也可以操作数据库。
  Schema、Model、Entity的关系请牢记，Schema生成Model，Model创造Entity，Model和Entity都可对数据库操作造成影响，但Model比Entity更具操作性

#### Model 基本的find查询
使用Model进行基本的find查询： Model.find(查询条件,callback);
    
    Model.find({},function(error,docs){
        //若没有向find传递参数，默认的是显示所有文档
     });
    Model.find({ "age": 28 }, function (error, docs) {
       if(error){
         console.log("error :" + error);
       }else{
         console.log(docs); //docs: age为28的所有文档
       }
     }); 

#### Model 保存方法
 语法：Model.create(文档数据, callback))
 示例 :
 
    Model.create({ name:"model_create", age:26}, function(error,doc){
           if(error) {
               console.log(error);
           } else {
               console.log(doc);
           }
       });

#### Entity 保存方法
语法 : Entity.save(文档数据, callback))
示例:
    
    var Entity = new Model({name:"entity_save",age: 27});  
    Entity.save(function(error,doc) {
        if(error) {
            console.log(error);
        } else {
            console.log(doc);
        }
    });

#### Model 数据更新  
  语法 : Model.update(查询条件,更新对象,callback);
  示例:

    var conditions = {name : 'test_update'};
    var update = {$set : { age : 16 }};
    Model.update(conditions, update, function(error){
        if(error) {
            console.log(error);
        } else {
            console.log('Update success!');
        }
    });

#### Model 删除数据
语法 : Model.remove(查询条件,callback);
示例:
    
    var conditions = { name: 'tom' };
    Model.remove(conditions, function(error){
        if(error) {
            console.log(error);
        } else {
            console.log('Delete success!');
        }
    });

#### Model find过滤查询
语法 :  属性过滤 Model.find(Conditions,*field*,callback);
*field* 说明：我们只需要把显示的属性设置为大于零的数就可以，当然1是最好理解的_id是默认返回 如果不要显示加上("_id":0)
但是 对其他不需要显示的属性且不是_id 如果设置为0的话将会抛异常或查询无果。
示例：
//返回只包含一个键值name、age的所有记录
    
    Model.find({},{name:1, age:1, _id:0}，function(err,docs){
       //docs 查询结果集
    })

#### Model findOne的基本用法
与find相同，但只返回单个文档，也就说当查询到即一个符合条件的数据时，将停止继续查询，并返回查询结果。
单条数据查询语法: Model.findOne(Conditions,callback);

    Model.findOne({ age: 27}, function (err, doc){
        // 查询符合age等于27的第一条数据
        // doc是查询结果
     });
findOne方法，只返回第一个符合条件的文档数据。

#### Model findById的基本用法
与findOne相同，但它只接收文档的_id作为参数，返回单个文档。
单条数据查询语法: Model.findById(_id, callback);
    
    TestModel.findById('_id', function (err, doc){
     //doc 查询结果文档
    });

#### Model 条件查询

    "$lt"(小于)，"$lte"(小于等于),"$gt"(大于)，"$gte"(大于等于)，"$ne"(不等于)，"$in"(可单值和多个值的匹配)，"$or"(查询多个键值的任意给定值)，"$exists"(表示是否存在的意思)"$all"

**使用$gt(>)、$lt(<)、$lte(<=)、$gte(>=)操作符进行排除性的查询，如下示例**

    Model.find({"age":{"$gt":18}},function(error,docs){
       //查询所有nage大于18的数据
    });
    Model.find({"age":{"$lt":60}},function(error,docs){
       //查询所有nage小于60的数据
    });
    Model.find({"age":{"$gt":18,"$lt":60}},function(error,docs){
       //查询所有nage大于18小于60的数据
    });

**$ne(!=)操作符的含义相当于不等于、不包含，查询时我们可通过它进行条件判定，具体使用方法如下**
      
    Model.find({ age:{ $ne:24}},function(error,docs){
        //查询age不等于24的所有数据
    });
    Model.find({name:{$ne:"tom"},age:{$gte:18}},function(error,docs){
      //查询name不等于tom、age>=18的所有数据
    });

**和$ne操作符相反，$in相当于包含、等于，查询时查找包含于指定字段条件的数据。具体使用方法如下**
    
    Model.find({ age:{ $in: 20}},function(error,docs){
       //查询age等于20的所有数据
    }); 
    Model.find({ age:{$in:[20,30]}},function(error,docs){
      //可以把多个值组织成一个数组
    }); 

**$or操作符，可以查询多个键值的任意给定值，只要满足其中一个就可返回，用于存在多个条件判定的情况下使用，如下示例**

    Model.find({"$or":[{"name":"yaya"},{"age":28}]},function(error,docs){
      //查询name为yaya或age为28的全部文档
    });

**$exists操作符，可用于判断某些关键字段是否存在来进行条件查询。如下示例**
    
    Model.find({name: {$exists: true}},function(error,docs){
      //查询所有存在name属性的文档
    });
    Model.find({telephone: {$exists: false}},function(error,docs){
      //查询所有不存在telephone属性的文档
    });

**limit的基本用法**
限制返回数量语法：Model.find(Conditions,fields,**options**,callback);
示例：

    Model.find({},null,**{limit:20}**,function(err,docs){
        console.log(docs);
    });

**sort的基本用法**
对结果排序语法：Model.find(Conditions,fields,options,callback);
示例：

    Model.find({},null,{sort:{age:-1}},function(err,docs){
      //查询所有数据，并按照age降序顺序返回数据docs
    });
sort函数可以将查询结果数据进行排序操作，该函数的参数是一个或多个键/值对，
键代表要排序的键名，值代表排序的方向，1是升序，-1是降序

**skip的基本用法**
跳过数量：find(Conditions,fields,**options**,callback);
    
    Model.find({},null,**{skip:4}**,function(err,docs){
        console.log(docs);
    });
如果查询结果数量中少于4个的话，则不会返回任何结果。

**skip limit sort 联合查询 示例**
   
    Model.find({},null,{skip:0,limit:2,sort:{age:1}},function(err,docs){
     // ...
    });

#### 链式查询
这种方式相对直接查询，分的比较明细，如果不带 callback，则返回 query，
query 表示没有执行的预编译查询语句，该 query 对象执行的方法都将返回自己，
只有在执行 exec 方法时才执行查询，而且必须有回调。
    
    var query = Model
    .find({ age: 24})
    .limit(10)
    .skip(5);
    query.exec(function(err,docs){
      if(err){
         //...
        }else{ 
         console.log(docs);
        }
    });















