## Pull Image

---

### Docker的中央仓库

1. Docker官方的中央仓库：镜像最全，但是下载速度慢

    [https://hub.docker.com/](https://hub.docker.com/)

2. 国内的镜像网站：网易蜂巢，daoCloud等，下载速度快，但是镜像相对不全

    [网易蜂巢: https://c.163yun.com/hub#/home](https://c.163yun.com/hub#/home)

    [daoCloud: http://hub.daocloud.io/(推荐)](http://hub.daocloud.io/)

3. 公司内部会采用私服的方式拉取镜像(需要添加配置)

    ```json
    # 需要创建 /etc/docker/daemon.json，并添加如下内容
    {
        "registry-mirrors":["https://registry.docker-cn.com"],
        "insecure-registries":["ip:port"]
    }
    ```
    
    重启两个服务

    ```sh
    systemctl daemon-reload
    systemctl restart docker
    ```

### pull images

从中央仓库拉取镜像到本地

```sh

docker pull 镜像名称[:tag]
```

### 查看本地全部镜像

查看本地已经安装过的镜像信息，包含标识，名称，版本，更新时间，大小

```sh
docker images
```

### 删除本地镜像

通过查看本地镜像获取标识后，直接手动删除镜像，减少占用磁盘空间

```sh
docker rmi imageID
```

### 镜像的导入与导出

也可以通过硬盘、U盘的方式传输镜像，但是这种方式导出的镜像名称和版本都是null，需要手动修改

#### 导出本地镜像

```sh
docker save -o 导出路径 imageID
```

#### 加载本地的镜像

```sh
docker load -i 镜像文件
```

#### 修改镜像文件

```sh
docker tag imageID NewImageName:Version
```