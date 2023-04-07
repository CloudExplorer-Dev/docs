## 1 环境要求

!!! Abstract ""

    按照部署服务器要求准备好部署环境后，可通过 CloudExplorer-Lite 快速安装脚本一键快速部署。  
    **注意：** 一键安装采用默认参数，更多有关离线部署方式可查看本文档的【安装部署】。 

    **部署服务器要求:** 
    ```
    - 操作系统：CentOS7.X
    - CPU/内存：8核16GB
    - 磁盘空间：200GB
    - 网络要求：可访问互联网
    ```

## 2 安装部署
!!! Abstract ""
	执行以下脚本进行一键安装： 
    ```
    /bin/bash -c "$(curl -fsSL https://resource.fit2cloud.com/cloudexplorer-lite/installer/releases/latest/quick_start.sh)"
    ```

## 3 部署完成  
!!! Abstract ""   
    CloudExplorer-Lite 是一款 B/S 架构的产品，即浏览器/服务器结构，在服务器安装完成后，客户端通过浏览器访问以下地址，即可开始使用。
    ```
    http://目标服务器IP地址：服务运行端口
    默认用户名：admin，默认密码：cloudexplorer
    ```