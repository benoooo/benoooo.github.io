# 部署LNMP环境（CentOS 8）


# 部署LNMP环境（CentOS 8）

[本页目录](javascript:void(0))

- [前提条件]
- [背景信息]
- [步骤一：准备编译环境]
- [步骤二：安装Nginx]
- [步骤三：安装MySQL]
- [步骤四：安装PHP]
- [步骤五：配置Nginx]
- [步骤六：配置MySQL]
- [步骤七：配置PHP]
- [步骤八：测试访问LNMP平台]
- [后续步骤]

本教程介绍如何手动在ECS实例上搭建LNMP环境（CentOS 8），其中LNMP分别代表Linux、Nginx、MySQL和PHP。

## 前提条件

- 已创建ECS服务器实例并为实例分配公网IP地址。
- 已在实例安全组的入方向添加安全组规则并放行80端口。)。

## 背景信息

CentOS 8版本的操作系统中默认安装了DNF软件包管理器，是YUM软件包管理器的下一代版本。您可以在CentOS 8系统中运行**dnf**命令获取相关的使用说明。

本教程适用于熟悉Linux操作系统。

操作系统及软件版本如下所示。当您使用不同软件版本时，可能需要根据实际情况调整命令和参数配置。

- 操作系统：CentOS 8.1 64位
- Nginx版本：Nginx 1.16.1
- MySQL版本：MySQL 8.0.17
- PHP版本：PHP 7.3.5

## 步骤一：准备编译环境

1. 远程连接Linux实例。

