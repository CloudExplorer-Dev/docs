## 1 接口调用

### 1.1 获取 JWT-TOKEN

!!! Abstract ""
    使用用户名密码调用登录接口获取 JWT-TOKEN，从返回的header中获取

    ```bash
    curl -L -X POST 'localhost:9000/login' \
    -i \
    -H 'Content-Type: application/json' \
    -d '{
    "username": "admin",
    "password": "cloudexplorer"
    }'
    ```
    登录成功返回
    ```
    HTTP/1.1 200 OK
    CE-TOKEN: eyJhbGciOiJIUzUxMiIsInppcCI6IkdaSVAifQ.H4sIAAAAAAAA_42RvU7DMBSFX6Xy3ES5jhM7mUCoY1uppQMiDI5zA0ZOHOVHIFVdmWFj5QV4K8RrkJ_SDiyM9_M5x-fq7knXYE1isk-IzhISJ0RyT_AwohENUAovAy7AFwGl6GOWIiRknoyuUhY4ObJClyM-oe_Pj-_Xl6-39xFjKVODQ3xbdziAQmpz9l7kuqXK2C5zlS1GS_VgyyGq7IwZRtk0T7aeGmIqM6XyECNEYKkXsjzgIRfoeykPchwDGtvVaipjrJJmhKpG2eK1PrakHvUdjzngzcCLWRAH_rRdlf1Hprq6xrLdWDPpLlfr1c1yvduOr3WPl7I673CUb3-LHbGuRjNE1IVQuD64U7qx97r824HOgMUMYuCnX5pecXt3IHPy2Or-lpwLFUkUTgRMOowz3xFApcMpsEgKmgYy6MVNl_biq4Wz2y42_axlS-K-AlABIedzgs_VBHw2gMMPnsAUJC0CAAA.tLfEcyT8oYAlu6nkRGW-8yRm8z3uaOvoGVf-7oXkZ1bLEc1Y84mi7x9_aygMY7vhS4kT49EhnIcZY3L5lfajLw
    Content-Type: application/json
    Content-Length: 66
    Cache-Control: no-cache, no-store, max-age=0, must-revalidate
    Pragma: no-cache
    Expires: 0
    X-Content-Type-Options: nosniff
    X-Frame-Options: SAMEORIGIN
    X-XSS-Protection: 1 ; mode=block
    Referrer-Policy: no-referrer
    
    {"requestId":null,"code":200,"message":"登录成功","data":null}
    ```
    其中，相应头中的 `CE-TOKEN` 就是我们需要的 JWT-TOKEN

### 1.2 使用 JWT-TOKEN

!!! Abstract ""
    调用登录以及任意需要认证的接口，在 response 的 header 内均会返回当前用户的 JWT-TOKEN：`CE-TOKEN`

    后端调用接口需要以下header：
    ```
    CE-TOKEN:  登录获取到的 JWT token
    CE-ROLE:   角色(ADMIN,ORGADMIN,USER)
    CE-SOURCE: 组织/工作空间id（ADMIN角色不传）
    ```
    若`CE-ROLE`和`CE-SOURCE`与当前用户不匹配，后端认为就是`ANONYMOUS`角色。

    ```bash
    #以获取当前用户接口为例
    curl -L -X GET 'localhost:9000/api/user/current' \
    -H 'CE-SOURCE;' \
    -H 'CE-ROLE: ADMIN' \
    -H 'CE-TOKEN: eyJhbGciOiJIUzUxMiIsInppcCI6IkdaSVAifQ.H4sIAAAAAAAA_42Ru07DMBSGX6Xy3ES-Jk4mEOrYVmrpgAiDY5-AkXNRLgKp6soMGysvwFshXoNcSjuwMJ7P___7Pzp71DVQoxjtE2RNguIEqRDLMIhoRAUoiQ0JJWFSUAoMTAokQfNkdBUqh8lhcluM-IS-Pz--X1--3t5HDIVKHQzxbd3BAHJl3dl7kdmWald2xtdlPlqqh7IYoorOuWFUTfNU1lNDSJXROgsgAiA8xQHPRBiEEhhOQ5HBGNCUXa2nMq7Uyo1Q16BauLbHlhRT5mHuETwjOOYiFmzarjL_kemurqFoN6WbdJer9epmud5tx9e6x0tVnXc4yre_xY7YVqOZRNQngfQZ8ad0V97b4m8HOiM8ZmGMo9MvTa-4vTugOXpsbX9LkbIMBGdeSBX3uKLSkxkGj2uIVBZgI3jai5su7cVXC2-3XWz62aoWxX0FQiXhNJojeK4mwPgADj8zS7tQLQIAAA.mj7QaLQwiR5OYHQ5bya1Hd3PmgU4gFdpMb2bcNdONbpn_0TCAHIDkZSFTMWd2LfVRNo5Z7QrD32M6xJsSe_JVA'
    ```

