## 1 环境要求

!!! Abstract ""
    **部署服务器要求：**

    * 操作系统: CentOS 7.x
    * CPU/内存: 4核8G
    * 磁盘空间: 200G

## 2 下载安装包

!!! Abstract ""
    **请自行下载 CloudExplorer-Lite 最新版本的离线安装包，并复制到目标机器的 /tmp 目录下：**  
    安装包下载链接: 

## 3 解压安装包

!!! Abstract ""
    **以 root 用户 ssh 登录到目标机器, 并执行如下命令：**  

    ```sh
    cd /tmp
    # 解压安装包
    tar -zxvf cloudexplorer-v0.2.0-offline-linux-x86_64.tar.gz
    ```

## 4 设置安装参数（可选）

!!! Abstract ""
	**注意：强烈建议不要将安装包的路径作为 CloudExplorer-Lite 的安装目录，对日常的维护以及后续版本的升级会带来一些不必要的麻烦。**  

    ```properties
    #本机IP
    CE_SERVER_HOST=CE_SERVER_HOST_PLACE_HOLDER
    # sed -i "s/CE_SERVER_HOST_PLACE_HOLDER/`hostname -I|awk '{print $1}'`/" .env

    CE_BASE=${CE_BASE:-"/opt"}

    CE_IMAGE_REPOSITORY=registry.cn-qingdao.aliyuncs.com/cloudexplorer/

    #token过期时间
    CE_JWT_EXPIRE_MINUTES=100

    ## Service 端口
    CE_PORT=80
    ## docker 网段设置
    CE_DOCKER_SUBNET=172.20.0.0/16
    ## docker 网关 IP
    CE_DOCKER_GATEWAY=172.20.0.1

    # 数据库配置
    ## 是否使用外部数据库
    CE_EXTERNAL_MYSQL=false
    ## 数据库地址
    CE_MYSQL_HOST=mysql
    ## 数据库端口
    CE_MYSQL_PORT=3306
    ## 数据库库名
    CE_MYSQL_DB=ce
    ## 数据库用户名
    CE_MYSQL_USER=root
    ## 数据库密码
    CE_MYSQL_PASSWORD=Password123@mysql
    CE_MYSQL_VERSION=8.0

    CE_MYSQL_IMAGE_REPOSITORY=${CE_IMAGE_REPOSITORY}

    ## 数据库地址
    CE_QUARTZ_MYSQL_HOST=${CE_MYSQL_HOST}
    ## 数据库端口
    CE_QUARTZ_MYSQL_PORT=${CE_MYSQL_PORT}
    ## 数据库库名
    CE_QUARTZ_MYSQL_DB=quartz
    ## 数据库用户名
    CE_QUARTZ_MYSQL_USER=${CE_MYSQL_USER}
    ## 数据库密码
    CE_QUARTZ_MYSQL_PASSWORD=${CE_MYSQL_PASSWORD}


    # Redis 配置
    ## 是否使用外部Redis
    CE_EXTERNAL_REDIS=false
    ## Redis 端口
    CE_REDIS_PORT=6379
    ## Redis 密码
    CE_REDIS_PASSWORD=Password123@redis
    ## Redis地址
    CE_REDIS_HOST=${CE_SERVER_HOST}
    CE_REDIS_VERSION=7.0.8-alpine

    CE_REDIS_IMAGE_REPOSITORY=${CE_IMAGE_REPOSITORY}

    #elk 配置
    CE_EXTERNAL_ELASTICSEARCH=false
    CE_ELASTICSEARCH_PORT=9200
    CE_ELASTICSEARCH_HOST=http://${CE_SERVER_HOST}:${CE_ELASTICSEARCH_PORT}
    #CE_ELASTICSEARCH_HOST=http://elasticsearch:9200
    CE_ELASTICSEARCH_VERSION=7.17.9
    CE_ELASTICSEARCH_MEM_LIMIT=1073741824
    CE_ELASTICSEARCH_CLUSTER_NAME=ce-elasticsearch-cluster
    CE_ELASTICSEARCH_NODE_NAME=es01
    CE_ELASTICSEARCH_IMAGE_REPOSITORY=${CE_IMAGE_REPOSITORY}

    CE_KIBANA_ENABLE=false
    CE_KIBANA_PORT=5601
    CE_KIBANA_VERSION=${CE_ELASTICSEARCH_VERSION}
    CE_KIBANA_ELASTICSEARCH_HOST=${CE_ELASTICSEARCH_HOST}
    CE_KIBANA_IMAGE_REPOSITORY=${CE_ELASTICSEARCH_IMAGE_REPOSITORY}

    CE_EXTERNAL_LOGSTASH=false
    CE_LOGSTASH_VERSION=${CE_ELASTICSEARCH_VERSION}
    CE_LOGSTASH_ELASTICSEARCH_HOST=${CE_ELASTICSEARCH_HOST}
    CE_LOGSTASH_IMAGE_REPOSITORY=${CE_ELASTICSEARCH_IMAGE_REPOSITORY}
    ```

