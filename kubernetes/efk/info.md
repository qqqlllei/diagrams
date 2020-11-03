### es 批量删除index
       curl -XDELETE http://10.33.80.32:32009/filebeat-6.8.13-*

### filebeat filebeat.yml 配置
        filebeat.inputs:
        - type: log
          paths:
            - /var/log/containers/app*_server-*.log #匹配 log 文件
          fields:
            type: server #自定义属性
          symlinks: true
        output.elasticsearch:
          hosts: ['${ELASTICSEARCH_HOST:elasticsearch}:${ELASTICSEARCH_PORT:9200}']
          indices:
            - index: "app-front-%{+yyyy.MM.dd}"
              when.equals:
                fields.type: "front"
            - index: "app-server-%{+yyyy.MM.dd}"
              when.equals:
                fields.type: "server" #根据自定义属性创建index