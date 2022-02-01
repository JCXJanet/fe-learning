## Nginx

1）正向代理：

所谓正向代理就是内网服务器主动要去请求外网的地址或服务，所进行的一种行为。内网服务---访问--->外网

server端：

应用在nginx的server段，不要server_name,需要添加一个resolver。

```
server {
    # 配置DNS解析IP地址，比如 Google Public DNS，以及超时时间（5秒）
    resolver 8.8.8.8;    # 必需
    resolver_timeout 5s;

    # 监听端口
    listen 8080;

    access_log  /home/reistlin/logs/proxy.access.log;
    error_log   /home/reistlin/logs/proxy.error.log;

    location / {
        # 配置正向代理参数
        proxy_pass $scheme://$host$request_uri;
        # 解决如果URL中带"."后Nginx 503错误
        proxy_set_header Host $http_host;

        # 配置缓存大小
        proxy_buffers 256 4k;
        # 关闭磁盘缓存读写减少I/O
        proxy_max_temp_file_size 0;
         # 代理连接超时时间
        proxy_connect_timeout 30;

        # 配置代理服务器HTTP状态缓存时间
        proxy_cache_valid 200 302 10m;
        proxy_cache_valid 301 1h;
        proxy_cache_valid any 1m;
    }
}
```

client端：

一次代理，直接在shell执行：

```
#export http_proxy=http://192.168.1.9：8080
```

永久使用：

```
#vim .bashrc

export http_proxy=http://192.168.1.9：8080

#source  .bashrc
```

2）反向代理：

所谓反向代理就是外网要访问内网服务而进行的一种行为。 外网----请求--->内网服务

proxy_pass 代理

反向代理的相关配置需要在 Location 模块中进行配置，把匹配的请求进行反向代理，转发到 proxy_pass 配置的服务器进行处理。

此配置项将当前请求反向代理到 URL 参数指定的服务器上，URL可以是主机名或IP地址加端口的形式。proxy_pass URL;

```
proxy_pass URL;

# example

location /uewx/ {
index index.jsp index.html index.php;
proxy_pass http://192.168.119.62:8080/ConfigService/uewx/;
}
```

这里的IP地址 192.168.119.62 是我本地的局域网IP，通过在我的测试服务器的Nginx配置中加上这一句，就可以把外网进来的 /uewx 的请求转发到我本地的Tomcat服务器进行处理，真的非常方便。

通过上面例子简单的 proxy_pass 配置，就可以把 /uewx 的请求转发到 http://192.168.119.62:8080/ConfigService/uewx/ 进行处理，在进行配置的过程中，要注意 URL 后面的斜杠。

针对斜杠问题，下面针对访问地址： http://test.local_nginx.com/proxy/test.html 进行说明：

```
location /proxy/ {
    proxy_pass http://192.168.119.62/;
}
# 代理到 http://192.168.119.62/test.html

location /proxy/ {
    proxy_pass http://192.168.119.62;  # 代理URL没有/
}
# 代理到 http://192.168.119.62/proxy/test.html

location /proxy/ {
    proxy_pass http://192.168.119.62/proxy/; # 代理URL包含匹配，包含/
}
# 代理到 http://192.168.119.62/proxy/test.html

location /proxy/ {
 proxy_pass http://192.168.119.62/proxy; # 代理URL包含匹配，缺少/
}
# 代理到 http://192.168.119.62/proxytest.html
```

这里的URL还可以结合 upstream 负载均衡进行配置：

# 负载均衡配置

```
upstream backend { 
    server backend1.com  weight=5; 
    server backend2.com:8080;
} 

server { 
   location / { 
    proxy_pass http://backend; 
   } 
}
```

默认情况下反向代理是不会转发请求中的Host头部的。如果需要转发，那么必须加上配置：

