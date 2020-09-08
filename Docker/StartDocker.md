## Docker 运用

---

### install nginx image

可以直接拉取 nginx image 后安装

```sh
# 使用 Daocloud 镜像源拉取 nginx 1.18 镜像
$ docker pull daocloud.io/library/nginx:1.18

# 启动容器
$ docker run -d -p 80:80 --name nginx imageID
```

也可以直接启动 nginx，当本地没有镜像的时候，docker会自动去镜像源拉取镜像

```sh
$ docker run -d -p 80:80 --name nginx daocloud.io/library/nginx:1.18
```

### install mysql image

同样也可以通过 `pull` 拉取镜像后安装，这里直接演示 `run` 方法

```sh
$ docker run -d -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=yourpassword daocloud.io/library/mysql:8.0.20
```

* `-e MYSQL_ROOT_PASSWORD=` 用于指定 mysql 的 root 用户密码

### copy the file to inside of the container

```sh
$ docker cp fileName containerID:containerInnerPath
```

注意：但是不推荐使用此方法部署服务