## 2 后端权限

### 2.1 模块基础权限

!!! Abstract ""
    模块内基础权限配置，在每个模块内的`PermissionConstants.java`中配置`MODULE_PERMISSION_BUILDER`

    ```java
    private static final ModulePermission.Builder MODULE_PERMISSION_BUILDER = new ModulePermission.Builder()
                .group(
                        //用户管理
                        new PermissionGroup.Builder()
                                .id(GROUP.USER)    //权限组
                                .name("i18n_permission_user")
                                .permission(
                                        //查看用户
                                        new Permission.Builder()
                                                .operate(OPERATE.READ)    //权限操作
                                                .name("i18n_permission_user_read")
                                                .role(RoleConstants.ROLE.ADMIN)    //生效的角色
                                                .role(RoleConstants.ROLE.ORGADMIN)
                                )
                                .permission(
                                        //创建用户
                                        new Permission.Builder()
                                                .require(OPERATE.READ)      //该权限的基础权限
                                                .operate(OPERATE.CREATE)    //权限操作
                                                .name("i18n_permission_user_create")
                                                .role(RoleConstants.ROLE.ADMIN)    //生效的角色
                                                .role(RoleConstants.ROLE.ORGADMIN)
                                )
                                
                        //...
                )
                .group(
                        //权限管理
                        new PermissionGroup.Builder()
                                .id(GROUP.ROLE)    //权限组
                                .name("i18n_permission_role")
                                .permission(
                                        new Permission.Builder()
                                                .operate(OPERATE.READ)
                                                .name("i18n_permission_role_read")
                                                .role(RoleConstants.ROLE.ADMIN)
                                                .role(RoleConstants.ROLE.ORGADMIN)
                                )
                )
                //...
                ;
    
    
    ```

### 2.2 后端权限限制

!!! Abstract ""
    支持以下几种方式：
    ```java
    /**
     * 当前模块内的权限判断，第一个参数是权限组group，第二个参数是权限操作operate
     */
    @PreAuthorize("hasCePermission('USER', 'READ')")
    public void mMethod() {
            //...
    }
    
    /**
     * 推荐！
     * 当前模块内的权限判断，参数为权限组与操作的组合: "group:operate"
     */
    @PreAuthorize("hasCePermission('USER:READ')")
    public void mMethod() {
            //...
    }
    
    /**
     * 推荐！
     * 在上面的基础上支持匹配多个权限
     */
    @PreAuthorize("hasAnyCePermission('USER:READ')")
    public void mMethod() {
            //...
    }
    
    /**
     * security默认的方法
     * 需要带上模块名称 "[module_name]group:require+operate" 或 "[module_name]group:operate"
     */
    @PreAuthorize("hasAuthority('[management-center]USER:READ+CREATE')")
    public void mMethod() {
            //...
    }
    
    /**
     * security默认的方法
     * 在上面的基础上支持匹配多个权限
     */
    @PreAuthorize("hasAnyAuthority('[management-center]USER:READ+CREATE')")
    public void mMethod() {
            //...
    }
    
    
    ```

    实现源码见`CeSecurityExpressionRoot.java`

## 3 前端权限

### 3.1 html代码

