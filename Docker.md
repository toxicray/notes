# 					Docker学习资料

## 前置条件

在当前的linux上安装docker

裸机先安装包管理工具即可

yum redhat或者centos的条件下 /或者apt ubnatu



以centos为例

1. yum update   更新到最新的版本'

2. yum install docker 安装docker的

3. 设置镜像加速, 到阿里云的平台里面找到自己的加速地址

4. 不同的系统版本对应的docker可能也会有一些区别,建议注意一下服务器的系统版本

   尽可能走原有的已有的路

5. 



docker run --name postgresdb -p 5432:5432 -e POSTGRES_PASSWORD=root -d postgres



wiki的服务id   B4QN-YAW5-JMSG-Y42W

激活码   

```
AAABLQ0ODAoPeJxtkFFrwjAQx9/zKQJ7jphWEYTAYhuGW5O6VSc+xnpugZhK0pb57RetvozBveTuf
z9+l6ctHLBqekxnseaUztMUZ9UaJ+OEohxC7c25NY1jWeOOtgNXA1LdaQ++PG4C+MAIRZkHfQ3lu
gV23SQ01gzFnVbXrdInYO3FxwSqI2YUe6aPLd/BIyOkNpYZ15tg9haeQw0ORs4i0Wvb3ejsqG2Ag
VCYOA+wvpzhRs9KKcVHtuQFiiDXgtPRVPycjb8MVmk6IzQhyXQAPG7IbBda8Ko5QGBjVAnFduUGS
/4msBSY44rneMVVzkeo9F/amTDIGPVpKrMoBF4LLlEFvge/zNli8q7Ijm+n5FVWL2Q3Sbbobhunx
TJ/vP6XW3W+/tYB/nzlLxGiiVMwLAIUGxvTl3zE6F7l53QjJZKx8VBb+5gCFHwdy2C5v6+026TFY
9a6d/YzS8dIX02f3
```





## wiki的安装地址参照

https://www.cnblogs.com/rslai/p/8845777.html



##    						阿里云的容器镜像加速



```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://b7lzsg97.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```



## Docker的软件市场

```
docker hub
```

下载之后的镜像都在本地

docker pull nginx  默认拉取的是最新的镜像



## 端口映射

启动容器

```
docker run --help

docker run [options] image [command] [arg...]
           设置项      镜像     命令

docker run --name=mynginx -d --restart=always -p 88:80  nginx

docker rm    删除   
docker rm -f 强制删除
docker rmi   删除镜像
docker ps  运行中的
docker ps -a  所有的
docker start 容器id/容器名称
reboot 重启
systemctl enable ...
sudo  管理员权限
docker run --help | grep restart
--restart=always 开机自启动,设置项
docker update 容器id/容器名称 --restart=always  开机自启动

```

