### 构建自定义镜像

    `docker build -t qqlei/skywalking-agent:8.1.0 . 
    
    
### 以sidecar形式对业务服务监控
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: $DEPLOYMENT_NAME
      name: $DEPLOYMENT_NAME
      namespace: hmall
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: hui-mall-gateway-docker
      template:
        metadata:
          labels:
            app: hui-mall-gateway-docker
        spec:
          initContainers:
          - name: init-agent
            image: qqlei/skywalking-agent:8.1.0
            command:
            - 'sh'
            - '-c'
            - 'set -ex;mkdir -p /skywalking/agent;cp -r /opt/skywalking/agent/* /skywalking/agent;'
            volumeMounts:
            - name: agent
              mountPath: /skywalking/agent
          containers:
          - env:
            - name: SKYWALKING_ADDR
              value: $SKYWALKING_ADDR
                   ***部分省略***
            volumeMounts:
            - name: agent
              mountPath: /opt/skywalking/agent 
          volumes:
          - name: agent
            emptyDir: {}
            
            
### server 参数配置
    SW_CORE_ROLE: 
    
### client 参数配置
    SW_AGENT_SAMPLE： 每 3秒采集的样本跟踪比例，如果是负数则表示 100%采集
    SW_LOGGING_LEVEL： 日志级别