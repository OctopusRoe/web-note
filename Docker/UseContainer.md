## Use Container

---

### 运行容器

```sh
$ docker run imageID | imageName[:tag]
```

运行 `docker run` 命令时，如果本地已经拉取image，将直接运行image，如果没有拉取镜像，将去默认地址拉取image以上方式无法访问到启动的 Container内运行的程序的

```sh
$ docker run -d -p systemPort:ContainerPort --name ContainerName imageID | imageName[:tag]
```

1. `-d`: 在 Linux 系统后台运行
2. `-p systemPort:ContainerPort`: 映射 Linux的端口和容器端口，使我们能访问容器内的程序
3. `--name ContainerName`: 指定容器的名称

### 查看正在运行的容器

```sh
$ docker ps [-qa]
```

1. `-q`: 只能查看容器的标识
2. `-a`: 查看全部容器，包括没有运行的

### 查看容器日志

通过查看容器日志，以查看容器运行的情况

```sh
docker logs -f containerID
```

1. -f 可以滚动查看日志的最后几行

### 进入容器内部

docker就是一个虚拟的 system，可以直接进入内部进行操作或者设置

```sh
$ docker exec containerID command
```

使用以上命令时，只会执行一次command命令，然后从容器内部退出，推荐使用 `-it ... bash` 方式

```sh
$ docker exec -it containerID bash | [/bin/bash]
```

1. `-i`: 即使没有连接，也要保持STDIN打开
2. `-t`: 禁用pseudo-tty分配。默认情况下`docker-compose exec`分配一个终端

### 复制文件到容器

将宿主机的文件复制到容器的指定目录

```sh
$ docker cp fileName containerID:ContainerInnerPath
```

### 重启、启动、停止、删除Container

重启 Container

```sh
$ docker restart containerID
```

启动停止运行的 Container

```sh
$ docker start containerID
```

停止指定 Container

```sh
$ docker stop containerID
```

停止全部 Container

```sh
$ docker stop $(docker ps -qa)
```

删除指定 ContainerID

```sh
$ docker rm containerID
```

删除全部容器

```sh
$ docker rm $(docker ps -qa)
```

查看 container 信息

```sh
$ docker inspect containerID
```