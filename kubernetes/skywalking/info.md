### �����Զ��徵��

    `docker build -t qqlei/skywalking-agent:8.1.0 . 
    
    
### ��sidecar��ʽ��ҵ�������
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
                   ***����ʡ��***
            volumeMounts:
            - name: agent
              mountPath: /opt/skywalking/agent 
          volumes:
          - name: agent
            emptyDir: {}
            
            
### server ��������
    SW_CORE_ROLE: 
    
### client ��������
    SW_AGENT_SAMPLE�� ÿ 3��ɼ����������ٱ���������Ǹ������ʾ 100%�ɼ�
    SW_LOGGING_LEVEL�� ��־����