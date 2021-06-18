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

<br/>
![image](https://user-images.githubusercontent.com/50992676/122519575-31b1f880-d045-11eb-9f9f-48b31a715433.png)
![image](https://user-images.githubusercontent.com/50992676/122519589-35de1600-d045-11eb-85b5-054a14075979.png)

*  之后点击连接测试是否连接成功,上述安装步骤走完以后，我们就来安装我们所需要的一些插件，因为我们部署前端的项目是需要node环境的，所以我们要在jenkins的插件管理里安装一个nodejs插件
点击系统管理->插件管理

![image](https://user-images.githubusercontent.com/50992676/122519739-61f99700-d045-11eb-8464-901fabefc22e.png)

*  在可选插件里搜索nodejs
 
![image](https://user-images.githubusercontent.com/50992676/122519779-6c1b9580-d045-11eb-86b0-7eae221cc720.png)

* 安装好以后重启一下jenkins，重启之后在系统设置-》全局工具配置中配置node

![image](https://user-images.githubusercontent.com/50992676/122519973-aa18b980-d045-11eb-9fc1-d81f39ba25d4.png)



