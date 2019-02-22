# dnmp
刚接触Docker没多久，这个练手的 希望大家多多指教

在此之前先要了解docker一些基本用法 我学习docker的记录：https://jlmvp.cn/

首先确保你安装了docker和docker-compse以及git

# 开始
1、将项目clone到本地

2、将env-example 重命名为.env

3、配置env中你所需要设置的环境变量

3、在docker-compose.yml目录 执行docker-compose config 你可以看到完整配置信息
![Image text](https://github.com/MichealJl/dnmp/blob/master/1550834410.jpg)

4、执行docker-compose up -d

5、修改nginx的配置文件 nginx/conf.d/default.conf

6、重启nginx服务

