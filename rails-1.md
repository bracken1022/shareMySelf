

#rails first record

##从这里开始

从这里你可以学到：
* 可以安装rails，创建rails的应用，并且和数据库关联起来
* rails的大体上的页面布局
* 基本的MVC的概念和原理，了解RESTful设计
* 如何快速的建立rials应用程序

###假设你已经拥有的配置

* 这个教程是针对rials的初学者，并且假定你已经安装了ruby 1.8.7版本或者更新的版本。

>建议升级到ruby 1.9.2版本以上，ruby 1.8.7有使得rails 3.0崩溃的bug。ruby 1.9.1也不能用，导致rails 3.0段错误。

* 需要安装rubyGems包管理系统
* 需要SQLite3 数据库管理系统

rails是一个基于ruby的web应用程序框架。如果你对ruby语言不熟悉，那么学习rails会有比较大的压力。

##什么rails？

>本节所讲的是rails的背景，当然，你可以跳过，后续有了基本的概念之后再来了解一下。（我们跳过)

###MVC 架构

rials的核心就是Model，View，Controller架构，通常缩写为MVC。MVC架构的益处有：

* 将逻辑业务从用户接口中独立出来
* 使得代码符合DRY(Dont Repeate Yourself)

####模型Models

模型代表了应用程序所依赖的数据信息和对数据信息的操作。在rails中models是第一个处理和数据库相关的接口。大多数情况下，每个数据库中的表都对应到一个模型中。

####视图Views

视图模块定义了应用程序的用户接口部分。rails中视图模块通常情况下是嵌入了ruby代码的html文件。视图完成了向发起请求的浏览器传送数据的工作。

####控制器

控制器起了视图和模型之间的连接作用。控制器负责处理浏览器发起的请求，取得数据并传送给视图。

###rails的组成

rails由以下几个部分组成，这些组成部分的细节可以暂时不用考虑，后面会进行详细解释。

* Action Pack
  **Action Contoller
  **Action Dispatch
  **Action View

* Action Mailer
* Active Model
* Active Record
* Active Resource
* Active Support
* Railties

####Action包

#####Action 控制器

Action 控制器在rails中起控制作用的组件。它处理请求提取请求中的参数并且将它们分发到相应的控制器组件。Action控制器提供的服务还包括会话管理，模板渲染和重定向功能。

#####Action 视图

视图组件处理rails的视图部分。默认创建为html或者xml，并且处理模板的渲染和内置的AJAX。

#####Action 分发

Action分发处理web他们到请求的路由和分发。体积到了rack（？）。

#####Action Mailer

这是一个建立e-mail服务的组件，可以用它来接收和发送邮件。

#####Action 模型

提供一个定义好的Action Pack的gem和ORM的gems的接口，以便rails可以利用其他的ORM模型。

#####Active Record

它是rails中模型的基础。提供了通过CRUD方式对数据库的处理，进一步提供了查询和其他关系模型的服务。

#####Active Resource

#####Acvite Support

#####Railties

###REST

REST构架就不在这里详述了。

