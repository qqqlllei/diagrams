## service
### headless
    apiVersion: v1
    kind: Service
    metadata:
      #在内部网络可以通过 该名称访问service,跨namespace的访问时，如业务上的服务访问elk中的es 则 elasticsearch-data-service.elk
      name: nacos-headless
      labels:
        app: nacos
      annotations:
        service.alpha.kubernetes.io/tolerate-unready-endpoints: "true"
    spec:
      ports:
        - port: 8848  #service 的端口
          name: server
          targetPort: 8848 #pod的端口
        - port: 7848
          name: rpc
          targetPort: 7848
      clusterIP: None  #clusterIp 为None，headless 一般配合statefulset使用
      selector:
        app: nacos #代理spec.template.metadata.labels app: nacos的pod
        
## StatefulSet
### pod 亲和性
          # 当前创建的pod 必须要与拥有标签app=zk的pod 不在同一个node上，如果无法满足，则pod一直处于pending状态，一般是用来构建高可用集群，如zk，redis等
          affinity: # 亲和性设置
            podAntiAffinity: # 设置pod的反亲和性
              requiredDuringSchedulingIgnoredDuringExecution: # 必须要满足的条件
                - labelSelector: # 标签选择与那个pod 有反亲和性
                    matchExpressions:
                      - key: "app" # 标签key
                        operator: In
                        values:
                          - zk  #标签的值，即拥有 label app=nginx的pod
                  topologyKey: "kubernetes.io/hostname" #节点所属拓扑域
                  
    