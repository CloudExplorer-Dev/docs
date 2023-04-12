# 开发环境搭建

## 1 前端环境准备

- 安装 [node](https://nodejs.org/)
- 启用 corepack  
!!! Abstract ""
    Node.js >=16.10
    ```bash
    corepack enable
    ```
!!! Abstract ""
    Node.js <16.10  
    ```bash
    npm i -g corepack
    ```


## 2 后端环境准备

- 安装 [JDK17](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html)
- 安装 [Maven](https://maven.apache.org/download.cgi)
- 安装 [Docker](https://www.docker.com/)


## 3 开发环境搭建

### 3.1 VMware相关依赖

!!! Abstract ""
    请在项目根目录执行
    ``` bash
    mvn initialize
    ```

### 3.2 接口基本调用参数

!!! Abstract ""
    调用登录以及任意需要认证的接口，在response的header内均会返回当前用户的 JWT token：`CE-TOKEN`

!!! Abstract ""
    后端调用接口需要以下header：
    ```
    CE-TOKEN:  登录获取到的 JWT token
    CE-ROLE:   角色(ADMIN,ORGADMIN,USER)
    CE-SOURCE: 组织/工作空间id（ADMIN角色不传）
    ```
!!! Abstract ""
    若`CE-ROLE`和`CE-SOURCE`与当前用户不匹配，后端认为就是`ANONYMOUS`角色。


### 3.3 后端权限

- 模块基础权限 
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

- 后端权限限制

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
!!! Abstract ""
    实现源码见`CeSecurityExpressionRoot.java`

-  前端权限


!!! Abstract ""
    **html代码中**
    ```html
    <!--
        这里必须使用带模块名的权限格式
        "[module_name]group:require+operate" 或 "[module_name]group:operate"
    -->

    <div v-hasPermission="'[management-center]USER:READ+CREATE'"></div>


    <!-- 也支持数组判断，只要有一个符合就显示 -->

    <div v-hasPermission="['[management-center]USER:READ+CREATE','[management-center]USER:READ+EDIT']"></div>

    ```
!!! Abstract ""

    **ts代码中** 
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