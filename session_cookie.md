
#Session & cookie 的区别

##session 

session 被用于表示一个持续的链接状态，在网站的访问中一般指客户端浏览器的进程从开启到结束的过程。表示一个访问过程。

##cookie

session 的常见形式就是会话cookie，即未设置过期时间的cookie，这个cookie的默认生命周期为浏览器的会话期间，只要浏览器关闭，cookie就消失了。实现机制就是当用户发起一个请求的时候，服务器会检查该请求中是否包含sessionid，如果未包含，则创建一个cookie返回给浏览器(只放入内存，并不存在硬盘中)。并将其以Hash Table的形式写到服务器的内存里面。当已经包含sessionid时，服务器端会查找到与该sessionid相匹配的信息。如果存在，则直接使用该sessionid，如果不存在重新生成新的session。

##session存在的时效

浏览器端session当浏览器被关闭时，session也随之消失。服务器端失效时间一般是人为设置，一般设置为当会话处于非活动时间达20-30分钟时清除该session。

##多个进程共享一个session

目前很多浏览器支持多进程共享session，即通过多个标签或页面打开多个进程访问一个网站共享一个session cookie。

##重新认识cookie

cookie是一小段文本信息，伴随着用户请求和页面在Web服务器和浏览器之间传递。用户每次访问站点时，Web应用程序都可以读取cookie包含的信息。

这里所说的cookie指的是持久cookie。持久cookie指的是存放于硬盘上的cookie信息。持久cookie一般会保存用户的用户ID，该信息在用户注册或第一次登录的时候由服务器生成包含域名及相关信息的cookie发送并存放到客户端的硬盘文件上，并设置cookie的过期时间，以便于实现用户的自动登录和网站内容自定义。

