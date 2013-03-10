#Configuration配置

Beaker可以有多种不同的配置方法，这取决于它的用法。最值得推荐的用法就是：通过字典将值传递给SessionMiddleware或者CacheManager.

Beaker的session和caching利用同一个后台存储系统，有一些选项可以对他们进行配置。

大多数的选项可以通过字符串(预先在INI文件中建立好的)来设置，并且可以被强制转换成正确的值。只有日期和timetelta不能被强制类型转换。

用到beaker的框架通常允许session和caching两个都可以在同一个位置来进行配置。beaker假定这个条件可以得到满足，并且有选项来进行配置。

例如，在Pylons的INI文件中配置beaker session的cookie_expires选项可能如下所示：

    beaker.session.cookie_expires = true

又或者利用独立的SessionMiddleware中间件：

    from beaker.middleware import SessionMiddleware
    session_opts = {
        'session.cookie_expires': True
    }

    app = SomeWSGIAPP()
    app = SessionMiddleware(app, session_opts)

利用CacheManager的例子如下：

    cache_opts = {
        'cache.type': 'file',
        'cache.data_dir': '/tmp/cache/data',
        'cache.lock_dir': '/tmp/cache/lock'
    }

    cache = CacheManager(**parse_cache_config_options(cache_opts))

##Options For Sessions and Caching

data_dir(optional, string)

将数据存储到物理文件中，dbm或者基于文件的后台系统。这个路径应该为绝对路径。

lock_dir(required, string)

为了配合locking,此lock文件用来确保不会有多个进程在同一个时间产生同一个值。

memcache_module(optional, string)

memcache, cmemcache, pylibmc, auto四者之一，默认为auto。此选项指定用ext:memcached后台的时候，哪种客户端缓存库将被加载。

type(required, string)

用来存储session或者cache对象的后台的名称。beaker所允许的类型有：file, dbm, memory, ext:memcached, ext:database, ext:google。

对于session来说，cookie可以保存所有的session信息。但是cookie有大小限制。

url(optional,string)

URL在ext:memcached 或者ext:database情况下使用。当使用ext:memcached时：
    session_opts = {
        'session.type': 'ext:memcached',
        'session.url': '127.0.0.1:11211',
    }

当使用ext:database时，url的值必须为合法的sqlachemy字符串。

##Session Options

依据如何在cookie中存储session id和利用何种可选加密算法，session有多种可选项。

auto(option,bool)

设置为true时，session将会在任意时刻保存自身。默认为false。

cookie_expires( optional, bool, datetime, timedelta, int)

设置客户端cookie超时时间，设置为bool值时，将会在浏览器退出时超时，或者永远不会超时。
设置日期时间格式，强制session结束时间为某个具体日期。
设置一个整数，将会在此整数秒之后超时。

默认不会过期。

cook_domain(option,string)

key(required, string)

cookie的key所对应的名字。

secret(required, string)

利用HMAC加密算法来保证session的完整性。此值应该为一个随机数。在集群系统中，这个值应该在所有的系统上都一样。

secure(optional, bool)

timeout(optional, integer)

