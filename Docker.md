# 					Docker学习资料







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

