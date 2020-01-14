# 部署LAMP环境（centos7）

* * *

本教程介绍如何使用云服务器ECS实例搭建LAMP平台，其中LAMP分别代表Linux、Apache、MySQL和PHP。

使用本教程进行操作前，请确保您已经注册了阿里云账号。如还未注册，请先完成[账号注册](https://account.alibabacloud.com/register/intl_register.htm)。

本篇教程在示例步骤中使用了以下配置的ECS实例。

-   操作系统：CentOS 7.2 64位
-   专有网络
-   公网IP

本篇教程在示例步骤中使用了以下版本的软件。操作时，请您以实际软件版本为准。

-   Apache：2.4.37
-   MySQL：5.6.24
-   PHP：7.0.32
-   phpMyAdmin：4.0.10.20

本篇教程适用于熟悉Linux操作系统，初次使用阿里云进行建站的个人用户。

本篇教程主要说明手动安装LAMP平台的操作步骤，您也可以在[云市场](https://marketplace.alibabacloud.com/)购买LAMP镜像直接启动ECS，以便快速建站。

## 操作步骤 

使用云服务器ECS搭建LAMP平台的操作步骤如下：

1.  [步骤一：准备编译环境](#section_g52_3gn_gq0)
2.  [步骤二：安装Apache](#section_quq_rne_jnu)
3.  [步骤三：安装MySQL](#section_11v_i8e_gi0)
4.  [步骤四：安装PHP](#section_mi4_v1k_psh)
5.  [步骤五：安装phpMyAdmin](#section_sae_6lj_aa5)

## 步骤一：准备编译环境 

完成以下操作，准备编译环境：

1.  [使用向导创建实例](../intl.zh-CN/实例/创建实例/使用向导创建实例.md#)。
2.  [使用管理终端连接Linux实例](../intl.zh-CN/实例/连接实例/连接Linux实例/使用管理终端连接Linux实例.md#)。
3.  运行命令cat /etc/redhat-release查看系统版本。 

    ![查看系统版本](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64105/156862661732170_zh-CN.png)

4.  关闭防火墙。 
    1.  运行systemctl status firewalld命令查看当前防火墙的状态。 

        ![查看防火墙状态](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/64105/156862661732172_zh-CN.png)

        -   如果防火墙的状态参数是inactive，则防火墙为关闭状态。
        -   如果防火墙的状态参数是active，则防火墙为开启状态。本示例中防火墙为开启状态，因此需要关闭防火墙。
    2.  关闭防火墙。如果防火墙为关闭状态，请忽略此步骤。 
        -   如果您想临时关闭防火墙，运行命令systemctl stop firewalld。

            **说明：** 这只是暂时关闭防火墙，下次重启Linux后，防火墙还会开启。

        -   如果您想永久关闭防火墙，运行命令systemctl disable firewalld。

            **说明：** 如果您想重新开启防火墙，请参见[firewalld官网信息](https://firewalld.org/)。

5.  关闭SELinux。 
    1.  运行getenforce命令查看SELinux的当前状态。 

        ![查看SELinux状态](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9763/156862661721065_zh-CN.png)

        -   如果SELinux状态参数是Disabled， 则SELinux为关闭状态。
        -   如果SELinux状态参数是Enforcing，则SELinux为开启状态。本示例中SELinux为开启状态，因此需要关闭SELinux。
    2.  关闭SELinux。如果SELinux为关闭状态，请忽略此步骤。 
        -   如果您想临时关闭SELinux，运行命令setenforce 0。

            **说明：** 这只是暂时关闭SELinux，下次重启Linux后，SELinux还会开启。

        -   如果您想永久关闭SELinux，运行命令vi /etc/selinux/config编辑SELinux配置文件。回车后，把光标移动到`SELINUX=enforcing`这一行，按`i`键，将其修改为`SELINUX=disabled`， 按`Esc`键，然后输入`:wq`并回车以保存并关闭SELinux配置文件。

            **说明：** 如果您想重新开启SELinux，请参见[SELinux的官方文档](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/deployment_guide/ch-selinux#s1-SELinux-resources)。

    3.  重启系统使设置生效。
6.  在实例安全组的入方向添加规则，放行所需端口。具体步骤，请参见[添加安全组规则](../intl.zh-CN/安全/安全组/添加安全组规则.md#)。

## 步骤二：安装Apache 

完成以下操作，安装Apache：

1.  依次运行以下命令安装依赖包。 
    1.
    ``` 
    yum groupinstall "Development Tools" -y
    ```
    2.  
    ``` {#codeblock_q6g_eav_yze}
    yum install libtool -y
    ```

    3.  
    ``` 
    yum install expat-devel pcre pcre-devel openssl-devel -y
    ```

2.  依次运行以下命令下载并解压Apache，Apr和Apr-util的源码包。 

    1.  ``` 
        wget https://mirrors.aliyun.com/apache/httpd/httpd-2.4.37.tar.gz
        ```
    2.  ```
        wget https://mirrors.aliyun.com/apache/apr/apr-1.6.5.tar.gz
        ```

    3.  ``` {#codeblock_7xb_c1h_c4d}
        wget https://mirrors.aliyun.com/apache/apr/apr-util-1.6.1.tar.gz
        ```

    4.  ``` {#codeblock_q0b_izq_pqj}
        tar xvf httpd-2.4.37.tar.gz -C /usr/local/src
        ```

    5.  ``` {#codeblock_xmg_4su_n7b}
        tar xvf apr-1.6.5.tar.gz -C /usr/local/src
        ```

    6.  ``` {#codeblock_1a8_8c7_6r1}
        tar xvf apr-util-1.6.1.tar.gz -C /usr/local/src
        ```

    **说明：** 源代码版本会不断升级。您可以从[httpd源码安装包](https://mirrors.aliyun.com/apache/httpd/)和[apr源码安装包](https://mirrors.aliyun.com/apache/apr/)获取合适的安装包地址。

3.  依次运行以下命令把Apr和Apr-util的文件夹移到Apache的srclib文件夹下。 
    1.  ``` {#codeblock_rpf_6sh_306}
        cd /usr/local/src
        ```

    2.  ``` {#codeblock_ara_5z4_wzw}
        mv apr-1.6.5 httpd-2.4.37/srclib/apr
        ```

    3.  ``` {#codeblock_ghn_mpb_877}
        mv apr-util-1.6.1 httpd-2.4.37/srclib/apr-util
        ```

4.  依次运行以下命令编译源码。 
    1.  ``` {#codeblock_45a_td3_idb}
        cd /usr/local/src/httpd-2.4.37
        ```

    2.  ``` {#codeblock_9p5_oqw_224}
        ./buildconf
        ```

    3.  ``` {#codeblock_jnw_gmb_r5b}
        ./configure --prefix=/usr/local/apache2 \
        --enable-ssl \
        --enable-so \
        --with-mpm=event \
        --with-included-apr \
        --enable-cgi \
        --enable-rewrite \
        --enable-mods-shared=most \
        --enable-mpms-shared=all
        ```

    4.  ``` {#codeblock_dcu_y16_0vy}
        make && make install
        ```

5.  依次运行以下命令设置PATH环境变量。 
    1.  ``` {#codeblock_6c1_37v_f08}
        echo "export PATH=$PATH:/usr/local/apache2/bin" > /etc/profile.d/httpd.sh
        ```

    2.  ``` {#codeblock_toc_gz5_zac}
        source /etc/profile.d/httpd.sh
        ```

6.  运行httpd -v命令可查看Apache的版本号。 

    ![查看版本号](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661733483_zh-CN.png)

7.  添加Apache的启动配置文件。 
    1.  运行命令vi /usr/lib/systemd/system/httpd.service打开Apache的启动配置文件。
    2.  按下`i`键，然后在配置文件中写下如下内容。 

        ``` {#codeblock_pil_cyu_qvf}
        [Unit] 
        Description=The Apache HTTP Server 
        After=network.target 
        
        [Service] 
        Type=forking 
        ExecStart=/usr/local/apache2/bin/apachectl -k start 
        ExecReload=/usr/local/apache2/bin/apachectl -k graceful 
        ExecStop=/usr/local/apache2/bin/apachectl -k graceful-stop 
        PIDFile=/usr/local/apache2/logs/httpd.pid 
        PrivateTmp=false
        
        [Install] 
        WantedBy=multi-user.target
        ```

    3.  按下`Esc`键，然后输入`:wq`并回车以保存并关闭Apache启动配置文件。
8.  依次运行以下命令启动Apache服务并设置服务开机自启动。 
    1.  ``` {#codeblock_xoi_hx2_54m}
        systemctl start httpd
        ```

    2.  ``` {#codeblock_gp8_5ct_alt}
        systemctl enable httpd
        ```

9.  查看安装结果。 
    1.  登录[ECS管理控制台](https://ecs.console.aliyun.com)。
    2.  在左侧导航栏，单击**实例与镜像** \> **实例**。
    3.  在**实例列表**中找到正在部署环境的实例，从这个实例的**IP地址**项中复制它的公网IP。
    4.  在本地机器的浏览器地址栏中，输入`http://实例公网IP`并按`Enter`键。 若返回页面如下图所示，说明Apache服务启动成功。

        ![默认欢迎页面](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661712353_zh-CN.png)


## 步骤三：安装MySQL

完成以下操作，安装MySQL：

1.  依次运行以下命令准备编译环境。 
    1.  ``` {#codeblock_xhk_nr8_5dq}
        yum install ncurses-devel bison gnutls-devel -y
        ```

    2.  ``` {#codeblock_naq_2sz_tq3}
        yum install cmake -y
        ```

2.  依次运行以下命令准备MySQL数据存放目录。 
    1.  ``` {#codeblock_pl4_xuc_29u}
        cd
        ```

    2.  ``` {#codeblock_hbu_qof_i6n}
        mkdir /mnt/data
        ```

    3.  ``` {#codeblock_ywi_hvv_soe}
        groupadd -r mysql
        ```

    4.  ``` {#codeblock_hs6_tbj_643}
        useradd -r -g mysql -s /sbin/nologin mysql
        ```

    5.  ``` {#codeblock_rq1_h94_oza}
        id mysql
        ```

3.  运行以下命令更改数据目录属主和属组。 

    ``` {#codeblock_9sc_0bh_avc}
    chown -R mysql:mysql /mnt/data
    ```

4.  依次运行以下命令下载稳定版源码包并解压、编译。 
    1.  ``` {#codeblock_ib0_cql_9q8}
         wget https://downloads.mysql.com/archives/get/file/mysql-5.6.24.tar.gz
        ```

    2.  ``` {#codeblock_xyx_x9v_kst}
         tar xvf mysql-5.6.24.tar.gz -C  /usr/local/src
        ```

    3.  ``` {#codeblock_454_km3_2nu}
        cd /usr/local/src/mysql-5.6.24
        ```

    4.  ``` {#codeblock_581_ngr_b6q}
        cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
        -DMYSQL_DATADIR=/mnt/data \
        -DSYSCONFDIR=/etc \
        -DWITH_INNOBASE_STORAGE_ENGINE=1 \
        -DWITH_ARCHIVE_STORAGE_ENGINE=1 \
        -DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
        -DWITH_READLINE=1 \
        -DWITH_SSL=system \
        -DWITH_ZLIB=system \
        -DWITH_LIBWRAP=0 \
        -DMYSQL_TCP_PORT=3306 \
        -DDEFAULT_CHARSET=utf8 \
        -DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \
        -DDEFAULT_COLLATION=utf8_general_ci \
        -DWITH_SYSTEMD=1 \
        -DINSTALL_SYSTEMD_UNITDIR=/usr/lib/systemd/system 
        ```

    5.  ``` {#codeblock_n8i_684_40w}
         make && make install
        ```

5.  运行以下命令修改安装目录的属组为mysql。 

    ``` {#codeblock_13o_sv2_pci}
    chown -R mysql:mysql /usr/local/mysql/
    ```

6.  依次运行以下命令初始化数据库并复制配置文件。 
    1.  ``` {#codeblock_abf_jzn_2rg}
        cd /usr/local/mysql
        ```

    2.  ``` {#codeblock_m1i_2ph_a1y}
        /usr/local/mysql/scripts/mysql_install_db --user=mysql --datadir=/mnt/data/
        ```

    3.  ``` {#codeblock_6nu_4j7_07k}
        mv /etc/my.cnf /etc/my.cnf.bak
        ```

    4.  ``` {#codeblock_0df_uiu_3j3}
        cp /usr/local/mysql/support-files/my-default.cnf  /etc/my.cnf
        ```

7.  运行以下命令修改配置文件中的安装路径及数据目录存放路径。 

    ``` {#codeblock_g58_sa3_wj4}
    echo -e "basedir = /usr/local/mysql\ndatadir = /mnt/data\n" >> /etc/my.cnf
    ```

8.  修改MySQL的启动配置文件。 
    1.  运行命令vi /usr/lib/systemd/system/mysql.service打开MySQL的启动配置文件。
    2.  按下`i`键，然后添加如下内容： 

        ``` {#codeblock_qqq_zmq_q7n}
        [Unit]
        Description=MySQL Community Server
        After=network.target
        After=syslog.target
        
        [Install]
        WantedBy=multi-user.target
        Alias=mysql.service
        
        [Service]
        User=mysql
        Group=mysql
        PermissionsStartOnly=true
        ExecStart=/usr/local/mysql/bin/mysqld
        TimeoutSec=600
        Restart=always
        PrivateTmp=false
        ```

    3.  按下`Esc`键，然后输入`:wq`并回车以保存并关闭MySQL启动配置文件。
9.  依次运行以下命令设置PATH环境变量。 
    1.  ``` {#codeblock_hw2_tjt_pyi}
        echo "export PATH=$PATH:/usr/local/mysql/bin" > /etc/profile.d/mysql.sh
        ```

    2.  ``` {#codeblock_ncf_wl2_fiy}
        source /etc/profile.d/mysql.sh
        ```

10. 依次运行以下命令启动MySQL服务并设置开机自启动。 
    1.  ``` {#codeblock_d2f_7wm_k2d}
        systemctl start mysql
        ```

    2.  ``` {#codeblock_od9_xey_rf6}
        systemctl enable mysql
        ```

11. 修改MySQL的root用户密码。运行以下命令，并按界面提示设置密码。 

    ``` {#codeblock_630_t3q_dax}
    mysqladmin -u root password
    ```

12. 运行以下命令测试登录MySQL数据库。 

    ``` {#codeblock_1hl_yco_i5z}
    mysql -uroot -p
    ```

    ![测试数据库](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661733592_zh-CN.png)

13. 运行`\q`命令退出MySQL。

## 步骤四：安装PHP

完成以下操作，安装PHP：

1.  运行以下命令安装依赖包。 

    ``` {#codeblock_ayh_jjy_2m0}
    yum install libmcrypt libmcrypt-devel mhash mhash-devel libxml2 libxml2-devel bzip2 bzip2-devel -y
    ```

2.  依次运行以下命令下载稳定版源码包并解压、编译。 

    1.  ``` {#codeblock_o62_j2j_5ry}
        cd
        ```

    2.  ``` {#codeblock_8sc_e1d_wiq}
        wget http://cn2.php.net/get/php-7.0.32.tar.bz2/from/this/mirror
        ```

    3.  ``` {#codeblock_1w5_7ni_clh}
        cp mirror php-7.0.32.tar.bz2
        ```

    4.  ``` {#codeblock_mqy_c9x_cfb}
        tar xvf php-7.0.32.tar.bz2 -C /usr/local/src
        ```

    5.  ``` {#codeblock_dbv_jmb_2iz}
        cd /usr/local/src/php-7.0.32
        ```

    6.  ``` {#codeblock_wb6_cxk_v58}
        ./configure --prefix=/usr/local/php \
        --with-config-file-scan-dir=/etc/php.d \
        --with-apxs2=/usr/local/apache2/bin/apxs \
        --with-config-file-path=/etc \
        --with-pdo-mysql=mysqlnd \
        --with-mysqli=/usr/local/mysql/bin/mysql_config \
        --enable-mbstring \
        --with-freetype-dir \
        --with-jpeg-dir \
        --with-png-dir \
        --with-zlib \
        --with-libxml-dir=/usr \
        --with-openssl \
        --enable-xml \
        --enable-sockets \
        --enable-fpm \
        --with-bz2
        ```

    7.  ``` {#codeblock_29h_rvz_3sj}
        make && make install
        ```

    **说明：** 若ECS实例规格内存较小，配置时可关闭不需要的PHP扩展，节省内存。例如，在./configure命令中添加--disable-fileinfo选项，关闭fileinfo扩展。

3.  运行以下命令复制PHP的配置文件。 

    ``` {#codeblock_197_yoq_mar}
    cp php.ini-production /etc/php.ini
    ```

4.  运行命令vi /usr/local/apache2/conf/httpd.conf打开Apache配置文件，按下`i`键开始编辑。 
    1.  找到ServerName参数，添加`ServerName localhost:80`。 

        ![添加参数内容](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661712350_zh-CN.png)

    2.  找到Directory参数，在`Require all denied`前面添加`#`，然后添加`Require all granted`。 

        ![添加内容](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661712349_zh-CN.png)

    3.  找到`DirectoryIndex index.html`，将它替换为`DirectoryIndex index.php index.html`。 

        ![替换内容](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661733711_zh-CN.png)

    4.  找到如下内容： 

        ![找到如下内容](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661833713_zh-CN.png)

        在后面添加如下内容：

        ``` {#codeblock_iep_395_lo8}
        AddType application/x-httpd-php .php
        AddType application/x-httpd-php-source .phps
        ```

        添加完成后，如下图所示。

        ![添加完成](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661833714_zh-CN.png)

    5.  按下`Esc`键，然后输入`:wq`并回车以保存并关闭Apache配置文件。
5.  添加Apache对解析PHP的支持。 
    1.  运行以下命令打开index.php文件。 

        ``` {#codeblock_4e0_l3z_02l}
        vi /usr/local/apache2/htdocs/index.php
        ```

    2.  按下`i`键进入编辑模式，并添加以下内容： 

        ``` {#codeblock_ig4_jcc_ksq}
        <?php
        phpinfo();
        ?>
        ```

    3.  按下`Esc`键，然后输入`:wq`并回车以保存并关闭index.php文件。
    4.  运行以下命令重启Apache服务。 

        ``` {#codeblock_jn3_3rd_2q8}
        systemctl restart httpd
        ```

6.  在本地机器的浏览器地址栏中，输入`http://实例公网IP`并按`Enter`键。 若返回页面如下图所示，说明PHP解析成功。

    ![PHP解析成功](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661833715_zh-CN.png)


## 步骤五：安装phpMyAdmin 

完成以下操作，安装phpMyAdmin：

1.  依次运行以下命令准备phpMyAdmin数据存放目录。 
    1.  ``` {#codeblock_iyv_ohz_gnb}
        cd
        ```

    2.  ``` {#codeblock_b4t_pej_8fe}
        mkdir -p /usr/local/apache2/htdocs/phpmyadmin
        ```

2.  依次运行以下命令下载phpMyAdmin压缩包并解压。 
    1.  ``` {#codeblock_krx_ugy_8mk}
        wget https://files.phpmyadmin.net/phpMyAdmin/4.0.10.20/phpMyAdmin-4.0.10.20-all-languages.zip
        ```

    2.  ``` {#codeblock_kvm_xp8_yki}
        unzip phpMyAdmin-4.0.10.20-all-languages.zip
        ```

3.  运行以下命令复制phpMyAdmin文件到准备好的数据存放目录。 

    ``` {#codeblock_vgo_f5l_5eg}
    mv phpMyAdmin-4.0.10.20-all-languages/*  /usr/local/apache2/htdocs/phpmyadmin
    ```

4.  在本地机器浏览器地址栏，输入`http://实例公网 IP/phpmyadmin`并按`Enter`键，访问phpMyAdmin登录页面。 若返回页面如下图所示，说明phpMyAdmin安装成功。

    ![phpMyAdmin安装成功](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661833718_zh-CN.png)

5.  输入MySQL的用户名和密码，单击**执行**。 

    ![输入用户名和密码](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/9778/156862661833716_zh-CN.png)