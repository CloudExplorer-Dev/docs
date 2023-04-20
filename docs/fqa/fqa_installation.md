
## 1、在线安装时，遇到无法安装 Docker 的情况如何处理？

!!! Abstract ""
    **分析原因：** 这种情况一般是 Linux 系统的镜像源无法访问导致的。    
    **解决方案：** 我们可以更换系统的镜像源为可以正常访问的源，比如阿里镜像源。这里列举 CentOS、Ubuntu 的换源方法。    

    - **CentOS 更换 yum 源操作步骤如下：**
  
    第一步：使用之前请确保已经安装wget，如未安装请执行下面一条命令来安装，执行以下命令：
    ```bash
    yum install -y wget
    ```
    第二步：备份原来的源，执行以下命令：
    ```bash
    mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
    ```
    第三步：下载阿里源，执行以下命令：
    ```bash
    cd /etc/yum.repos.d
    wget -nc http://mirrors.aliyun.com/repo/Centos-7.repo
    ```
    第四步：更改阿里 yum 源为默认源，执行以下命令：
    ```bash
    mv Centos-7.repo CentOS-Base.repo
    ```
    第五步：更新本地 yum 缓存，执行以下命令：
    ```bash
    # 全部清除
    yum clean all
    ​
    # 更新列表
    yum list
    ​
    # 缓存 yum 包信息到本机，提高搜索速度
    yum makecache
    ```

    - **Ubuntu 更换 yum 源操作步骤如下：**

    第一步：更换源之前先安装 apt-transport-https和ca-certificates，执行以下命令：
    ```bash
    sudo apt-get install apt-transport-https ca-certificates
    ```
    第二步：查看 Ubuntu 的 Codename，执行以下命令：
    ```bash
    lsb_release -a | grep Codename | awk '{print $2}' # 输出结果为下文中的Codename
    ```
    第三步：备份系统源，执行以下命令：
    ```bash
    # 备份源
    sudo mv /etc/apt/sources.list /etc/apt/sources.list.bak
    ```
    第四步：写入阿里源，执行以下命令：
    ```bash
    vi /etc/apt/sources.list
    ```
    说明：下面源信息中 \$Codename 为第一步中系统的 Codename，用记事本批量替换即可。
    ```bash
    deb http://mirrors.aliyun.com/ubuntu/ $Codename main multiverse restricted universe
    deb http://mirrors.aliyun.com/ubuntu/ $Codename-backports main multiverse restricted universe
    deb http://mirrors.aliyun.com/ubuntu/ $Codename-proposed main multiverse restricted universe
    deb http://mirrors.aliyun.com/ubuntu/ $Codename-security main multiverse restricted universe
    deb http://mirrors.aliyun.com/ubuntu/ $Codename-updates main multiverse restricted universe
    deb-src http://mirrors.aliyun.com/ubuntu/ $Codename main multiverse restricted universe
    deb-src http://mirrors.aliyun.com/ubuntu/ $Codename-backports main multiverse restricted universe
    deb-src http://mirrors.aliyun.com/ubuntu/ $Codename-proposed main multiverse restricted universe
    deb-src http://mirrors.aliyun.com/ubuntu/ $Codename-security main multiverse restricted universe
    deb-src http://mirrors.aliyun.com/ubuntu/ $Codename-updates main multiverse restricted universe
    ```
    第五步：执行更新，执行以下命令：
    ```bash
    apt-get update
    ```

## 2.安装过程中提示安装 Docker-compose 失败如何处理？

!!! Abstract ""
    **分析原因：** 这种情况一般是因为服务器当前安装的 Docker 版本较低，不满足 CloudExplorer Lite 要求版本。    
    **解决方案一：** 可以将当前 Docker 卸载后，直接执行 CloudExplorer Lite 的部署，部署过程会自动安装新版本的 Docker。    
    **解决方案二：** 手动升级当前服务器的 Docker 到新版本，然后执行部署脚本或命令。 
