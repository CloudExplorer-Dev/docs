## 1 项目结构

!!! Abstract ""
    ```
    .
    ├── LICENSE                                          # License 申明
    ├── README.md
    ├── demo                                             # demo
    │     ├── DEMO-TEMPLATE                                   # 功能模块demo模版
    │     ├── pom.xml
    │     └── src
    ├── doc
    │     ├── cloudexplorer                              # 配置项模版
    │     └── 开发指南.md
    ├── framework                                        # 项目主框架应用
    │     ├── eureka                                          # 注册中心
    │     ├── gateway                                         # 网关
    │     ├── management-center                               # 管理中心
    │     ├── pom.xml
    │     ├── provider                                        # 需要用到的外部云sdk
    │     └── sdk                                             # 项目通用的前后端依赖/网关的前端
    ├── mvnw
    ├── mvnw.cmd
    ├── package.json                                     # 整体 yarn 项目使用的 package 文件
    ├── pom.xml                                          # 整体 maven 项目使用的 pom 文件
    └── services                                         # 功能模块
        ├── finance-management
        ├── operation-analysis
        ├── pom.xml
        ├── security-compliance
        └── vm-service
    ```

## 2 环境准备

### 2.1 前端环境

!!! Abstract ""
    - 安装 [node](https://nodejs.org/)
    - 启用 corepack
  
        - 当 Node.js >=16.10
        ```bash
        corepack enable
        ```

        - 当 Node.js <16.10  
        ```bash
        npm i -g corepack
        ```


### 2.2 后端环境

!!! Abstract ""
    - 安装 [JDK17](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html)
    - 安装 [Maven](https://maven.apache.org/download.cgi) (可选，可直接使用项目内的 mvnw 命令代替 mvn 命令)
    - 安装 [Docker](https://www.docker.com/) (可选)


## 3 开发准备

### 3.1 VMware相关依赖

!!! Abstract ""
    请在项目根目录执行
    ``` bash
    ./mvnw initialize
    ```
### 3.2 本地配置

!!! Abstract ""
    若要项目启动，需要准备配置文件及目录

    - 准备目录
    ```bash
    #将doc/下的 cloudexplorer 目录拷贝至 /opt目录下
    cp -r doc/cloudexplorer /opt/cloudexplorer
    ```

    - 若在 Windows 环境下配置，将配置文件放置到工程源码的所在盘的指定路径下。
    如源码工程在 D 盘下，则 /opt/cloudexplorer 存放路径为 d:\opt\cloudexplorer

    - 配置`/opt/cloudexplorer/conf/cloudexplorer.properties`，建议按下列参数配置，并根据实际情况配置文件中的数据库访问地址等参数
    ```properties
    ce.datasource.url=jdbc:mysql://localhost:3306/ce?autoReconnect=false&useUnicode=true&characterEncoding=UTF-8&characterSetResults=UTF-8&zeroDateTimeBehavior=convertToNull&serverTimezone=Asia/Shanghai
    ce.datasource.password=password
    ce.datasource.username=root
    
    #quartz定时任务的数据库可不配置，不配置时默认使用ce的数据库配置
    ce.datasource.quartz.url=jdbc:mysql://localhost:3306/quartz?autoReconnect=false&useUnicode=true&characterEncoding=UTF-8&characterSetResults=UTF-8&zeroDateTimeBehavior=convertToNull&serverTimezone=Asia/Shanghai
    ce.datasource.quartz.password=password
    ce.datasource.quartz.username=root
    
    eureka.client.service-url.defaultZone=http://127.0.0.1:8761/eureka/
    eureka.instance.prefer-ip-address=true
    eureka.instance.hostname=eureka
    
    server.max-http-header-size=4086KB
    
    spring.redis.host=localhost
    spring.redis.port=6379
    spring.redis.database=1
    spring.redis.password=redis_password
    
    # ELASTICSEARCH
    spring.elasticsearch.uris=localhost:9200
    #spring.elasticsearch.password=ES_PASSWORD
    #spring.elasticsearch.username=ES_USER
    spring.elasticsearch.connection-timeout=10s
    spring.elasticsearch.read-timeout=30s
    
    #JWT超时时间配置
    jwt.expire.minutes=100
    
    spring.servlet.multipart.max-file-size=50MB
    spring.servlet.multipart.max-request-size=50MB

    logger.level=INFO

    ce.debug=true

    ```
    
    - 配置数据库my.cnf
    ```cnf
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
    - 创建数据库
    ```sql
    CREATE DATABASE `ce` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
    ```
    

## 4 开发调试

### 4.1 启动前端项目

!!! Abstract ""
    先在根目录执行安装前端需要的依赖
    ```bash
    yarn install
    ```
    根据根目录下`package.json`文件中`script`的命令启动对应模块
    ```bash 
    #以下为快速启动命令

    #启动基座
    yarn dev:base
    #启动management-center
    yarn dev:manage
    #启动vm-service
    yarn dev:vm
    #启动finance-management
    yarn dev:bill
    #启动security-compliance
    yarn dev:security
    #启动operation-analysis
    yarn dev:operation
    ```
    根据启动后控制台显示地址进行访问

### 4.2 启动后端端项目

!!! Abstract ""
    启动对应模块的Application.java启动类即可

### 4.3 jar包方式启动

!!! Abstract ""
    先执行构建
    ```bash
    ./mvnw clean install
    ```
    

    等待构建完毕，构建完的 jar 包在项目根目录下 target 内
    
    - 将 target 内 repository 文件夹拷贝至 /opt/cloudexplorer/apps/core/ 目录下
    
    - 将 target 内 `eureka-main.jar`, `gateway-main.jar`, `management-center-main.jar` 拷贝至 /opt/cloudexplorer/apps/core/ 目录下

    - 将 target 内其余 jar 包拷贝至 /opt/cloudexplorer/apps/extra/ 目录下

    - 使用 `java -jar xxx.jar` 命令启动各个模块

### 4.4 注意事项

!!! Abstract ""
    第一次启动时，除 eureka 以外，必须先启动 management-center 进行必要数据库的初始化。