## 5 执行安装脚本

!!! Abstract ""
    ```sh
    # 进入安装包目录
    cd cloudexplorer-v0.2.0-offline-linux-x86_64
    # 运行安装脚本
    bash install.sh
    ```
    **注意：如果使用外部数据库进行安装，推荐使用 MySQL 8.0 版本。同时 CloudExplorer-Lite 对数据库部分配置项有要求，请参考下附的数据库配置，修改环境中的数据库配置文件。**

    ```
    [mysqld]
    datadir=/var/lib/mysql
    default-time_zone=+8:00
    default-storage-engine=INNODB
    character_set_server=utf8mb4
    lower_case_table_names=1
    table_open_cache=128
    max_connections=2000
    max_connect_errors=6000
    innodb_file_per_table=1
    innodb_buffer_pool_size=1G
    max_allowed_packet=64M
    transaction_isolation=READ-COMMITTED
    innodb_flush_method=O_DIRECT
    innodb_lock_wait_timeout=1800
    innodb_flush_log_at_trx_commit=0
    sync_binlog=0
    sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'
    skip-name-resolve
    max_connections=1000
    wait_timeout=28800

    [mysql]
    default-character-set=utf8mb4

    [mysql.server]
    default-character-set=utf8mb4
	```

    **请参考文档中的建库语句创建 CloudExplorer-lite 使用的数据库，CloudExplorer-lite 服务启动时会自动在配置的库中创建所需的表结构及初始化数据。**
    ```mysql
    CREATE DATABASE `ce` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
    ```

!!! Abstract ""
    安装脚本默认使用 /opt/cloudexplorer 使用的数据库，cloudexplorer 的配置文件、数据及日志等均存放在该安装目录。  
    **安装目录目录结构说明：**
    ```
    /opt/cloudexplorer/
    ├── apps                                        #-- 安装过程中需要加载到容器中的脚本和功能模块jar包
    ├── conf                                        #-- CloudExplorer-Lite 各组件及数据库中间件的配置文件
    ├── docker-compose-elasticsearch.yml            #-- CloudExplorer-Lite 内建的 Elasticsearch 所需的 Docker Compose 文件
    ├── docker-compose-kibana.yml                   #-- CloudExplorer-Lite 内建的 Kibana 所需的 Docker Compose 文件
    ├── docker-compose-logstash.yml                 #-- CloudExplorer-Lite 内建的 Logstash 所需的 Docker Compose 文件
    ├── docker-compose-mysql.yml                    #-- CloudExplorer-Lite 内建的 MySQL 所需的 Docker Compose 文件
    ├── docker-compose-redis.yml                    #-- CloudExplorer-Lite 内建的 Redis 所需的 Docker Compose 文件
    ├── docker-compose.yml                          #-- CloudExplorer-Lite 基础 Docker Compose 文件，定义了网络等基础信息
    ├── downloads                                   #-- CloudExplorer-Lite 内建的 elasticsearch 所需的 Docker Compose 文件
    ├── elk                                         #-- CloudExplorer-Lite Elasticsearch、Logstash、Kibana 组件的数据持久化目录
    ├── logs                                        #-- CloudExplorer-Lite 各组件的日志文件持久化目录
    ├── mysql                                       #-- CloudExplorer-Lite 数据库的数据持久化目录
    └── VERSION                                     #-- CloudExplorer-Lite 的版本信息记录文件

    ```
!!! Abstract ""
    **安装成功后，通过浏览器访问如下页面登录 CloudExplorer-lite：**

    ```
    地址: http://目标服务器IP地址:服务运行端口
    用户名: admin
    密码: cloudexplorer-lite
    ```

