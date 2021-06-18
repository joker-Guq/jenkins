# Jenkins+git+nginx 自动化部署前端项目

# 1.安装部署jenkins
*  这里我使用的是自己的linux服务器，我们以此为例进行安装, 由于jenkins基于java，
    所以我们需要先安装java的环境(这里的版本根据需求安装)：
```sh
yum install java-1.8.0-openjdk*
```
* ### 安装maven：
```sh
wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
```
```sh
yum -y install apache-maven
```
* ### 在安装完java基础的环境后，我们开始安装jenkins
```sh
wget https://pkg.jenkins.io/redhat/jenkins-2.230-1.1.noarch.rpm   
```
```sh
rpm -ivh jenkins-2.230-1.1.noarch.rpm
```
```sh
vi /etc/sysconfig/jenkins     //修改默认端口 默认是8080
```
* ### 安装修改完成后，我们可以启动jenkins
```sh
# systemctl start jenkins
```
* 这时候打开浏览器可以输入 http://服务器地址ip+/8080 
![image](https://user-images.githubusercontent.com/50992676/122517815-0c23ef80-d043-11eb-8f1a-2bd8094bf81f.png)
* 可以看到欢迎界面，根据页面提示路径 cat上面地址 查看第一次进入的密码，输入并以继续，接下来选择安装推荐插件
![image](https://user-images.githubusercontent.com/50992676/122518151-6e7cf000-d043-11eb-851a-9fda766b0029.png)

* 之后可以创建一个管理员账号，后续登陆使用设置的账号密码登陆即可


![image](https://user-images.githubusercontent.com/50992676/122518345-a8e68d00-d043-11eb-9229-3c7da134a868.png)


* 接下来 就会看到这个页面 表示安装完成


![image](https://user-images.githubusercontent.com/50992676/122518391-b69c1280-d043-11eb-95fd-69f9b0f60daf.png)


# 2.github的配置
* 为了方便演示，我使用自己的github来做测试，关于对接gitlab的话可以参考[jenkins配置gitlab](https://www.cnblogs.com/yueminghai/p/12929048.html), 先对github配置，首先登陆你的github账号依次点击右上角头像:
```sh
Settings->Developer settings->Personal access tokens->Generate new token
```
![image](https://user-images.githubusercontent.com/50992676/122518962-7be6aa00-d044-11eb-84e6-1cef108bb4c1.png)

*  然后对Note命名为jenkins，勾选下图两项<br/>


![image](https://user-images.githubusercontent.com/50992676/122519027-8e60e380-d044-11eb-81d3-79d49c4eff13.png)

*  之后点击确定，第一次创建完成的后会在列表显示token的值，复制下来，如果忘记复制可以点击Regenerate token，会重新显示<br/>
![image](https://user-images.githubusercontent.com/50992676/122519054-991b7880-d044-11eb-98b8-f9d7cad6c131.png)


# 3.Jenkins配置

* 依次点击系统管理->系统设置，找到GitHub，添加GitHub服务器，其中API URL是固定的，输入名称，点击添加，添加一个凭据。


![image](https://user-images.githubusercontent.com/50992676/122519196-c49e6300-d044-11eb-8dc2-2121cac590d4.png)
*  类型选择Secret text，Secret输入刚才复制的tonken，描述随意写，就是一个别名。


![image](https://user-images.githubusercontent.com/50992676/122519575-31b1f880-d045-11eb-9f9f-48b31a715433.png)
![image](https://user-images.githubusercontent.com/50992676/122519589-35de1600-d045-11eb-85b5-054a14075979.png)

*  之后点击连接测试是否连接成功,上述安装步骤走完以后，我们就来安装我们所需要的一些插件，因为我们部署前端的项目是需要node环境的，所以我们要在jenkins的插件管理里安装一个nodejs插件
点击系统管理->插件管理

![image](https://user-images.githubusercontent.com/50992676/122519739-61f99700-d045-11eb-8464-901fabefc22e.png)

*  在可选插件里搜索nodejs
 
![image](https://user-images.githubusercontent.com/50992676/122519779-6c1b9580-d045-11eb-86b0-7eae221cc720.png)

* 安装好以后重启一下jenkins，重启之后在系统设置-》全局工具配置中配置node

![image](https://user-images.githubusercontent.com/50992676/122519973-aa18b980-d045-11eb-9fc1-d81f39ba25d4.png)


* 下拉找到Nodejs 新增node，选择版本

![image](https://user-images.githubusercontent.com/50992676/122521672-b271f400-d047-11eb-9f3f-508f49d7fb40.png)


# 4.新建Jenkins任务

* 现在让我们来新建一个任务，输入名称，选择构建一个自由风格的软件项目

![image](https://user-images.githubusercontent.com/50992676/122521966-0250bb00-d048-11eb-90df-4413462de7ef.png)

* 输入描述，写上你的git地址

![image](https://user-images.githubusercontent.com/50992676/122522001-0da3e680-d048-11eb-892b-d7afac0f1ef4.png)


* 输入描述，填写你的git地址


![image](https://user-images.githubusercontent.com/50992676/122522081-23b1a700-d048-11eb-8892-f165a25f2bd6.png)

### 源码管理
* 这里输入的url是以.git结尾的，就是你克隆项目是使用的url。
* 之后点击添加，选择账号密码，输入即可。
* 默认分值为master，在你的github上切一个test分支，并指定构建test分支

![image](https://user-images.githubusercontent.com/50992676/122522361-77bc8b80-d048-11eb-9233-b44571499575.png)


* 构建环境：选择nodejs，刚才在全局配置工具中配置的

![image](https://user-images.githubusercontent.com/50992676/122522391-82772080-d048-11eb-8c7e-051f53e8b2c8.png)


* 执行shell 这里我用的是我目前简单构建用到的命令

![image](https://user-images.githubusercontent.com/50992676/122522421-8c991f00-d048-11eb-9505-7fd0a10d7dfa.png)


* npm install 后面多了一串命令 --unsafe-perm ,这里为什么用到这个我解释一下，这是我部署的时候发现的一个问题，我用npm install安装的时候，会构建失败提示我 npm无权限写入

![image](https://user-images.githubusercontent.com/50992676/122522503-9de22b80-d048-11eb-9a42-649041405bee.png)


* 我在服务器上查看了下读写权限，并全部改写了最高权限，发现还是不行，最后找到了问题：
npm 出于安全考虑不支持以 root 用户运行，即使你用 root 用户身份运行了，npm 会自动转成一个叫 nobody 的用户来运行，而这个用户几乎没有任何权限。这样的话如果你脚本里有一些需要权限的操作，比如写文件（尤其是写 /root/.node-gyp），就会崩掉了。
为了避免这种情况，要么按照 npm 的规矩来，专门建一个用于运行 npm 的高权限用户；要么加 –unsafe-perm 参数，这样就不会切换到 nobody 上，运行时是哪个用户就是哪个用户，即使是 root。
经常是安装node-sass的时候会很容易出现因为权限不够无法创建目录的问题

![image](https://user-images.githubusercontent.com/50992676/122522719-d71a9b80-d048-11eb-95fc-64e3f71658b4.png)

* 这里我也是经历了数次的构建失败：


![image](https://user-images.githubusercontent.com/50992676/122522757-e26dc700-d048-11eb-8978-186c06d3c7ff.png)



* 好了shell这一块填写完后 你可以再加一个ls 列出目录简单的测试下 你构建后的结果,这个时候我们就要回到标题自动化了，现在就加上自动化构建，即检测到代码push，jenkins自动构建项目。

# 5.代码热更新

* 首先我们去GitHub找到当前项目的

```sh
Settings->Webhooks->Add webhook
```

![image](https://user-images.githubusercontent.com/50992676/122523083-3b3d5f80-d049-11eb-81ac-84d98f7791c1.png)

* 输入的url为，其中/github-webhook/是固定的

```sh
http://你的ip地址:8080/github-webhook/
```
![image](https://user-images.githubusercontent.com/50992676/122523183-54dea700-d049-11eb-81c5-589075db745a.png)


* 点击确定后返回jenkins找到刚才的任务，点击配置

![image](https://user-images.githubusercontent.com/50992676/122523215-5e680f00-d049-11eb-84a3-4f02baf25275.png)


* 勾选之后修改shell命令，意思为打包后把dist文件夹移动到 /root/testweb/目录下，并更名为 jenkinsPro

```sh
npm run build;
```
```sh
mv dist  /home/web/jenkinsPro
```
* 至此可以构建一下，成功后查看代码是否构建至指定目录，这时候再配合nginx,就完全可以实现我们的自动化构建了

# 6.jenkins构建代码至远端服务器

以上的操作仅仅是在jenkins所在服务器构建我们代码，接下来就是如何构建代码至其他服务器上面去，首先给我们的jenkins安装两个插件：
publish over ssh 用于连接远程服务器
Deploy to container  用于把打包的应用发布到远程服务器
还是一样的打开插件管理，可选插件，搜索对应插件安装，这个插件有很多，实现的方式也不同，例如公司的jenkis使用的是ssh remote的一个插件。这里的话我采用我比较习惯的方式来演示。

* 安装好以后还是老规矩去我们的系统管理配置下拉，找到publish over ssh

![image](https://user-images.githubusercontent.com/50992676/122524673-003c2b80-d04b-11eb-9b86-a6fbe8cbb9d3.png)


* Jenkins SSH Key：jenkins服务器ssh秘钥

* 查看服务器是否存在ssh秘钥文件:

```sh
    ll ~/.ssh
```

如果服务器已经生成过ssh秘钥文件，目录下会有：id_rsa.pub、id_rsa文件。
如果不存在ssh秘钥文件，使用下面命令生成即可

```sh
    ssh-keygen -t rsa -C "username". //# username一般设置为你的邮箱地址
```

* 查看公钥文件，复制内容添加至SSH Server服务器~/.ssh/authorized_keys文件中,这个地方是把jenkins服务器公钥要添加至你构建目标服务器的authorized_keys里


* authorized_keys是什么呢？

      我们需要本地机器ssh访问远程服务器时为了减少输入密码的步骤，基本上都会在本地机器生成ssh公钥，
      然后将本地ssh公钥复制到远程服务器的.ssh/authorized_keys中，这样就可以免密登录了。（ 服务器之间访问同理）
      查看秘钥文件，复制内容至 Key 选项输入框
      如果在添加ssh秘钥文件时设置了密码也需要配置Passphrase、Path to key选项
      设置SSH Server服务器Name、Hostname、Username、Remote Directory
      设置完成后点击：Test Configuration按钮，出现：Success表示设置成功
      
 * 接下来去任务的配置修改我们的构建参数：

![image](https://user-images.githubusercontent.com/50992676/122525606-fcf56f80-d04b-11eb-9b23-682d13cdd8ac.png)

![image](https://user-images.githubusercontent.com/50992676/122525661-0bdc2200-d04c-11eb-85a0-deff8a6284ad.png)

        
    Name是我们之前在系统配置里服务器，自动带入的

    Source files     项目构建后的目录 dist/** 匹配的是dist下所有文件包括文件夹

    Remove prefix    去前缀 这里不填写的话 构建后扔过去的就是包含dist的完整文件夹

    Remote directoty  测试发布的目录 

    Exec command     发布完执行的命令
   
   
 * 保存以后，我们再次构建项目

![image](https://user-images.githubusercontent.com/50992676/122525850-3e861a80-d04c-11eb-8e33-412189e77ac6.png)


* 输出成功以后，我们登陆目标服务器去查看下

![image](https://user-images.githubusercontent.com/50992676/122525903-4b0a7300-d04c-11eb-91ed-031ac6fa2f7e.png)


        可以看到我们的代码成功构建到了这里。至此构建代码至其他服务器的一个流程也完成了。
        现在我们每次提交代码至规定的分支，都会自动触发构建至我们的类似测试环境或者开发联调环境。
        接下来就要配合nginx配合完成我们前端项目的部署了。


# Nginx部署前端项目

* Nginx 是一个高性能的HTTP和反向代理web服务器,何为反响代理，作为前端开发的同学还是有必要了解一下:


![image](https://user-images.githubusercontent.com/50992676/122526366-ca984200-d04c-11eb-847a-c489ad12a496.png)



在客户端配置代理服务器，通过代理服务器进行互联网访问。代理对象是客户端，不知道服务端是谁。我们举个最直接的正向代理的例子，比如我们要访问国外的网站，速度很慢，甚至访问不到，这个时候我们就需要翻墙，使用vpn正向代理。并且vpn是在我们的用户端设置的(并不是在远端的服务器设置)。浏览器先访问vpn地址，vpn地址转发请求，并最后将请求结果原路返回来。


![image](https://user-images.githubusercontent.com/50992676/122526381-cd933280-d04c-11eb-8767-144c85f47ac7.png)



客户端不需要任何配置就能访问，只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器，获取数据后在返回给客户端。对外就一个服务器，暴露的是反向代理服务器地址，隐藏了真实服务器IP地址。代理对象是服务端，不知道客户端是谁。反向代理是作用在服务器端的，是一个虚拟ip(VIP)。对于用户的一个请求，会转发到多个后端处理器中的一台来处理该具体请求。


### 1.nginx的配置文件目录

![image](https://user-images.githubusercontent.com/50992676/122526431-ddab1200-d04c-11eb-8ea3-e1f0a2f9e380.png)



*  /etc/nginx/nginx.conf 核心配置文件
*  /etc/nginx/conf.d/default.conf 默认http服务器配置文件
*  /etc/nginx/fastcgi_params fastcgi配置
*  /etc/nginx/scgi_params scgi配置
*  /etc/nginx/uwsgi_params uwsgi配置
*  /etc/nginx/koi-utf
*  /etc/nginx/koi-win
*  /etc/nginx/win-utf 这三个文件是编码映射文件，因为作者是俄国人
*  /etc/nginx/mime.types 设置HTTP协议的Content-Type与扩展名对应关系的文件
*  /usr/lib/systemd/system/nginx-debug.service
*  /usr/lib/systemd/system/nginx.service
*  /etc/sysconfig/nginx
*  /etc/sysconfig/nginx-debug 这四个文件是用来配置守护进程管理的
*  /etc/nginx/modules 基本共享库和内核模块
*  /usr/share/doc/nginx-1.18.0 帮助文档
*  /usr/share/doc/nginx-1.18.0/COPYRIGHT 版权声明
*  /usr/share/man/man8/nginx.8.gz 手册
*  /var/cache/nginx Nginx的缓存目录
*  /var/log/nginx Nginx的日志目录
*  /usr/sbin/nginx 可执行命令
*  /usr/sbin/nginx-debug 调试执行可执行命令

### 2.nginx核心模块

nginx的核心配置文件nginx.conf主要由3个部分组成:

#### 基本配置

```makefile
#user  nobody;                  #配置worker进程运行用户

worker_processes  1;            #配置工作进程数目,根据硬件调整，通常等于CPU数量或者2倍于CPU数量

#error_log  logs/error.log;     # 配置全局错误日志及类型
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;     #配置进程pid文件
```

#### events配置

```makefile
    events {

        # use epoll;  #事件处理模型优化

        worker_connections  1024;  #配置每个worker进程连接数上限，nginx支持的总连接数等于worker_connections*worker_processes
   }
```

#### http配置，基本配置

```makefile
http {
    include       mime.types;
    default_type  application/octet-stream;
    client_max_body_size 100m;

    sendfile        on;    #开启高效文件传输模式
    #cp_nopush     on;  #防止网络阻塞

    keepalive_timeout  0;
    
    gzip on; //启动
    gzip_buffers 32 4K;
    gzip_comp_level 6; //压缩级别，1-10，数字越大压缩的越好
    gzip_min_length 100; //不压缩临界值，大于100的才压缩，一般不用改
    gzip_types application/javascript text/css text/xml;
    gzip_disable "MSIE [1-6]\."; // IE6对Gzip不友好，对Gzip
    gzip_vary on;
  }
```
- `client_max_body_size`表示 客户端请求服务器最大允许大小,如果请求正文数据大于设定值，HTTP协议会报413错误 `Request Entity Too Large`，如果需要上传大文件是需要修改这个值的。
  关于这部分具体内容如果有兴趣的可以看这篇[文献](https://www.cnblogs.com/shixinlong/p/13914985.html)
- `keepalive_timeout` :长连接超时时间，单位是秒,`Nginx `使用 `keepalive_timeout` 来指定 `KeepAlive` 的超时时间（timeout）。指定每个 TCP 连接最多可以保持多长时间。
  Nginx 的默认值是 75 秒，有些浏览器最多只保持 `60`秒，所以可以设定为 `60` 秒。若将它设置为 0，就禁止了 keepalive 连接。做好这些超时时间的限定，
  判定超时后资源被释放，用来处理其他的请求，以此提升 `Nginx` 的性能
  
- `gzip`:在不设置服务器gzip的情况下，我们访问网站

![image](https://user-images.githubusercontent.com/50992676/122529785-6b3c3100-d050-11eb-9ef6-4ea7923be359.png)


设置了gzip后访问情况如下

![image](https://user-images.githubusercontent.com/50992676/122529838-78592000-d050-11eb-912f-f94c5182f1ae.png)


- 经过对比，我们发现，设置gzip之后，获取同样的数据。压缩之后的数据量大概是原始数据的1/4。同样的，获取数据的时间也大大降低。极大的优化了用户的体验。

#### Server配置

```makefile
server {
        listen       80;   #配置监听端口
        server_name  localhost;  #配置服务名
     
        root /home/test; #root根目录
        
        ##默认的匹配斜杠/的请求，当访问路径中有/，会被该localtion匹配到并进行处理
       location / {
                try_files $uri $uri/ /index.html;  
    	  }
          
 	   location /api {  
        	proxy_pass http:url;  #接口的代理地址
    	 }


        error_page  404  /404.html;
        #可显示自定义404页面内容，正常返回404状态码

        error_page 404 =  /404.htmml
        #可显示自定义404页面内容，但返回200状态码。
     }
```
```js
location / {
                try_files $uri $uri/ /index.html;  
    	  }
```

- 以上这段代码主要是为了解决vue项目history模式下刷新页面出现404的问题，一般我们认为启用history模式只是去掉地址栏的#符号，然后`Nginx`服务器为了实现这个效果需要添加一段代码用于支持该效果，给`Nginx`添加这段代码后，我们可以直接在地址栏输入路由链接，从而进入到对应的路由页面，如果没有使用该段代码，地址栏的#号依旧会消失，但是你无法通过直接输入路由地址直接进入到对应的页面。主要原因是路由的路径资源并不是一个真实的路径，所以无法找到具体的文件因此需要`rewrite`到index.html中，然后交给路由在处理请求资源


- `error_page`在一次请求中只能响应一次，对应的`Nginx`有另外一个配置可以控制这个选项：`recursive_error_pages`默认为false，作用是控制error_page能否在一次请求中触发多次。
####  适配PC与移动环境


- 当用户从移动端打开PC端的场景时，将自动跳转指移动端m.anchnet.com，本质上是Nginx可以通过内置变量$http_user_agent，获取到请求客户端的userAgent，从而知道当前用户当前终端是移动端还是PC，进而重定向到H5站还是PC站 
```makefile
    server {
         location / {
                //移动、pc设备agent获取

                if ($http_user_agent ~* '(Android|webOS|iPhone)') {
                    set $mobile_request '1';
                }
                if ($mobile_request = '1') {
                    rewrite ^.+ http://m.anchnet.com;
                }
            } 
        }
```
#### Nginx配置https

```makefile
    server {
       #ssl参数
       listen              443 ssl; //监听443端口，因为443端口是https的默认端口。80为http的默认端口
       server_name         example.com;
       #证书文件
       ssl_certificate     example.com.crt;
       #私钥文件
       ssl_certificate_key example.com.key;
    }
```

#### nginx 端口映射多个应用

```makefile
    server {
    listen       8000;
    location / {
        proxy_pass http://http://65.49.218.66/:9999/;
        index  index.html index.htm;
    }
    location /cdh {
        proxy_pass http://65.49.218.66/:4690/;
        index  index.html login.html;
    }
    location /tomcat {
        proxy_pass http://http://65.49.218.66/:8282/;
        index  index.html index.htm;
    }
}
```
