---
title: Sequelize为什么需要使用Migrations
date: 2019-04-24 21:42:23
tags:
---

在项目中可以使用model在生成数据库表结构，也可以用migrations来生成表结构，
但当我习惯于使用migrations来生成表结构，了解migrations的好处之后不会再回过头来
使用model来直接生成表结构。migration可以实现操作数据库的所用功能，
下文中列出了使用migrations的几个好处。

###  Migrations相当于Git

在Sequelize文档中对于migrations的定义如下：
就像使用Git / SVN管理源代码中的更改一样，你可以使用迁移来跟踪对数据库的更改。
生成的每个migration都有他生成的时间，当你只用model来生成表结构的时候无法追踪
生成或者修改表结构的时间。使用migration可以方便的对表结构进行追踪记录和维护，
而且migration的记录对于其他开发人员了解表结构也有很大的帮助。相当于Git这一个
特点使使用migration的最重要原因。

### Migration效率更高

当你熟悉了migration的命令之后可以提高建立model和表结构的效率
```
sequelize model:create --name modelname --attributes  name:string,gender:enum
```
当你运行这样一句命令就可以快速的建立一个只有type类型的model，然后在生成的migration
加上约束，外健。。。等等就可以建立一个model
```
sequelize migration:create --name xxxx
```
当你运行这一命令可以单独生成一个migration文件，可以进行加一个column，添加一个约束，
改变column的type等操作
```
sequelize db:migrate   //运行migration，建立表结构
sequelize db:migrate:undo //撤销上一个migation ，也可以指定回退到哪一步
```

当你只需要掌握这几个简单的命令，那么可以快速的进行model的建立，model的修改等等操作，
并且随时可以进行撤销。

### 代码更安全

当你不使用migration来建立表结构的时候可能在代码上会遗留一些如
<strong>db.sequelize.sync({force:true})</strong>这样的代码来进行
表结构的建立。当我部署到环境的时候会确认好几遍来确认这段代码已经被注释掉。
当在代码结构中保留这些drop的语句是很危险也很可怕的一件事情。

