## 使用 Dockerfile 创建自定义镜像

---

使用 dockerfile 创建 Docker 镜像，dockerfile 是一个文本文档，其中包含了用户创建 image 时所调用的一条条指令，每一条指令构建一层，每一层描述该层应当如何构建

### Dockerfile 指令

#### FROM

指定基础 image

```dockerfile
FROM centos:6
```

#### MAINTAINER

指明 image 作者及其联系方式

```dockerfile
MAINTAINER OctopusRoe <zp393939@outlook.com>
```

更推荐使用 LABEL 来指定 image 作者

```dockerfile
LABEL maintainer="zp393939@outlook.com"
```

#### RUN

构建 image 时运行的 Shell 命令

```dockerfile
RUN yum install nginx
RUN ["yum", "install", "nginx"]
```

两种方式都可以

#### CMD

启动 container 时执行的 Shell 命令

```dockerfile
CMD nginx -d
CMD ["nginx", "-d"]
```

两种方式都可以

#### EXPOSE

指定 container 运行时对外暴露的端口

```dockerfile
EXPOSE 80 443
```

#### ENV

构建 image 时设置环境变量，后续的 RUN 可以使用它所创建的环境变量，创建基于该 image 的 container 时，会自动拥有设置的环境变量

```dockerfile
ENV dirName testNginx
RUN mkdir $dirName
```

通过 ENV 定义的环境变量无法被 CMD 指令使用，也不能被 docker run 的命令参数引用

但是在 docker run 命令中通过 -e 标记来传递环境变量，这样 container 运行时就可以使用该变量

```sh
docker run -it  -e "myName=OctopusRoe" imageID bash
```

#### ADD

拷贝文件或目录到 image 中，如果文件或目录是URL或tar，将会自动下载或解压

```dockerfile
ADD /home/nginx/data/dist /usr/share/nginx/html

ADD xxx.tar.gz /usr/share/nginx/html

ADD https://xxx.com/xxx.tar.gz /usr/share/nginx/html
```

#### COPY

拷贝文件或目录到镜像中，用法和 ADD 指令一样，只是不支持自动下载和解压，一般常用，能提高构建 image 速度

```dockerfile
COPY /home/nginx/data/dist /usr/share/nginx/html
```

#### ENTRYPOINT

启动 container 时执行 Shell 命令，同 CMD 类似，指定多个 ENTRYPOINT 时，只有最后一个会生效，而且如果指定了 ENTRYPOINT 后，CMD 的含义就发生变化，不在是直接运行命令，而是将 CMD 中的内容作为参数传递给 ENTRYPOINT 指令

这样做的好处是，在启动 container 时可以传递参数，而参数会拼接到 CMD 命令后

而且 ENTRYPOINT 内是可以放入脚本的，而这个脚本会将接到的参数(CMD内容) 作为命令，在脚本的最后执行

```dockerfile
ENTRYPOINT nginx -d
ENTRYPOINT ["nginx", "-d"]
```

#### VOLUME

将 container 指定的目录挂载到宿主机或其他 container 的指定目录，如果宿主机h或其他 container 没有这个目录，将自动创建

```dockerfile
VOLUME /html /home/nginx/data/dist
VOLUME ["/html", "/home/nginx/data/dist"]
```

一般很少在 Dockerfile 中使用，常见于在 `docker run` 的时候使用 `-v` 数据卷

#### USER

用于指定 container 执行 `RUN` `CMD` `ENTRYPOINT` Shell 命令时用户身份，默认是 root 用户

```dockerfile
USER OctopusRoe
USER <user>[:<usergroup>]
USER <UID>[:<UID>]
```

在执行 `docker run` 时可以通过 `-u` 选项来覆盖 `USER` 指令的设置

#### WORKDIR

用于指定 container 内的一个目录，container 启动时 `RUN` `CMD` `ENTRYPOINT` `COPY` `ADD` 执行的命令会在指定的目录下执行(相当于给 container 指定了一个工作目录)

```dockerfile
WORKDIR /data
```

#### HEALTHCHECK

告诉 Docker 如何测试 container 以检测 container 是否仍在工作

```dockerfile
HEALTHCHECK --interval=5m --timeout=3s --retries=3 --start-period=30m \
    CMD curl -f http://localhost/ || exit 1
```

1. `--interval`: 每隔多长时间探测一次，默认为30秒
2. `timeout`: 服务响应超时时长，默认30秒
3. `--start-period`: 服务器启动后多久开始探测，默认是0秒
4. `--retries`: 认为检测失败几次为宕机，默认为3次

命令的退出状态指示 container 的状况:

1. `0`: container 可以正常使用
2. `1`: container 无法正常使用
3. `2`: 保留，不要使用此退出码

#### ARG

构建参数和 `ENV` 的效果一样，都是设置环境变量，所不同的是，`ARG` 所设置是构建环境的环境变量，在 container 运行时时不会存在使用 `ARG` 构建的变量的，不要使用 `ARG` 来保存密码之类的敏感信息，因为 `docker history` 还是可以看到所有的值

使用 `ARG` 构建的环境参数，可以在构建命令 `docker build` 中使用 `--build-arg <参数名>=<值>` 来覆盖

```dockerfile
ARG <参数名>[=<默认值>]
```