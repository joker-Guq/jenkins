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



