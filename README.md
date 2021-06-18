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
* 为了方便演示，我使用自己的github来做测试，关于对接gitlab的话可以参考：https://www.cnblogs.com/yueminghai/p/12929048.html 进行配置
