# 新增一个扩展模块

## 1 创建一个新模块

!!! Abstract ""
    打开目录`demo/src/test/java/`下文件 `CreateModuleUtil.java`

!!! Abstract ""
    修改文件中以下字段为需要生成的新模块信息
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

## 2 完成创建新模块

!!! Abstract ""
    执行 createModule() 方法，等待执行完成，即会在services目录下根据模版创建出新的模块。

## 3 修改模块icon及菜单显示

!!! Abstract ""
    快速生成模块后，可根据需要再修改模块内的 `/模块名.yml` `/backend/src/main/resources/application.yml` 来修改该模块的icon以及菜单序号。

## 4 修改模块启动类名称

!!! Abstract ""
    可按需修改模块内的`/backend/src/main/java/com/fit2cloud/ModuleApplication.java`文件名为自己想要的名字。


## 5 启动模块服务
  
!!! Abstract ""
     要启动前端项目请先在项目根目录执行
      ``` bash
      yarn install
      ```
     启动前端项目
      ``` bash
      yarn workspace 你的新模块名 run dev
      ```
!!! Abstract ""
    为了以后启动方便，你也可以在根目录的`package.json`中的`scripts`下添加你的模块
      ``` json
      "scripts": {
        ...
        "dev:你的新模块名": "yarn workspace 你的新模块名 run dev",
        "lint:你的新模块名": "yarn workspace 你的新模块名 run lint",
      },

      ```