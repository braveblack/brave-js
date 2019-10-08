# 				docker命令

​	

```
docker ps : 查看正在运行的容器
docker run -d -P 
	-d:让容器在后台运行。
	-P:将容器内部使用的网络端口映射到我们使用的主机上。
docker images : 列出所有的docker镜像	
```



docker安装redis：

```
docker search redis: 查找redis镜像
docker pull redis:3.2  拉取镜像
docker images redis: 查找redis镜像
docker run -p 6379:6379 -v $PWD/data:/data  -d redis:3.2 redis-server --appendonly yes:运行redis镜像
docker exec -it 43f7a65ec7f8 redis-cli: 使用redis镜像执行redis-cli命令连接到刚启动的容器
```

