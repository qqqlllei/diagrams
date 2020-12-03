### es api
       批量删除index:   curl -XDELETE http://10.33.80.32:32009/app-server-*
       查询index占用磁盘空间：curl -XGET http://10.33.80.30:32009/_cat/allocation?v

### filebeat filebeat.yml 配置
    processors:
    - drop_fields:
       fields: ["beat","log","source"] #过滤的属性
    filebeat.inputs:
    - type: log
      paths:
        - /var/log/containers/app*_server-*.log #匹配的log 文件
      fields:
        type: server
        nodeName: '${NODE_NAME}' #从filebeat 获取的node 信息，及当前服务的node节点
      symlinks: true
    output.elasticsearch:
      hosts: ['${ELASTICSEARCH_HOST:elasticsearch}:${ELASTICSEARCH_PORT:9200}']
      indices:
        - index: "app-front-%{+yyyy.MM.dd}"
          when.equals:
            fields.type: "front"
        - index: "app-server-%{+yyyy.MM.dd}"
          when.equals:
            fields.type: "server" # 根据自定义字段，创建不同的index
            
### filebeat 官方文档
    https://www.elastic.co/guide/en/beats/filebeat/6.8/index.html