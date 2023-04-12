# 新增模块

!!! Abstract ""
    本节介绍如何在 CloudExplorer Lite 中新增一个模块服务。

## 修改模块名称、服务端口

!!! Abstract ""
    第一步：打开目录`demo/src/test/java/`下文件 [CreateModuleUtil.java](..%2Fdemo%2Fsrc%2Ftest%2Fjava%2FCreateModuleUtil.java)
!!! Abstract ""
    第二步：修改文件中以下字段为需要生成的新模块信息
    ```java
    /**
    * 模块名
    */
    private static final String NEW_MODULE_NAME = "sample-module";

    /**
    * 模块显示名称
    */
    private static final String NEW_MODULE_DISPLAY_NAME = "样例模块";

    /**
    * 模块显示名称（繁体）
    */
    private static final String NEW_MODULE_DISPLAY_NAME_TW = "樣例模塊";

    /**
    * 模块显示名称（英文）
    */
    private static final String NEW_MODULE_DISPLAY_NAME_EN = "Sample Module";

    /**
    * 后端服务端口
    */
    private static final long NEW_MODULE_PORT = 9021;

    /**
    * 开发时前端服务端口
    */
    private static final long NEW_MODULE_FRONTEND_PORT = 5021;

    /**
    * 服务管理端口
    */
    private static final long NEW_MODULE_MANAGEMENT_PORT = 9921;
        
    ```
## 创建完成新的模块

!!! Abstract ""
    执行 createModule() 方法，等待执行完成，即会在services目录下根据模版创建出新的模块。

## 修改模块icon

!!! Abstract ""
    快速生成模块后，可根据需要再修改模块内的 `/模块名.yml` `/backend/src/main/resources/application.yml` 来修改该模块的icon以及菜单序号。

## 修改模块名称

!!! Abstract ""
    可按需修改模块内的`/backend/src/main/java/com/fit2cloud/ModuleApplication.java`文件名为自己想要的名字。

## 开发模块功能

!!! Abstract ""
    根据需求开发功能模块