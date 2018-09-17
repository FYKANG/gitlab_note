# GitLab笔记

## 说明
1. 环境：CentOS 7 86x_64 
2. 使用[docker](https://github.com/FYKANG/docker_note)进行安装

## 安装
1. 镜像选择
    ```linux
    docker search gitlab #搜索镜像
    ```
    ![搜索镜像](https://github.com/FYKANG/gitlab_note/raw/master/img/serarch_gitlab.png)
2. 下载镜像
    ```linux
    docker pull docker.io/gitlab/gitlab-ce #以第一个为例
    ```
3. 运行容器
    ```linux
    docker run --name='gitlab-ce' -d -p 10022:22 -p 80:80 --restart always --volume /opt/gitlab/config:/etc/gitlab --volume /opt/gitlab/logs:/var/log/gitlab --volume /opt/gitlab/data:/var/opt/gitlab/ gitlab/gitlab-ce
    ```
    * 参数说明
        * --name="nginx-lb":为容器指定一个名称
        * -d:后台运行容器，并返回容器ID
        * -p:指定映射端口号，本文是将ssh的22端口映射为10022端口，web访问的80端口映射为80端口
        * -volume: 用来指定挂载目录，将config配置目录、data数据目录、logs日志目录挂载到宿主机上
4. 配置gitlab.rb
    * 配置文件路径
        ```linux
        vim /opt/gitlab/config/gitlab.rb #对应容器中的/etc/gitlab在运行容器的时候已经把这个文件挂载到宿主机中的可以直接在这个目录下配置
        ```
    * 配置的内容
        ```
        ##web访问验证的时候访问这个地址
        external_url 'http://服务器地址:端口' 
        ##ssh配置
        gitlab_rails['gitlab_ssh_host'] = '服务器地址'
        gitlab_rails['gitlab_shell_ssh_port'] = 端口
        ```
        ![gitlabHome](https://github.com/FYKANG/gitlab_note/raw/master/img/gitlab_rb.png)
5. 使配置信息生效
    * 进入容器
        ```linux
        docker exec -it gitlab-ce bash
        ```
    * 配置信息更新
        ```linux
        gitlab-ctl reconfigure
        ```
    * 退出容器
        ```linux
        eixt #此操作容器不会退出运行状态
        ```
6. web访问验证
    * 访问刚刚在gitlab.rb中配置的external_url地址
    * 第一次访问需要进行root密码初始化，初始化好密码后进入的首页如下图
    ![gitlabHome](https://github.com/FYKANG/gitlab_note/raw/master/img/gitlabHome.png)

## 其他配置以及常用命令

1. 配置
    * 时区设置
        ```linux
        docker exec -it gitlab-ce bash #进入容器
        dpkg-reconfigure tzdata #选择时区
        ```

2. 常用命令
    ```linux
    gitlab-ctl tail #查看日志
    ```