!!! Abstract ""
    ```html
    <!--
        这里必须使用带模块名的权限格式
        "[module_name]group:require+operate" 或 "[module_name]group:operate"
    -->
    
    <div v-hasPermission="'[management-center]USER:READ+CREATE'"></div>
    
    
    <!-- 也支持数组判断，只要有一个符合就显示 -->
    
    <div v-hasPermission="['[management-center]USER:READ+CREATE','[management-center]USER:READ+EDIT']"></div>
    
    ```

### 3.2 ts代码

!!! Abstract ""
    ```ts
    import { usePermissionStore } from "@commons/stores/modules/permission";
    const permissionStore = usePermissionStore();
    
    /**
     * 以下方法的返回值即为是否有权限 
     * 这里必须使用带模块名的权限格式
     * "[module_name]group:require+operate" 或 "[module_name]group:operate"
     */
    permissionStore.hasPermission("[vm-service]CLOUD_SERVER:STOP")
    
    /**
     * 也支持数组判断，只要有一个符合就返回true
     */
    permissionStore.hasPermission(["[vm-service]CLOUD_SERVER:START", "[vm-service]CLOUD_SERVER:STOP"])
    
    ```

## 4 菜单路由

!!! Abstract ""
    模块内菜单路由配置，在每个模块内的`MenuConstants.java`中配置`MENUS_BUILDER`

### 4.1 单级菜单

!!! Abstract ""
    ```java
        private static final Menus.Builder MENUS_BUILDER = new Menus.Builder()
                .menu(new Menu.Builder()
                        .name("cloud_account")                                       //菜单唯一ID
                        .title("云账号")                                              //菜单显示名称
                        .path("/cloud_account")                                      //菜单路由
                        .componentPath("/src/views/CloudAccount/index.vue")          //路由对应的vue页面，与该项目中前端的frontend目录下的文件路径对应
                        .icon("icon_cloud_outlined")                                 //菜单中展示的icon
                        .order(1)                                                    //菜单中的排序
                        .redirect("/cloud_account/list")                             //如果路径是一个基础路径，其vue组件内有router-view，下面可能包含其他的操作路径，则需要指定默认跳转的下一级子路径
                        .requiredPermission(new MenuPermission.Builder()             //指定访问该菜单所需要的权限
                                .role(RoleConstants.ROLE.ADMIN)
                                .permission(GROUP.CLOUD_ACCOUNT, OPERATE.READ))
                        .childOperationRoute(new Menu.Builder()                       //子路由，配置这个路由不会在前端菜单中展示，结合上面有router-view的组件，可以通过url路径访问到
                                .name("cloud_account_list")
                                .path("/list")                                        //实际会根据父路径拼接成/cloud_account/list进行访问
                                .title("列表")
                                .saveRecent(true)                                     //是否作为最近访问进行记录，在首页最近访问中可以看到
                                .componentPath("/src/views/CloudAccount/list.vue")
                                .requiredPermission(new MenuPermission.Builder()
                                        .role(RoleConstants.ROLE.ADMIN)
                                        .permission(GROUP.CLOUD_ACCOUNT, OPERATE.READ)
                                )
                        )
                        .childOperationRoute(new Menu.Builder()
                                .name("cloud_account_create")
                                .path("/create")
                                .title("创建")
                                .quickAccess(true)                                     //是否支持首页快速访问
                                .saveRecent(true)
                                .quickAccessName("添加云账号")                           //在首页快速访问中展示的名称
                                .quickAccessIcon("icon_cloud_outlined")                //在首页快速访问中展示的图标（已废弃）
                                .componentPath("/src/views/CloudAccount/create.vue")
                                .requiredPermission(new MenuPermission.Builder()
                                        .role(RoleConstants.ROLE.ADMIN)
                                        .permission(GROUP.CLOUD_ACCOUNT, OPERATE.CREATE)
                                )
                        )
                        //...
                )
    ```

### 4.2 有层级的菜单

