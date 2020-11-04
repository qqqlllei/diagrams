### 容器OOM文件导出
#### server.yaml
    image: web/demo:release-2-9-1
    imagePullPolicy: Always
    name: demo
    ports:
    - name: http
      containerPort: 9090
    livenessProbe: #存活探针
      httpGet:
        port: http # 对应ports 的name
        path: /actuator/health
      initialDelaySeconds: 150 # 第一次执行延迟150s
      periodSeconds: 5 # 5s一次存活探测
      timeoutSeconds: 30 
    lifecycle:
      preStop:
        exec:
          command: ["sh","/opt/dump/alioss.sh"]
      
    
    项目添加参数 -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/opt/dump/dumpfile
    
#### 脚本 alioss.sh
    
    #!/bin/bash
    
    oss_util_path_url=/opt/dump/ossutil64
    dump_file_name=/opt/dump/dumpfile
    ## 钉钉报警
    dingtalktoken=https://oapi.dingtalk.com/robot/send?access_token=你的token
    
    echo 'ALIYUN_LOGTAIL_USER_DEFINED_ID' $ALIYUN_LOGTAIL_USER_DEFINED_ID
    
    if [ ! -f "$dump_file_name" ];then
      echo "file not exist"
      exit 8
    else
      echo "file exist"
    fi
    
    if [ ! -f "$oss_util_path_url" ];then
      wget http://gosspublic.alicdn.com/ossutil/1.6.16/ossutil64
      chmod 755 ossutil64
      ./ossutil64  config -e oss-cn-hangzhou-internal.aliyuncs.com -i [你的accessKeyID] -k [你的accessKeySecret] -c myossconfig
    else
      echo ""
    fi
    
    if [ ${#ALIYUN_LOGTAIL_USER_DEFINED_ID} -lt 1 ];then
      ALIYUN_LOGTAIL_USER_DEFINED_ID=default
      echo "change name"
    fi
    
    ls_date=`date +%Y.%m.%d.%H.%M.%S.%3N`
    
    echo $ALIYUN_LOGTAIL_USER_DEFINED_ID
    
    ./ossutil64 --config-file=myossconfig  cp $dump_file_name  oss://resource-fat/ops/javadump/$ALIYUN_LOGTAIL_USER_DEFINED_ID/$ls_date/
    
    apk add curl
    
    curl $dingtalktoken \
       -H 'Content-Type: application/json' \
       -d "{\"msgtype\": \"text\",\"text\": {\"content\": \"alarm:  "${ALIYUN_LOGTAIL_USER_DEFINED_ID}"  test\"}}"