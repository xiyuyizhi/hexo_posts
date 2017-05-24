---
title: ubuntu
date: 2016-05-03 17:24:48
tags: ubuntu
---

## 常用命令：
    
    `
      ls
      ls -l
      cp 
      mv
      tar -zxvf 解压 tar.gz文件
      rm -rf //删除文件、文件夹
      rm -R
      
      //apt-get  install purge remove 安装软件
      dpkg -i -L -r -P 查看文件安装 
      
    `

## 生成ssh key
  
   `
   
      apt-get install ssh
      ssh-keygen -t rsa -C 'xxx@email.com'
      复制id_rsa.pub到github个人ssh
      git clone github项目使用ssh方式
      
      ssh远程登录
      ssh username@remoteHost
   `

## 文件传输

   、
     本地传输到远端
      scp XXX username@remoteHost:/xxxx
      scp -r XXX/ username@remoteHost:/xxxx
      
      远端传输到本地
      scp username@remoteHost:/XXXX  xxxx
   、

## vim

normal/insert模式
i进入insert
esc退出insert
：q ：q1 :wq（保存退出）

*h j k l * 移动鼠标

1. h  左

2. l  右

3. j 下

4. K 上


*：q enter 强制退出*

*x 删除*

*i insert*

*a append*

*ESC 退出到normal模式* 



## 软件安装

安装 JDK

    ```
        编辑oracle jdk相关的profile（subl命令将会启动Sublime文本编辑器）
        sudo subl /etc/profile.d/oraclejdk.sh

        #将以下内容拷贝进入此文件
        export J2SDKDIR=/usr/lib/jvm/jdk1.8.0_66
        export J2REDIR=/usr/lib/jvm/jdk1.8.0_66/jre
        export PATH=$PATH:/usr/lib/jvm/jdk1.8.0_66/bin:/usr/lib/jvm/jdk1.8.0_66/db/bin:/usr/lib/jvm/jdk1.8.0_66/jre/bin
        export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_66
        export DERBY_HOME=/usr/lib/jvm/jdk1.8.0_66/db

        #重启命令行终端，或运行以下的source命令以激活上面的profile
        source /etc/profile.d/oraclejdk.sh
    ```

安装tomcat
      
      ```
        打开启动的脚本文件
        sudo vi ./bin/startup.sh
        
        添加jdk和jre环境变量
        JAVA_HOME=/usr/java/jdk1.8.0_20
        JRE_HOME=$JAVA_HOME/jre
        PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME
        CLASSPATH=.:$JRE_HOME/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
        TOMCAT_HOME=/opt/apache-tomcat-8.0.12
      ```

使用nvm安装nodejs
 
    ```
        安装nvm

     git clone https://github.com/creationix/nvm.git
     配置终端启动时自动执行:
         在 ~/.bashrc, ~/.bash_profile, ~/.profile, 或者 ~/.zshrc 文件添加以下命令:
         source ~/git/nvm/nvm.sh
    
     nvm安装完毕后，可以使用help看下命令列表，使用install来安装，使用use来激活node。

      nvm help
      nvm install v4.4.5
      nvm use v4.4.5 

      激活特定版本的node
      nvm alias default v4.4.5

     bower不能工作问题解决
     bower install -g -allow-root

     使用阿里仓库
     alias cnpm='npm --registry=https://registry.npm.taobao.org'

    ```



安装chrome

    ```
        1: Download Google Chrome 

      wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

      有时，会出现某依赖未安装的问题，只要执行以下命令补齐即可
      sudo apt-get install -f

      2.Install .deb file
      sudo dpkg -i google-chrome-stable_current_amd64.deb
    ```


安装nginx

    ```
        apt-get install nginx
    
        nginx.conf位置 /etc/nginx/nginx.conf
    
        vim /sites-available/default
      
        路径没问题时403问题:
         ngindex.conf  user root;
      
        service nginx start/stop
    ```



## gnome3 安装

	```
		sudo add-apt-repository ppa:gnome3-team/gnome3
 
		sudo apt-get update
 
		sudo apt-get dist-upgrade
 
		sudo apt-get install gnome-themes-standard ubuntu-desktop gnome-shell

		删除
		sudo apt-get install ppa-purge
 
		sudo ppa-purge ppa:gnome3-team/gnome3
	```

## ubuntu-tweak

```
	sudo add-apt-repository ppa:tualatrix/ppa
	sudo apt-get update
	sudo apt-get install ubuntu-tweak
```