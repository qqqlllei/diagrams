### pv 持久化存储卷
    apiVersion: v1
    kind: PersistentVolume
    metadata:  # PV建立不要加名称空间，因为PV属于集群级别的
      name: nfs-pv001  # PV名称
      labels: # 这些labels可以不定义
        name: nfs-pv001
        storetype: nfs
    spec:  # 这里的spec和volumes里面的一样
      storageClassName: normal
      accessModes:  # 设置访问模型 不同类型的存储卷类型不同
        - ReadWriteMany #多路读写，卷能被集群多个节点挂载并读写
        - ReadWriteOnce #单路读写，卷只能被单一集群节点挂载读写
        - ReadOnlyMany #多路只读，卷能被多个集群节点挂载且只能读
      capacity: # 设置存储空间大小
        storage: 500Mi
      #  回收策略
      #  Retain 当删除与之绑定的PVC时候，这个PV被标记为released（PVC与PV解绑但还没有执行回收策略）且之前的数据依然保存在该PV上，但是该PV不可用，需要手动来处理这些数据并删除该PV。
      #  Delete 当删除与之绑定的PVC时候
      persistentVolumeReclaimPolicy: Retain 
      nfs: # 存储卷类型 NFS 、CephFS 、Glusterfs 、local
        path: /work/volumes/v1
        server: stroagesrv01.contoso.com
        
        
### pvc 持久化存储声明
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: nfs-pvc001
      namespace: default
      labels: # 这些labels可以不定义
        name: nfs-pvc001
        storetype: nfs
        capacity: 500Mi
    spec:
      storageClassName: normal # 一致才能匹配到pv
      accessModes:  # PVC也需要定义访问模式，不过它的模式一定是和现有PV相同或者是它的子集，否则匹配不到PV
      - ReadWriteMany
      resources: # 定义资源要求PV满足这个PVC的要求才会被匹配到
        requests:
          storage: 500Mi  # 定义要求有多大空间
          
### pod 对pvc的使用
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: tomcat-deploy
    spec:
      replicas: 1
      selector:
        matchLabels:
          appname: myapp
      template:
        metadata:
          name: myapp
          labels:
            appname: myapp
        spec:
          containers:
          - name: myapp
            image: tomcat:8.5.38-jre8
            ports:
            - name: http
              containerPort: 8080
              protocol: TCP
            volumeMounts:
              - name: tomcatedata # 挂载的名称
                mountPath : "/data"
          volumes:
            - name: tomcatedata # 挂在的名称
              persistentVolumeClaim: 
                claimName: nfs-pvc001 # 对应pvc的名称
                
                
### storageClass 动态供给
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: managed-nfs-storage
    provisioner: fuseim.pri/ifs
    parameters:
      archiveOnDelete: "false"
    
### Provisioner 自动配置程序 自动创建pv
    kind: Deployment
    apiVersion: apps/v1
    metadata:
      name: nfs-client-provisioner
    spec:
      replicas: 1
      strategy:
        type: Recreate
      selector:
        matchLabels:
          app: nfs-client-provisioner
      template:
        metadata:
          labels:
            app: nfs-client-provisioner
        spec:
          serviceAccount: nfs-client-provisioner
          containers:
            - name: nfs-client-provisioner
              image: quay.io/external_storage/nfs-client-provisioner:latest
              volumeMounts:
                - name: nfs-client-root
                  mountPath: /persistentvolumes
              env:
                - name: PROVISIONER_NAME
                  value: fuseim.pri/ifs
                - name: NFS_SERVER
                  value: 10.33.80.30
                - name: NFS_PATH
                  value: /home/nfs
          volumes:
            - name: nfs-client-root
              nfs:
                server: 10.33.80.30
                path: /home/nfs

### 通过pvc 使用StorageClass
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: test-pvc
      annotations: # 标识使用哪个StorageClass
        volume.beta.kubernetes.io/storage-class: "course-nfs-storage"
    spec:
      accessModes:
      - ReadWriteMany
      resources:
        requests:
          storage: 1Mi
### 通过volumeClaimTemplates 直接使用StorageClass
      volumeClaimTemplates:
        - metadata:
            name: zookeeper-data-volume
            annotations:
              volume.beta.kubernetes.io/storage-class: "managed-nfs-storage"
          spec:
            accessModes: [ "ReadWriteOnce" ]
            resources:
              requests:
                storage: 1Gi
                
                
### 本地持久化存储（Local Persistent Volume）
##### 把数据存储在POD运行的宿主机上，需求的原因是有时候你的应用对磁盘IO有很高的要求，网络存储性能肯定不如本地的高，尤其是本地使用了SSD这种磁盘

    # 定义pv
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: example-pv
    spec:
      capacity:
        storage: 5Gi
      volumeMode: Filesystem
      accessModes:
      - ReadWriteOnce
      persistentVolumeReclaimPolicy: Delete
      storageClassName: local-storage
      local: # local类型
        path: /data/vol1  # 节点上的具体路径
      nodeAffinity: # 这里就设置了节点亲和
        required:
          nodeSelectorTerms:
          - matchExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values:
              - node01 # 这里我们使用node01节点，该节点有/data/vol1路径
    
    # 定义存储类
    kind: StorageClass
    apiVersion: storage.k8s.io/v1
    metadata:
      name: local-storage
    # provisioner 字段定义为no-provisioner，这是因为 Local Persistent Volume 目前尚不支持Dynamic Provisioning动态生成PV，所以我们需要提前手动创建PV。
    provisioner: kubernetes.io/no-provisioner
    #延迟绑定，延迟绑定的好处是，POD的调度要参考卷的分布，调度POD的时候看看它要求的LPV在哪里，然后就调度到该节点，然后进行PVC的绑定，最后在挂载到POD中
    volumeBindingMode: WaitForFirstConsumer 
    
    # 定义pvc
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: local-claim
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 5Gi
      storageClassName: local-storage
    
    #定义pod
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: tomcat-deploy
    spec:
      replicas: 1
      selector:
        matchLabels:
          appname: myapp
      template:
        metadata:
          name: myapp
          labels:
            appname: myapp
        spec:
          containers:
          - name: myapp
            image: tomcat:8.5.38-jre8
            ports:
            - name: http
              containerPort: 8080
              protocol: TCP
            volumeMounts:
              - name: tomcatedata
                mountPath : "/data"
          volumes:
            - name: tomcatedata
              persistentVolumeClaim:
                claimName: local-claim