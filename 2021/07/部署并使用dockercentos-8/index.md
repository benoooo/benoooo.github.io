# 部署并使用Docker（CentOS 8）


# 部署并使用Docker（CentOS 8）

[本页目录](javascript:void(0))

- [前提条件](https://help.aliyun.com/document_detail/187598.html?spm=a2c4g.11186623.6.1313.11e52683kIycyR#h2-url-1)
- [背景信息](https://help.aliyun.com/document_detail/187598.html?spm=a2c4g.11186623.6.1313.11e52683kIycyR#h2-url-2)
- [部署Docker](https://help.aliyun.com/document_detail/187598.html?spm=a2c4g.11186623.6.1313.11e52683kIycyR#title-cq8-kwa-7g7)
- [使用Docker](https://help.aliyun.com/document_detail/187598.html?spm=a2c4g.11186623.6.1313.11e52683kIycyR#d6e131)
- [制作Docker镜像](https://help.aliyun.com/document_detail/187598.html?spm=a2c4g.11186623.6.1313.11e52683kIycyR#title-44z-bb7-p4s)

本文介绍如何在CentOS 8.1 64位操作系统的ECS实例上部署并使用Docker。适用于熟悉Linux操作系统，刚开始使用阿里云ECS的开发者。

## 前提条件

本教程示例步骤选用以下ECS实例配置：

- 实例规格：ecs.g6.large
- 操作系统：CentOS 8.1 64位
- 网络类型：专有网络VPC
- IP地址：公网IP

## 背景信息

本教程主要介绍以下内容：

- 部署Docker，详情请参见[部署Docker](https://help.aliyun.com/document_detail/187598.html?spm=a2c4g.11186623.6.1313.11e52683kIycyR#section-kjt-i59-n4a)。
- 使用Docker。
  - Docker的基本用法介绍，请参见[使用Docker](https://help.aliyun.com/document_detail/187598.html?spm=a2c4g.11186623.6.1313.11e52683kIycyR#section-kig-gpy-uti)。
  - 制作镜像的示例操作，请参见[制作Docker镜像](https://help.aliyun.com/document_detail/187598.html?spm=a2c4g.11186623.6.1313.11e52683kIycyR#section-o3m-mmi-0bz)。

## 部署Docker

本节主要介绍手动安装Docker的操作步骤，您也可以在[云市场](https://market.aliyun.com/software)购买相应镜像，一键部署云服务器。

1. 远程连接ECS实例。连接方式请参见[连接方式概述](https://help.aliyun.com/document_detail/71529.htm#concept-tmr-pgx-wdb)。

2. 运行以下命令，安装Docker存储驱动的依赖包。

   ```
   dnf install -y device-mapper-persistent-data lvm2
   ```

3. 运行以下命令，添加稳定的Docker软件源。

   ```
   dnf config-manager --add-repo=https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
   ```

4. 运行以下命令，查看已添加的Docker软件源。

   ```
   dnf list docker-ce
   ```

   正确的返回示例如下。

   ```
   docker-ce.x86_64        3:19.03.13-3.el7        docker-ce-stable
   ```

5. 运行以下命令安装Docker。

   ```
   dnf install -y docker-ce --nobest
   ```

6. 运行以下命令启动Docker。

   ```
   systemctl start docker
   ```

## 使用Docker

Docker有以下基本用法：

- 管理Docker守护进程。

  ```
  systemctl start docker     #运行Docker守护进程
  systemctl stop docker      #停止Docker守护进程
  systemctl restart docker   #重启Docker守护进程
  systemctl enable docker    #设置Docker开机自启动
  ```

- 管理镜像。本文使用的是来自阿里云仓库的Apache镜像。

  ```
  docker pull registry.cn-hangzhou.aliyuncs.com/lxepoo/apache-php5
  ```

  - 修改标签。由于阿里云仓库镜像的镜像名称较长，您可以修改镜像标签以便记忆区分。

    ```
    docker tag registry.cn-hangzhou.aliyuncs.com/lxepoo/apache-php5:latest aliweb:v1
    ```

  - 查看已有镜像。

    ```
    docker images
    ```

  - 强制删除镜像。

    ```
    docker rmi -f registry.cn-hangzhou.aliyuncs.com/lxepoo/apache-php5
    ```

- 管理容器。

  - 进入容器。e1abc****是执行

    ```
    docker images
    ```

    命令查询到的

    ImageId

    ，使用

    ```
    docker run
    ```

    命令进入容器。

    ```
    docker run -it e1abc**** /bin/bash
    ```

  - 退出容器。使用`exit`命令退出当前容器。

  - ```
    run
    ```

    命令加上

    ```
    –d
    ```

    参数可以在后台运行容器，

    ```
    --name
    ```

    指定容器命名为apache。

    ```
    docker run -d --name apache e1abc****
    ```

  - 进入后台运行的容器。

    ```
    docker exec -it apache /bin/bash
    ```

  - 查看容器ID。

    ```
    docker ps
    ```

  - 将容器做成镜像，命令的参数说明：

    ```
    docker commit <容器ID或容器名> [<仓库名>[:<标签>]]
    ```

    。

    ```
    docker commit containerID/containerName repository:tag
    ```

  - 为了方便测试和恢复，将源镜像运行起来后，再做一个命名简单的镜像做测试。

    ```
    docker commit 4c8066cd8**** apachephp:v1
    ```

  - 运行容器并将宿主机的8080端口映射到容器里去。

    ```
    docker run -d -p 8080:80 apachephp:v1
    ```

    在浏览器输入ECS实例IP地址加8080端口访问测试，出现以下内容则说明运行成功。

    **说明** ECS实例的安全组入方向规则需要放行8080端口，详情请参见[添加安全组规则](https://help.aliyun.com/document_detail/25471.htm#concept-sm5-2wz-xdb)。

    ![映射结果](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4712649951/p12348.png)

## 制作Docker镜像

1. 准备Dockerfile内容。

   1. 新建并编辑Dockerfile文件。

      ```
      vim Dockerfile
      ```

   2. 按i进入编辑模式，添加以下内容。

      ```
      #声明基础镜像来源
      FROM apachephp:v1
      #声明镜像拥有者
      MAINTAINER DTSTACK
      #RUN后面接容器运行前需要执行的命令，由于Dockerfile文件不能超过127行，因此当命令较多时建议写到脚本中执行
      RUN mkdir /dtstact
      #开机启动命令，此处最后一个命令需要是可在前台持续执行的命令，否则容器后台运行时会因为命令执行完而退出。
      ENTRYPOINT ping www.aliyun.com
      ```

   3. 按下键盘esc键，输入`:wq`并按下enter键，保存并退出Dockerfile文件。

2. 构建镜像。

   ```
   docker build -t webcentos8:v1 .    # . 是Dockerfile文件的路径，不能忽略
   docker images                                #查看是否创建成功
   ```

3. 运行容器并查看。

   ```
   docker run -d webcentos8:v1           #后台运行容器
   docker ps                             #查看当前运行中的容器
   docker ps -a                          #查看所有容器，包括未运行中的
   docker logs CONTAINER ID/IMAGE        #如未查看到刚才运行的容器，则用容器id或者名字查看启动日志排错
   ```

4. 制作镜像。

   ```
   docker commit fb2844b6**** dtstackweb:v1     #commit参数后添加容器ID和构建新镜像的名称和版本号。
   docker images                                #列出本地（已下载的和本地创建的）镜像
   ```

5. 将镜像推送至远程仓库。

   默认推送到Docker Hub。您需要先登录Docker，为镜像绑定标签，将镜像命名为`Docker用户名/镜像名:标签`的格式。最终完成推送。

   ```
   docker login --username=dtstack_plus registry.cn-shanghai.aliyuncs.com #执行后输入镜像仓库密码
   docker tag [ImageId] registry.cn-shanghai.aliyuncs.com/dtstack123/test:[标签]
   docker push registry.cn-shanghai.aliyuncs.com/dtstack123/test:[标签]
   ```

