### docker 学习

#### 1.docker nginx

```
docker search nginx； #查询对应的docker镜像
docker pull docker.io/nginx； #拉取镜像
mkdir -p /opt/seal/nginx/{conf.d,conf,log,html}; #创建镜像挂载本地文件
  
docker run --name nginx -d -p 80:80 -v /opt/seal/nginx/conf.d:/etc/nginx/conf.d  -v /opt/seal/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /opt/seal/nginx/log:/var/log/nginx -v /opt/seal/nginx/html:/usr/share/nginx/html docker.io/nginx; #运行镜像

vim /opt/seal/nginx/conf/nginx.conf or conf.d；#修改对应得本地配置文件
docker restart 'CONTAINER ID'; 重新启动镜像生效
```

#### 2. docker 部署rocketMQ

##### 单机版本

1. wget http://mirrors.tuna.tsinghua.edu.cn/apache/rocketmq/4.2.0/rocketmq-all-4.2.0-bin-release.zip  #下载bin文件

2. 创建namesrv的Dockerfile

   ```
   FROM java:8 #基于什么基础镜像
   ARG version
   # Rocketmq version
   ENV ROCKETMQ_VERSION 4.2.0
   # Rocketmq home
   ENV ROCKETMQ_HOME  /opt/seal/rocketmq/namesrv-${ROCKETMQ_VERSION}
   WORKDIR  ${ROCKETMQ_HOME}
   RUN mkdir -p \
           /opt/logs \
           /opt/store \
           /opt/conf
   #这行代码进行了修改，基于本地的rocketmq编译包生成镜像，而不是通过网上下载。
   ADD rocketmq-all-4.2.0-bin-release.zip rocketmq.zip
   RUN       unzip rocketmq.zip \
             && rm rocketmq.zip
   RUN chmod +x bin/mqnamesrv
   CMD cd ${ROCKETMQ_HOME}/bin && export JAVA_OPT=" -Duser.home=/opt" && sh mqnamesrv
   EXPOSE 9876
   VOLUME /opt/logs \
           /opt/store \
           /opt/conf
   ```

3.  构建镜像

   ```
   docker build -t  rocketmq-namesrv:4.2.0 .     # . 表示当前目录
   ```

4. 创建broker的Dockerfile

   ```
   FROM java:8
   ARG version
   # Rocketmq version
   ENV ROCKETMQ_VERSION 4.2.0
   # Rocketmq home
   ENV ROCKETMQ_HOME  /opt/seal/rocketmq/broker-${ROCKETMQ_VERSION}
   WORKDIR  ${ROCKETMQ_HOME}
   RUN mkdir -p \
           /opt/logs \
           /opt/store
   #这行代码进行了修改，基于本地的rocketmq编译包生成镜像，而不是通过网上下载。
   ADD rocketmq-all-4.2.0-bin-release.zip rocketmq.zip
   RUN       unzip rocketmq.zip \
             && rm rocketmq.zip
   RUN chmod +x bin/mqbroker
   CMD cd ${ROCKETMQ_HOME}/bin && export JAVA_OPT=" -Duser.home=/opt" && sh mqbroker -n namesrv:9876
   EXPOSE 10909 10911
   VOLUME /opt/logs \
           /opt/store
   ```

5. 构建镜像

   ```
   docker build -t  rocketmq-broker:4.2.0 .     # . 表示当前目录
   ```

6. 编写docker-compose.yml文件

   ```
   version: '2'
   services:
     namesrv:
       image: namesrv:4.2.0
       ports:
         - 9876:9876
       volumes:
         - "/opt/seal/rocketmq/namesrv-4.2.0/logs:/opt/logs"
         - "/opt/seal/rocketmq/namesrv-4.2.0/store:/opt/store"
     broker:
       image: rocketmq-broker:4.2.0
       ports:
         - 10909:10909
         - 10911:10911
       volumes:
         - "/opt/seal/rocketmq/broker-4.2.0/logs:/opt/logs"
         - "/opt/seal/rocketmq/broker-4.2.0/store:/opt/store"
       environment:
           JAVA_OPT_EXT: "-server -Xms128m -Xmx258m -Xmn128m"
       links:
         - namesrv:namesrv
   ```

7. 运行：

   ```
   docker-compose up -d # -d 后台启动
   ```

8. 大功告成。

   ![1562225194277](https://github.com/braveblack/brave-js/blob/master/1562225194277.png)



