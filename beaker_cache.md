#Caching

##About

Beaker 的缓存系统最初始基于perl的Cache::Cache模块。Beaker随后就是基于这个总体思想重新优化而来。

原始思想中的很多的概念依然存在于Beaker中。Beaker的cache和session利用了命名空间管理和容器的概念来处理数据的缓存。

后端利用定制化的对象来恰当的处理存储数据的方式，取决于后端的类型。

CacheManager用来获取合适的命名管理器，然后由他来存储数据值。每个单独的thing的命名空间应该被缓存，通常是否被缓存依赖于参数的变化。例如一个模板可能需要多份不同的依赖于用户是否登录的拷贝。每一个拷贝作为命名空间的键值并且存储在容器中。

有三种途径来使用Beaker的caching。第一种，也是最常用的方式是可编程的API。它提供了一个命名空间，并且检索出存储键值对的Cache对象，这些键值对是利用容器对象存放在命名空间中的。

越是简洁的系统，越是容易使用cache装饰器，这些装饰器同时支持Cache Regions.

Beaker 1.5中介绍的是一个非常灵活的cache_region() 装饰器。它在Beaker被配置之前利用Beaker的Cache Regions来渲染函数。这使得Beaker的region缓存装饰器可以很容易的被使用起来。

##创建缓存管理器

在使用Beaker缓存之前，需要先创建缓存管理器类的实例。所有这些假定在下面这个例子之前已经被创建好了。

创建缓存实例：

    from beaker.cache import CacheManager
    from beaker.util import parse_cache_config_options

    cache_opts = {
      'cache.type': 'file',
      'cache.data_dir': 'tmp/cache/data',
      'cache.lock_dir': 'tmp/cache/lock',
    }