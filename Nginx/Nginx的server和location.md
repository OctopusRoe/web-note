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
2. 