
user  root;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {

        upstream lms.inner {
            #ip_hash;
            server 10.33.80.113:9000;
            server 10.33.80.120:9000;
        }

        upstream basic.tests2 {
            #ip_hash;
            server 10.33.80.113:8012;
            server 10.33.80.129:8012;
        }

        upstream basic.tests4 {
            #ip_hash;
            server 10.33.80.108:8012;
            server 10.33.80.129:8012;
         }

        upstream userapi.test2 {
            #ip_hash;
            #server 10.33.80.108:8012;
            #server 10.33.80.129:8012;
            # server 10.33.80.129:9085; 
            server 10.33.80.113:9085;
         }




    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    server
    {
        listen 80;
        server_name userapi.test2.zhongjiaxin.com;
        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            #set $app_name lms;
            proxy_pass http://userapi.test2;
        }
    }




    server
    {
        listen 80;
        server_name test2.lms.inner.zhongjiaxin.com ;
        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            set $app_name lms;
            proxy_pass http://lms.inner;
        }
#       location ~ ^(/jieqingHFH/|/billsItem/lmsToHuofh) {
#                  proxy_set_header Host $host;
#                  proxy_set_header X-Real-IP $remote_addr;
#                  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#                  set $app_name lms;
#                  proxy_pass http://lms.inner2;
#              }
     }


    server
    {
        listen 80;
        server_name basic.tests2.zhongjiaxin.com;
        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            set $app_name lms;
            proxy_pass http://basic.tests2;
        }
    }

    server
    {
        listen 80;
        server_name basic.tests4.zhongjiaxin.com;
        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            set $app_name lms;
            proxy_pass http://basic.tests4;
        }
    }


	
    server {
    listen       80;
    server_name  wx.test2.zhongjiaxin.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;

    location /waiwei{
        root   /root/web;
        index  index.html index.htm;
    }

    location /waiwei/wechat {
            client_max_body_size 100m;
            proxy_connect_timeout 4;
            proxy_send_timeout 1200;
            proxy_pass http://10.33.80.113:8063/wechat;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE-HOST $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}	


server {
    listen       8061;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;

    location / {
    root   /root/web/collection-front;
    index  index.html index.htm;
    }

    location /collection {
            client_max_body_size 100m;
            proxy_connect_timeout 4;
            proxy_send_timeout 1200;
            proxy_pass http://10.33.80.113:8062/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE-HOST $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}


server {
        listen     80;
        server_name customer.test2.zhongjiaxin.com;

        location /customer {
            client_max_body_size 100m;
            proxy_connect_timeout 4;
            proxy_send_timeout 1200;
            proxy_pass http://10.33.80.113:8084;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE-HOST $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        } 
         
    }

server {
        listen     80;
        server_name neiwei.test2.zhongjiaxin.com;

        location / {
            client_max_body_size 100m;
            proxy_connect_timeout 4;
            proxy_send_timeout 1200;
            proxy_pass http://10.33.80.113:8099;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE-HOST $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

    }


 server {
                listen       8085;
                server_name  localhost;
                location / {
                root   /root/web/user-manage-front;
                 index  index.html index.htm;
    }
}

server {
                listen       7082;
                server_name  localhost;
                location / {
                root   /root/web/basic-manage-front;
                 index  index.html index.htm;
                }
                 location /api/ {
                  add_header 'Access-Control-Allow-Origin' '*';
                  proxy_pass http://10.33.80.113:8086/;
               }

}

 server {
         listen       8082;
         server_name  localhost;
         location / {
             root   /root/web/basic-manage-front;
             index  index.html index.htm;
                    }
	location /api/ {
        add_header 'Access-Control-Allow-Origin' '*';
        proxy_pass http://10.33.80.113:9081/;
        proxy_read_timeout 1200;

        }} 




upstream  www.gataway.com
{
    server   10.33.80.113:7030;
   # server   10.0.8.193:7030;
}

server {        
      listen       9896;         
      server_name  localhost;  
   
      location /{             
             root   /root/web/bi;             
             index  index.html index.htm;         
      }  
                
      location /biSys {             
            client_max_body_size 100m;             
            proxy_connect_timeout 1200;             
            proxy_send_timeout 1200;             
            proxy_read_timeout 1200;             
            root /home/ralf/tmp/biSys;             
            proxy_pass http://127.0.0.1:9898;             
            proxy_set_header Host $host;            
            proxy_set_header X-Real-IP $remote_addr;             
            proxy_set_header REMOTE-HOST $remote_addr;             
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;         
        }     
     }

server {
    listen       8020;
    server_name  localhost;
    location / {
        root   /root/web/post-loan-front;
        index  index.html index.htm;
    }

	location /api/ {
        add_header 'Access-Control-Allow-Origin' '*';
        proxy_pass http://10.33.80.113:7030/;
         proxy_read_timeout 1200;
    }


}

}