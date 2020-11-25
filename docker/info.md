### docker 安装指定版本
    yum install -y docker-ce-18.09.6 docker-ce-cli-18.09.6 containerd.io

### docker 删除所有容器
    docker rm `docker ps -a -q`
    
### 构建镜像
    docker build -t name:version .

### 查看日志
    docker logs -f -t --tail 1000 datacenter
    
    