## 简介
在这个简单示例中，我们用到了PHP7.2、MySQL5.7、Nginx1.12、Redis3.2，以及Composer、Xcache扩展等。总的来说，整个项目做起来就3个流程：
1. 编写各个镜像的dockfile（包括相关扩展）；
2. 编写配置文件（类似于本地环境），我们将配置文件进行归类，PHP的配置文件放在PHP目录下，Nginx的配置放在Nginx目录下，至于要不要再新建一个子文件夹就看情况了，比如conf.d文件夹（域名配置文件）。特别注意的是，**配置文件中出现的路径是上下文环境（容器内环境路径），而不是宿主机路径**；
3. 编写docker-composer.yml，定义一组相关联的应用容器为一个项目（project）。

## Project Tree
```
app/
        index.php
    data/
        mysql/
        redis/
    dockerfiles/
        mysql/
            my.cnf
            Dockerfile
        nginx/
            conf.d/
                localhost.conf
            Dockerfile
            nginx.conf
        php/
            Dockerfile
            php.ini
            php-dev.ini
            php-fpm.conf
        redis/
            Dockerfile    
        docker-compose.yml
    logs/
        mysql/
        nginx/
        php-fpm/
    .gitgnore
    README.md
```
## 使用
1. 安装 Docker，Docker-compose
- Docker，详见官方文档：https://docs.docker.com/engine/installation/linux/docker-ce/centos/
- docker-compose，文档：https://docs.docker.com/compose/install/

2. docker-compose构建项目
进入docker-compose.yml所在目录，执行命令：
```
docker-compose up

#如果没问题，下次可以守护模式启动 docker-composer up -d
```
使用`docker-compose down`关闭容器并删除服务，更多命令参见[文档](https://yeasy.gitbook.io/docker_practice/compose/commands)。
3. 使用Composer
我们在创建 PHP-fpm 容器时就已经将 Composer 安装在容器中，可使用docker-compose进行操作：
```
docker-compose run --rm -w /data/www/lnmp php-fpm composer update

#`-w /data/www/`为在php-fpm的工作区域
```
或者进入宿主机app目录下使用docker命令：
```
$ cd app
$ docker run -it --rm -v `pwd`:/data/www/ -w /data/www/lnmp files_php-fpm composer update
```
