## Nginx Config 配置

---

### 全局块

配置影响 `naginx` 全局的指令

一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等

```nginx
user nginx nginxgroup;
worker_processes 1;

error_log   /var/log/nginx/error.log warn;
pid         /var/run/nginx.pid;
```

1. user: 配置用户或者组，默认为 nobody nobody
2. worker_processes: 工作进程，允许生成的最大进程数，默认为1，在 `linux` 下通过 `ps aux | grep nginx` 可以查看到 `nginx` 的`master` 进程和 `worker processes` 进程
3. error_log: 指定日志存放路径，级别，这个设置可以放入全局块、http块、server块，级别依次为 `debug | info | notice | warn | error | crit | alert | emerg`
4. pid: `nginx` 运行的一个标识

### event块

配置影响 `nginx` 服务器或与用户的网络连接

有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同事接受多个网络连接，开启多个网络连接序列化等

```nginx
events {
    accept_mutex on;
    multi_accept on;
    use epoll;
    worker_connections 1024;
    
    # 以上为简单配置
    
    keepalive_timeout 60s;
    client_header_buffer_size 4k;
    open_file_cache max=2000 inactive=60s;
    open_file_cache_valid 60s;
    open_file_cache_min_uses 1;
}
```

1. accept_mutex: 设置网络连接序列化，防止惊群发生，默认为 on
2. multi_accept: 设置一个进程是否同事接受多个网络连接，默认为 off
3. use: 设置驱动模型，包含 `select | poll | kqueue | epoll | resig | /dev/poll | eventport`
4. worker_connections: `worker_processes` 的最大连接数量，理论上每台 `nginx` 服务器的最大连接数为 `worker_processes * worker_connections`
5. keepalive_timeout: 超时时间，此处指的是 http 层面的 `keep-alive` 不是 TCP的
6. client_header_buffer_size: 客户端请求头部的缓冲区大小，这个可以根据系统的分页大小来设置
7. open_file_cache: 为打开文件制定缓存，默认是没有启用的，`max` 指定缓存最大数，`inactive` 指经过多长时间文件没有被请求后删除缓存
8. open_file_cache_balid: 指多长时间检查一次缓存的有效信息，如果一个文件在 `inactive` 时间内一次也没被使用，它将被移除
9. open_file_cache_min_uses: `open_file_cache` 指令中的 `inactive` 参数时间内文件的最少使用次数，如果超过这个数，文件描述符一直在缓存中打开，如果一个文件在 `inactive` 时间内一次也没被使用，它将被移除

驱动模型

模型名称 | 描述 |
-|-|
select | 标准事件模型，如果当前系统不存在更好的方法，nginx将选择 selec 或 poll |
poll | 标准事件模型，如果当前系统不存在更好的方法，nginx将选择 selec 或 poll |
kqueue | 使用于FreeBSD 4.1+, OpenBSD 2.9+, NetBSD 2.0 和 MacOS X.使用双处理器的MacOS X系统使用kqueue可能会造成内核崩溃 |
epoll | 使用于Linux内核2.6版本及以后的系统 |
/dev/poll | 使用于Solaris 7 11/99+, HP/UX 11.22+ (eventport), IRIX 6.5.15+ 和 Tru64 UNIX 5.1A+ |
eventport | 使用于Solaris 10. 为了防止出现内核崩溃的问题， 有必要安装安全补丁 |

### http块

可以嵌套多个 `server块` 配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置

文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等

```nginx
http {
    include      mime.types;
    default_type application/octet-stream;

    log_format   myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for';

    access_log   log/access.log myFormat;
    sendfile     on;
    sendfile_max_chunk 100k;
    keepalive_timeout  60s;

    upstream mysvr {
        server 127.0.0.1;
        server 81.68.115.34 backup;
    }

    error_page 404 https://www.baidu.com;

    include /etc/nginx/conf.d/*.conf;
}
```