2. 关闭防火墙。

   1. 运行**systemctl status firewalld**命令查看当前防火墙的状态。

      [![查看防火墙状态](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p32172.png)](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p32172.png)

      - 如果防火墙的状态参数是inactive，则防火墙为关闭状态。
      - 如果防火墙的状态参数是active，则防火墙为开启状态。本示例中防火墙为开启状态，因此需要关闭防火墙。

   2. 关闭防火墙。如果防火墙为关闭状态可以忽略此步骤。

      - 如果您想临时关闭防火墙，运行命令

        systemctl stop firewalld

        。

        **说明** 这只是暂时关闭防火墙，下次重启Linux后，防火墙还会开启。

      - 如果您想永久关闭防火墙，运行命令

        systemctl disable firewalld

        。

        **说明** 如果您想重新开启防火墙，请参见[firewalld官网信息](https://firewalld.org/)。

3. 关闭SELinux。

   1. 运行**getenforce**命令查看SELinux的当前状态。

      [![查看SELinux状态](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p21065.png)](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p21065.png)

      - 如果SELinux状态参数是Disabled，则SELinux为关闭状态。
      - 如果SELinux状态参数是Enforcing，则SELinux为开启状态。本示例中SELinux为开启状态，因此需要关闭SELinux。

   2. 关闭SELinux。如果SELinux为关闭状态可以忽略此步骤。

      - 如果您想临时关闭SELinux，运行命令

        setenforce 0

        。

        **说明** 这只是暂时关闭SELinux，下次重启Linux后，SELinux还会开启。

      - 如果您想永久关闭SELinux，运行命令

        vim /etc/selinux/config

        编辑SELinux配置文件。回车后，把光标移动到

        ```
        SELINUX=enforcing
        ```

        这一行，按

        i

        键进入编辑模式，修改为

        ```
        SELINUX=disabled
        ```

        ，按

        Esc

        键，然后输入

        :wq

        并按

        Enter

        键以保存并关闭SELinux配置文件。

        **说明** 如果您想重新开启SELinux，请参见[开启或关闭SELinux](https://help.aliyun.com/document_detail/157022.htm#task-2385075)。

   3. 重启系统使设置生效。

## 步骤二：安装Nginx

1. 运行以下命令安装Nginx。

   本教程将选用Nginx 1.16.1版本。

   **说明** 您可以访问[Nginx官方安装包](http://nginx.org/packages/centos/8/x86_64/RPMS/)获取适用于CentOS 8系统的多版本的Nginx安装包。

   ```
   dnf -y install http://nginx.org/packages/centos/8/x86_64/RPMS/nginx-1.16.1-1.el8.ngx.x86_64.rpm
   ```

2. 运行以下命令查看Nginx版本。

   ```
   nginx -v
   ```

   查看版本结果如下所示。

   ```
   nginx version: nginx/1.16.1
   ```

## 步骤三：安装MySQL

1. 运行以下命令安装MySQL。

   ```
   dnf -y install @mysql
   ```

2. 运行以下命令查看MySQL版本。

   ```
   mysql -V
   ```

   查看版本结果如下所示。

   ```
   mysql  Ver 8.0.17 for Linux on x86_64 (Source distribution)
   ```

## 步骤四：安装PHP

1. 运行以下命令添加并更新epel源。

   ```
   dnf -y install epel-release
   dnf update epel-release
   ```

2. 运行以下命令删除缓存的无用软件包并更新软件源。

   ```
   dnf clean all
   dnf makecache
   ```

3. 启用`php:7.3`模块。

   **说明** 本示例使用`php:7.3`版本。如果您需要使用`PHP 7.4`版本，需要先安装remi源。remi源安装命令为**dnf -y install https://rpms.remirepo.net/enterprise/remi-release-8.rpm**。

   ```
   dnf module enable php:7.3
   ```

4. 运行以下命令安装PHP相应的模块。

   ```
   dnf install php php-curl php-dom php-exif php-fileinfo php-fpm php-gd php-hash php-json php-mbstring php-mysqli php-openssl php-pcre php-xml libsodium
   ```

5. 运行以下命令查看PHP版本。

   ```
   php -v
   ```

   查看版本结果如下所示。

   ```
   PHP 7.3.5 (cli) (built: Apr 30 2019 08:37:17) ( NTS )
   Copyright (c) 1997-2018 The PHP Group
   Zend Engine v3.3.5, Copyright (c) 1998-2018 Zend Technologies
   ```

## 步骤五：配置Nginx

1. 运行以下命令查看Nginx配置文件的默认路径。

   ```
   cat /etc/nginx/nginx.conf
   ```

   在`http`大括号内，查看`include`配置项。即配置文件的默认路径。[![conf](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p130116.png)](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p130116.png)

2. 在配置文件的默认路径下，备份默认配置文件。

   ```
   cd /etc/nginx/conf.d
   cp default.conf default.conf.bak
   ```

3. 修改默认配置文件。

   1. 运行以下命令打开默认配置文件。

      ```
      vi default.conf
      ```

   2. 按i进入编辑模式。

   3. 在`location`大括号内，修改以下内容。

      ```
      location / {
          #将该路径替换为您的网站根目录。
          root   /usr/share/nginx/html;
          #添加默认首页信息index.php。
          index  index.html index.htm index.php;
      }
      ```

   4. 去掉被注释的`location ~ \.php$`大括号内容前的`#`，并修改大括号的内容。

      修改完成如下所示。

      ```
      location ~ \.php$ {
          #将该路径替换为您的网站根目录。
          root           /usr/share/nginx/html;
          #Nginx通过unix套接字与PHP-FPM建立联系，该配置与/etc/php-fpm.d/www.conf文件内的listen配置一致。
          fastcgi_pass   unix:/run/php-fpm/www.sock;
          fastcgi_index  index.php;
          #将/scripts$fastcgi_script_name修改为$document_root$fastcgi_script_name。
          fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
          #Nginx调用fastcgi接口处理PHP请求。
          include        fastcgi_params;
      }
      ```

      **说明** Nginx与PHP-FPM进程间通信方式有两种。

      - TCP Socket：该方式能够通过网络，可用于跨服务器通信的场景。
      - UNIX Domain Socket：该方式不能通过网络，只能用于同一服务器中通信的场景。

   5. 按下Esc键，并输入`:wq`保存退出文件。

4. 运行以下命令启动Nginx服务。

   ```
   systemctl start nginx
   ```

5. 运行以下命令设置Nginx服务开机自启动。

   ```
   systemctl enable nginx
   ```

## 步骤六：配置MySQL

1. 运行以下命令启动MySQL，并设置为开机自启动。

   ```
   systemctl enable --now mysqld
   ```

2. 运行以下命令查看MySQL是否已启动。

   ```
   systemctl status mysqld
   ```

   查看返回结果中`Active: active (running)`表示已启动。

3. 运行以下命令执行MySQL安全性操作并设置密码。

   ```
   mysql_secure_installation
   ```

   命令运行后，根据命令行提示执行如下操作。

   1. 输入Y并回车开始相关配置。

   2. 选择密码验证策略强度，输入

      2

      并回车。

      策略0表示低，1表示中，2表示高。建议您选择高强度的密码验证策略。

   3. 设置MySQL的新密码并确认。

      本示例设置密码`PASSword123！`。

   4. 输入Y并回车继续使用提供的密码。

   5. 输入Y并回车移除匿名用户。

   6. 设置是否允许远程连接MySQL。

      - 不需要远程连接时，输入Y并回车。
      - 需要远程连接时，输入N或其他任意非Y的按键，并回车。

   7. 输入Y并回车删除`test`库以及对`test`库的访问权限。

   8. 输入Y并回车重新加载授权表。

## 步骤七：配置PHP

1. 修改PHP配置文件。

   1. 运行以下命令打开配置文件。

      ```
      vi /etc/php-fpm.d/www.conf
      ```

   2. 按i进入编辑模式。

   3. 找到`user = apache`和`group = apache`，将`apache`修改为`nginx`。

      [![php-fpm conf](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p130166.png)](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p130166.png)

   4. 按下Esc键，并输入`:wq`保存退出文件。

2. 新建phpinfo.php文件，用于展示PHP信息。

   1. 运行以下命令新建文件。

      ```
      vim <网站根目录>/phpinfo.php  #将<网站根目录>替换为您配置的网站根目录。
      ```

      网站根目录是您在nginx.conf文件中`location ~ .php$`大括号内配置的`root`值，如下图所示。

      [![lnmp-root-dir](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p69633.png)](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p69633.png)

      本教程配置的网站根目录为/usr/share/nginx/html，因此命令为：

      ```
      vim /usr/share/nginx/html/phpinfo.php
      ```

   2. 按i进入编辑模式。

   3. 输入下列内容，函数`phpinfo()`会展示PHP的所有配置信息。

      ```
      <?php echo phpinfo(); ?>
      ```

   4. 按Esc键后，输入:wq并回车以保存并关闭配置文件。

3. 运行以下命令启动`PHP-FPM`。

   ```
   systemctl start php-fpm
   ```

4. 运行以下命令设置`PHP-FPM`开机自启动。

   ```
   systemctl enable php-fpm
   ```

## 步骤八：测试访问LNMP平台

1. 在本地物理机打开浏览器。

2. 在地址栏输入`http://<ECS实例公网IP地址>/phpinfo.php`。

   返回结果如下图所示，表示LNMP环境部署成功。[![phpinfo](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p130169.png)](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4458755951/p130169.png)

## 后续步骤

测试访问LNMP平台成功后，建议您运行以下命令将phpinfo.php文件删除，消除安全隐患。

```
rm -rf <网站根目录>/phpinfo.php   #将<网站根目录>替换为您在nginx.conf中配置的网站根目录
```

本教程配置的网站根目录为/usr/share/nginx/html，因此命令为：

```
rm -rf /usr/share/nginx/html/phpinfo.php
```

