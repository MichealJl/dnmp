# dnmp
怀着忐忑的心情上传了我的第一个github项目
刚接触Docker没多久，这个练手的 希望大家多多指教

在此之前先要了解docker一些基本用法 我学习docker的记录：https://jlmvp.cn/

首先确保你安装了docker和docker-compose以及git

目前只加了php5.6 和php7.2两个版本切换，如需扩展请自行照猫画虎

# 开始
### 1、将项目clone到本地 

### 2、进入dnmp将env-example 重命名为.env

### 3、配置env中你所需要设置的环境变量

### 4、在docker-compose.yml目录 执行docker-compose config 你可以看到完整配置信息
![Image text](https://github.com/MichealJl/dnmp/blob/master/images/14.jpg)

### 5、执行docker-compose up -d  （额。。安装php的那些扩展挺慢的 你可以酌情 修改php目录下的Dockerfile，等用的到那些扩展的时候 再装）**
![Image text](https://github.com/MichealJl/dnmp/blob/master/images/4.jpg)

安装成功之后显示如下
![Image text](https://github.com/MichealJl/dnmp/blob/master/images/5.jpg)

### 6、修改nginx的配置文件 nginx/conf.d/default.conf

![Image text](https://github.com/MichealJl/dnmp/blob/master/images/15.jpg)

**查看 容器php名称

 `docker ps | grep php |awk $'{print $2}'`

### 7、重启nginx服务

### 8、在你的项目目录下创建index.php 输出phpinfo();
![Image text](https://github.com/MichealJl/dnmp/blob/master/images/10.jpg)

**结尾提示 如果你使用了mysql8.0以上版本会报如下错误

connect error：The server requested authentication method unknown to the client

解决方法 进入mysql容器登录mysql

执行 

`ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';`

`flush privileges;`

# Gogs安装

执行docker-compose up -d

进入mysql容器创建Gogs数据库 CREATE DATABASE gogs CHARACTER SET utf8 COLLATE utf8_bin; 

打开网页输入你的IP地址:你设置的Gogs端口号 进入安装安装向导页面

设置参数

![Image text](https://github.com/MichealJl/dnmp/blob/master/images/11.jpg)

![Image text](https://github.com/MichealJl/dnmp/blob/master/images/12.jpg)

![Image text](https://github.com/MichealJl/dnmp/blob/master/images/13.jpg)

