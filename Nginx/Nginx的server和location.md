## Nginx的server和location

---

Nginx 在决定请求由哪个 server 块执行的时候，主要是通过 server 块中的 `listen` && `server_name` 两个字段来识别的

### listen 字段

listen 字段定义 `server` 响应的ip和端口

如果没有显式的定义 `listen` 字段，在 root 用户权限下，nginx 将默认监听 `0.0.0.0:80` 端口，非 root 用户权限下，将默认监听 `0.0.0.0:8080` 端口

#### listen 配置规则

1. 一个ip和端口的组合 `listen 192.168.1.50:5000`
2. 一个ip `listen 192.168.1.50`，默认监听 `80` 端口
3. 一个端口 `listen 5000`，默认监听所有的ip
4. 一个 Unix socket 路径 (用于在不同 server 之间传递请求)

#### listen 配置运行原理

1. 如果缺少 listen 字段，或者 listen 字段的值只有ip或者只有端口，Nginx 首先将把 listen 字段缺少的属性填充默认值，如没有 listen 字段时，填充 `listen 0.0.0.0:80`,缺少端口时，在ip后填充默认端口
2. Nginx根据请求的ip和端口来选择最匹配的 server块，优先匹配指定了特定ip的 server块，其次才会选择 `listen 0.0.0.0:80` 这种 server块，但是端口是必须完全匹配的
3. 如果只有一个最佳匹配，那么将使用匹配的 server块 响应请求，否则开始评估每一个 server块 的 `server_name` 字段

### server_name 字段

当 Nginx 无法通过 listen 字段匹配到最佳 server块时，Nginx 就会检查请求头，通过 `Host` 头中的值，来匹配 server块中的 `server_name` 字段

**注意:** 浏览器默认会自动从Url中解析host

#### server_name 匹配规则

1. Nginx 会尝试寻找一个 `server_name` 和 `Host` 完全匹配的 server块，如果找到多个精确匹配，则会使用第一个匹配的 server块

```nginx
server {
    listen  80;
    server_name www.baidu.com;
}
```

2. 如果没有找到完全匹配的 server块，Nginx 则会尝试寻找 server块中，`server_name` 的值带有 `*` 号开头的，如果找到多个，则选择最长匹配的 server块

```nginx
server {
    listen  80;
    server_name *.baidu.com;
}
```

3. 如果没有找到使用 `*` 号开头的 server块，Nginx 则会寻找以 `*` 号结尾的 server块，如果找到多个，则选择最长匹配的 server块

```nginx
server {
    listen  80;
    server_name www.*;
}
```

4. 如果没有找到使用 `*` 号开头或结尾的 server块，Nginx 则会寻找使用正则表达式(以 `~` 号开头)命名的 server块，如果找到多个，则会使用第一个匹配的

```nginx
server {
    listen  80;
    server_name ~^(?.+)\.baidu\.com
}
```

5. 可以在 server块中的 listen 字段的值后添加 `default_server` 参数，在 `server_name` 没有匹配到任何一个 server块时，返回一个默认的 server块，这样可以返回错误到客户端

```nginx
server {
    listen 80 default_server;
    server_name _;  return 444;
}
```

通过返回 Nginx 的非标准错误码 444，来断开与客户端的连接

### location 块

对请求的 URL 中的 `path` 进行匹配，判断返回哪一个 location块

#### 修饰符

修饰符 | 说明 |
-|-|
~* | 使用正则表达式进行匹配，不区分大小写 |
~ | 使用正则表达式进行匹配，区分大小写 |
^~ | 根据前缀匹配，如果匹配到，则终止后续匹配 |
= | 如果请求的 `path` 与定义的 值完全一样，则使用该 location块
无 | 无修饰符，根据前缀匹配 |

#### 匹配规则

1. Nginx 先检查无修饰符的 location
2. 如果有 `=` 修饰符，则检查 location块是否与请求的 `path` 完全相等，如果相等，则使用该 location块
3. 如果没有找到带有=修饰符的location块匹配,则会继续计算非精确前缀,根据给定的URI找到最长匹配前缀,然后进行如下处理:
    
    > 1. 如果最长的匹配 locationk块 带有 `^~` 修饰符，Nginx 立刻使用该 location块 响应请求
    > 2. 如果最长的匹配 location块 不带有 `^~` 修饰符，Nginx 会将该匹配暂时存起来,然后继续后续匹配

4. 在确定并储存最长匹配的前缀 location块 后，Nginx 继续检查正则表达式匹配 locationK块，如果存在正则表达式满足要求的匹配,则会选择与请求的 URI 匹配的第一个正则表达式的 location块 来相应请求
5. 如果没有找到与请求的 URI 匹配的正则表达式 location块，则使用之前存储的最长前缀 location块 响应请求