!!! Abstract ""
    ```java
        private static final Menus.Builder MENUS_BUILDER = new Menus.Builder()
            .menu(new Menu.Builder()
                  .name("system_setting")
                  .title("系统设置")
                  .path("/system_setting")
                  .icon("icon-setting")
                  .order(5)
                  .requiredPermission(new MenuPermission.Builder()       //若有多个角色，需要多次追加requiredPermission
                      .role(RoleConstants.ROLE.ADMIN)
                      .permission(GROUP.PARAMS_SETTING, OPERATE.READ)
                      .permission(GROUP.ABOUT, OPERATE.READ)             //同一角色的MenuPermission.Builder()下可以追加多个permission
                  )
                  .childMenu(new Menu.Builder()                          //次级菜单
                      .name("about")
                      .title("关于")
                      .path("/about")
                      .componentPath("/src/views/About/index.vue")
                      .saveRecent(true)
                      .order(2)
                      .requiredPermission(new MenuPermission.Builder()
                          .role(RoleConstants.ROLE.ADMIN)
                          .permission(GROUP.ABOUT, OPERATE.READ)
                      )
        
                //...
                )
            )
    ```

## 5 定时任务

!!! Abstract ""
    模块内定时任务配置，在每个模块内的`JobConstants.java`中配置`JobSetting`
    
    ***任务类型***
    
    - cron表达式Job
    
    ```
       new JobSetting(
                    // 任务执行器 -> 任务执行器 集成AsyncJob 实现Job接口
                    CloudAccountSyncJob.BillAuthJob.class,
                    // 任务名称
                    "任务名称",
                    // 任务组
                    com.fit2cloud.common.constants.JobConstants.Group.SYSTEM_GROUP.name(),
                    // 任务描述
                    "cron表达式任务",
                    // 任务默认参数
                    Map.of(),
                    // cron表达式
                    "0 0 0 * * ? *",
                    // 当前云平台是否展示 
                    platform -> false,
                    // 当前云平台是否可开启/关闭任务
                    platform -> false,
                    // 当前云平台是否可修改定时任务时间 platform为当前云账号 
                    platform -> false
            );
    ```
    
    - 时间间隔Job
    
    ```
     new JobSetting(
                    // 任务执行器 -> 任务执行器 集成AsyncJob 实现Job接口
                    CloudAccountSyncJob.BillAuthJob.class,
                    // 任务名称
                    "任务名称",
                    // 任务组
                    com.fit2cloud.common.constants.JobConstants.Group.SYSTEM_GROUP.name(),
                    // 任务描述
                    "cron表达式任务",
                    // 任务默认参数
                    Map.of(),
                    // 间隔时间
                    25,
                    // 间隔单位
                    DateBuilder.IntervalUnit.MINUTE,
                    // 当前云平台是否展示
                    platform -> false,
                    // 当前云平台是否可开启/关闭任务
                    platform -> false,
                    // 当前云平台是否可修改定时任务时间 platform为当前云账号 
                    platform -> false
            );
    ```
    
    ***定时任务组类型***
    
    定时任务组用来区分任务类型,目前任务组类型可以分为俩大类:公共任务组,模块自定义任务组
    
    - 公共定时任务组
    
        - SYSTEM_GROUP
          ```
          系统定时任务组:
          使用系统定时任务,项目启动会根据任务配置创建一个定时任务;
          ```
        - CLOUD_ACCOUNT_RESOURCE_SYNC_GROUP
          ```
          云账号资源同步定时任务组:
          使用云账号定时任务,项目启动会根据任务参数,为每一个云账号(cloudAccountShow函数返回true的)都分配一个定时任务;
          并且可以在管理中心定时任务设置中修改任务信息;
          定时任务参数: 同步区域参数选择;
          ```
      - 模块自定义任务组
    
          - CLOUD_ACCOUNT_BILL_SYNC_GROUP
            ```
            云账单模块自定义任务组:
            使用云账号定时任务,项目启动会根据任务参数,为每一个云账号(cloudAccountShow函数返回true的)都分配一个定时任务;
            并且可以在管理中心定时任务设置中修改任务信息;
            定时任务参数: 同步方式以及参数配置;
            ```
          - CLOUD_COMPLIANCE_RESOURCE_SYNC_GROUP
            ```
            安全合规模块自定义任务组:
            使用云账号定时任务,项目启动会根据任务参数,为每一个云账号(cloudAccountShow函数返回true的)都分配一个定时任务;
            并且可以在管理中心定时任务设置中修改任务信息;
            定时任务参数: 没有任务参数配置;
            ```


