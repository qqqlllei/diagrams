### pv �־û��洢��
    apiVersion: v1
    kind: PersistentVolume
    metadata:  # PV������Ҫ�����ƿռ䣬��ΪPV���ڼ�Ⱥ�����
      name: nfs-pv001  # PV����
      labels: # ��Щlabels���Բ�����
        name: nfs-pv001
        storetype: nfs
    spec:  # �����spec��volumes�����һ��
      storageClassName: normal
      accessModes:  # ���÷���ģ�� ��ͬ���͵Ĵ洢�����Ͳ�ͬ
        - ReadWriteMany #��·��д�����ܱ���Ⱥ����ڵ���ز���д
        - ReadWriteOnce #��·��д����ֻ�ܱ���һ��Ⱥ�ڵ���ض�д
        - ReadOnlyMany #��·ֻ�������ܱ������Ⱥ�ڵ������ֻ�ܶ�
      capacity: # ���ô洢�ռ��С
        storage: 500Mi
      #  ���ղ���
      #  Retain ��ɾ����֮�󶨵�PVCʱ�����PV�����Ϊreleased��PVC��PV��󵫻�û��ִ�л��ղ��ԣ���֮ǰ��������Ȼ�����ڸ�PV�ϣ����Ǹ�PV�����ã���Ҫ�ֶ���������Щ���ݲ�ɾ����PV��
      #  Delete ��ɾ����֮�󶨵�PVCʱ��
      persistentVolumeReclaimPolicy: Retain 
      nfs: # �洢������ NFS ��CephFS ��Glusterfs ��local
        path: /work/volumes/v1
        server: stroagesrv01.contoso.com
        
        
### pvc �־û��洢����
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: nfs-pvc001
      namespace: default
      labels: # ��Щlabels���Բ�����
        name: nfs-pvc001
        storetype: nfs
        capacity: 500Mi
    spec:
      storageClassName: normal # һ�²���ƥ�䵽pv
      accessModes:  # PVCҲ��Ҫ�������ģʽ����������ģʽһ���Ǻ�����PV��ͬ�����������Ӽ�������ƥ�䲻��PV
      - ReadWriteMany
      resources: # ������ԴҪ��PV�������PVC��Ҫ��Żᱻƥ�䵽
        requests:
          storage: 500Mi  # ����Ҫ���ж��ռ�
          
### pod ��pvc��ʹ��
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
              - name: tomcatedata # ���ص�����
                mountPath : "/data"
          volumes:
            - name: tomcatedata # ���ڵ�����
              persistentVolumeClaim: 
                claimName: nfs-pvc001 # ��Ӧpvc������
                
                
### storageClass ��̬����
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: managed-nfs-storage
    provisioner: fuseim.pri/ifs
    parameters:
      archiveOnDelete: "false"
    
### Provisioner �Զ����ó��� �Զ�����pv
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

### ͨ��pvc ʹ��StorageClass
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: test-pvc
      annotations: # ��ʶʹ���ĸ�StorageClass
        volume.beta.kubernetes.io/storage-class: "course-nfs-storage"
    spec:
      accessModes:
      - ReadWriteMany
      resources:
        requests:
          storage: 1Mi
### ͨ��volumeClaimTemplates ֱ��ʹ��StorageClass
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
                
                
### ���س־û��洢��Local Persistent Volume��
##### �����ݴ洢��POD���е��������ϣ������ԭ������ʱ�����Ӧ�öԴ���IO�кܸߵ�Ҫ������洢���ܿ϶����籾�صĸߣ������Ǳ���ʹ����SSD���ִ���

    # ����pv
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
      local: # local����
        path: /data/vol1  # �ڵ��ϵľ���·��
      nodeAffinity: # ����������˽ڵ��׺�
        required:
          nodeSelectorTerms:
          - matchExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values:
              - node01 # ��������ʹ��node01�ڵ㣬�ýڵ���/data/vol1·��
    
    # ����洢��
    kind: StorageClass
    apiVersion: storage.k8s.io/v1
    metadata:
      name: local-storage
    # provisioner �ֶζ���Ϊno-provisioner��������Ϊ Local Persistent Volume Ŀǰ�в�֧��Dynamic Provisioning��̬����PV������������Ҫ��ǰ�ֶ�����PV��
    provisioner: kubernetes.io/no-provisioner
    #�ӳٰ󶨣��ӳٰ󶨵ĺô��ǣ�POD�ĵ���Ҫ�ο���ķֲ�������POD��ʱ�򿴿���Ҫ���LPV�����Ȼ��͵��ȵ��ýڵ㣬Ȼ�����PVC�İ󶨣�����ڹ��ص�POD��
    volumeBindingMode: WaitForFirstConsumer 
    
    # ����pvc
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
    
    #����pod
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