```
proxy_set_header Host ParseError: KaTeX parse error: Expected 'EOF', got '#' at position 9: host;  
#̲ 下面的配置用于向后台服务器转…remote_addr; 
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

## 其他可选代理配置

Nginx的反向代理模块提供了很多种配置，如设置转发请求过程中的行为、连接的超时时间、临时文件如何存储，以及最重要的如何缓存上游服务器响应等功能。以下的配置可以放在：http、server、location模块。

Nginx反向代理的配置可以参考官方文档：[Module ngx_http_proxy_module](http://nginx.org/en/docs/http/ngx_http_proxy_module.html) 下面介绍一些常用的配置。

### **proxy_method**

语法：proxy_method method;

此配置项表示转发时的协议方法名。GET请求在转发时方法名会改为POST。

```
proxy_method POST;
```

### **proxy_hide_header**

语法：proxy_hide_header the_header;

Nginx会将上游服务器的响应转发给客户端，但默认不会转发以下HTTP头部字段：Date、Server、X-Pad和X-Accel-*。使用proxy_hide_header后可以任意地指定哪些HTTP头部字段不能被转发。例如：

```
proxy_hide_header Cache-Control; 
proxy_hide_header MicrosoftOfficeWebServer;
```

### **proxy_pass_header**

语法：proxy_pass_header the_header;

与proxy_hide_header功能相反，proxy_pass_header会将原来禁止转发的header设置为允许转发。例如：

```
proxy_pass_header X-Accel-Redirect;
```

### **proxy_pass_request_body**

语法：proxy_pass_request_body on | off;

默认：proxy_pass_request_body on;

作用为确定是否向上游服务器发送HTTP包体部分。

### **proxy_pass_request_headers**

语法：proxy_pass_request_headers on | off;

默认：proxy_pass_request_headers on;

作用为确定是否转发HTTP头部。

### **proxy_redirect**

语法：proxy_redirect [ default|off|redirect replacement ];

默认：proxy_redirect default;

当上游服务器返回的响应是重定向或刷新请求（如HTTP响应码是301或者302）时，proxy_redirect可以重设HTTP头部的location或refresh字段。

例如，如果上游服务器发出的响应是302重定向请求，location字段的URL是：http://localhost:8000/two/some/uri/,那么在下面的配置情况下，实际转发给客户端的location是http://frontend/one/some/uri/。

```
proxy_redirect http://localhost:8000/two/ http://frontend/one/;
```

这里还可以使用ngx-http-core-module提供的变量来设置新的location字段。例如

```
proxy_redirect   http://localhost:8000/    http://host:server_port/;
```

也可以省略replacement参数中的主机名部分，这时会用虚拟主机名称来填充。例如：

```
proxy_redirect http://localhost:8000/two/ /one/;
```

使用off参数时，将使location或者refresh字段维持不变。例如：

```
proxy_redirect off;
```

使用默认的default参数时，会按照proxy_pass配置项和所属的location配置项重组发往客户端的location头部。例如，下面两种配置效果是一样的：

```
location /one/ { 

 proxy_pass       http://upstream:port/two/; 

 proxy_redirect default; 

 } 

 location /one/ { 

 proxy_pass       http://upstream:port/two/; 

 proxy_redirect   http://upstream:port/two/   /one/; 

}
```

### **proxy_next_upstream**

语法：proxy_next_upstream [error | timeout | invalid_header | http_500 | http_502 | http_503 | http_504 | http_404 | off ];

默认：proxy_next_upstream error timeout;

此配置项表示当向一台上游服务器转发请求出现错误时，继续换一台上游服务器处理这个请求。

上游服务器一旦开始发送应答，Nginx反向代理服务器会立刻把应答包转发给客户端。因此，一旦Nginx开始向客户端发送响应包，之后的过程中若出现错误也是不允许换下一台上游服务器继续处理的。这很好理解，这样才可以更好地保证客户端只收到来自一个上游服务器的应答。proxy_next_upstream的参数用来说明在哪些情况下会继续选择下一台上游服务器转发请求。

error：当向上游服务器发起连接、发送请求、读取响应时出错。

timeout：发送请求或读取响应时发生超时。

invalid_header：上游服务器发送的响应是不合法的。

http_500：上游服务器返回的HTTP响应码是500。

http_502：上游服务器返回的HTTP响应码是502。

http_503：上游服务器返回的HTTP响应码是503。

http_504：上游服务器返回的HTTP响应码是504。

http_404：上游服务器返回的HTTP响应码是404。

off：关闭proxy_next_upstream功能—出错就选择另一台上游服务器再次转发。

### cors配置

```
#
# Wide-open CORS config for nginx
#
location / {
     if ($request_method = 'OPTIONS') {
        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
        #
        # Custom headers and headers various browsers *should* be OK with but aren't
        #
        add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
        #
        # Tell client that this pre-flight info is valid for 20 days
        #
        add_header 'Access-Control-Max-Age' 1728000;
        add_header 'Content-Type' 'text/plain; charset=utf-8';
        add_header 'Content-Length' 0;
        return 204;
     }
     if ($request_method = 'POST') {
        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
        add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
        add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';
     }
     if ($request_method = 'GET') {
        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
        add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
        add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';
     }
}
```

### Nginx 负载均衡详解

在文章[Nginx 配置详解](https://www.runoob.com/w3cnote/nginx-setup-intro.html)中我说啦nginx有哪些中负载均衡算法。这一结我就给如何操作配置的给大家做详细说明下。

首先给大家说下upstream这个配置的，这个配置是写一组被代理的服务器地址，然后配置负载均衡的算法。这里的被代理服务器地址有两种写法。

```
upstream mysvr { 
    server 192.168.10.121:3333;
    server 192.168.10.122:3333;
}
server {
    ....
    location  ~*^.+$ {         
        proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表         
    }
}
```

然后，就来点实战的东西。

1、热备：如果你有2台服务器，当一台服务器发生事故时，才启用第二台服务器给提供服务。服务器处理请求的顺序：AAAAAA突然A挂啦，BBBBBBBBBBBBBB.....

```
upstream mysvr { 
    server 127.0.0.1:7878; 
    server 192.168.10.121:3333 backup;  #热备     
}
```

2、轮询：nginx默认就是轮询其权重都默认为1，服务器处理请求的顺序：ABABABABAB....

```
upstream mysvr { 
    server 127.0.0.1:7878;
    server 192.168.10.121:3333;       
}
```

3、加权轮询：跟据配置的权重的大小而分发给不同服务器不同数量的请求。如果不设置，则默认为1。下面服务器的请求顺序为：ABBABBABBABBABB....

```
upstream mysvr { 
    server 127.0.0.1:7878 weight=1;
    server 192.168.10.121:3333 weight=2;
}
```

4、ip_hash:nginx会让相同的客户端ip请求相同的服务器。

```
upstream mysvr { 
    server 127.0.0.1:7878; 
    server 192.168.10.121:3333;
    ip_hash;
}
```

5、如果你对上面4种均衡算法不是很理解，可以查看[Nginx 配置详解](https://www.runoob.com/w3cnote/nginx-setup-intro.html)，可能会更加容易理解点。

到这里你是不是感觉nginx的负载均衡配置特别简单与强大，那么还没完，咱们继续哈，这里扯下蛋。

关于nginx负载均衡配置的几个状态参数讲解。

- down，表示当前的server暂时不参与负载均衡。

- backup，预留的备份机器。当其他所有的非backup机器出现故障或者忙的时候，才会请求backup机器，因此这台机器的压力最轻。

- max_fails，允许请求失败的次数，默认为1。当超过最大次数时，返回proxy_next_upstream 模块定义的错误。

- fail_timeout，在经历了max_fails次失败后，暂停服务的时间。max_fails可以和fail_timeout一起使用。

```
upstream mysvr { 
    server 127.0.0.1:7878 weight=2 max_fails=2 fail_timeout=2;
    server 192.168.10.121:3333 weight=1 max_fails=2 fail_timeout=1;    
}
```
