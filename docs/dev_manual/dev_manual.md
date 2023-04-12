## 1 接口调用

### 1.1 获取JWT-TOKEN

!!! Abstract ""
    使用用户名密码调用登录接口获取 JWT-TOKEN，可以从返回body中获取，也可以从返回的header中获取

    ```curl
    #curl
    
    ```

### 1.2 使用JWT-TOKEN

!!! Abstract ""
    调用登录以及任意需要认证的接口，在 response 的 header 内均会返回当前用户的 JWT-TOKEN：`CE-TOKEN`

    后端调用接口需要以下header：
    ```
    CE-TOKEN:  登录获取到的 JWT token
    CE-ROLE:   角色(ADMIN,ORGADMIN,USER)
    CE-SOURCE: 组织/工作空间id（ADMIN角色不传）
    ```
    若`CE-ROLE`和`CE-SOURCE`与当前用户不匹配，后端认为就是`ANONYMOUS`角色。

    ```curl
    #curl
    
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
    文档准备中...

