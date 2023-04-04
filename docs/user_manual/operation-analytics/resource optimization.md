
!!! Abstract " "
    云管平台内置4种优化策略，统计云主机资源的使用情况根据优化策略分析出资源的优化方案推荐给管理员。
![云主机优化总览](./img/operation-analytics/resource optimization/云主机优化总览.png){ width="1235px" } 

## 优化策略

### 1 建议降配云主机

!!! Abstract " "
    -   过去10天内CPU/内存最大使用率小于等于30%；
    -   过去10天内CPU/内存平均使用率小于等于30%。
![建议降配云主机优化策略](./img/operation-analytics/resource optimization/建议降配云主机优化策略.png){ width="1235px" } 

### 2 建议升配云主机

!!! Abstract " "
    -   过去10天内CPU/内存最大使用率大于等于80%；
    -   过去10天内CPU/内存平均使用率大于等于80%。
![建议升配云主机优化策略](./img/operation-analytics/resource optimization/建议升配云主机优化策略.png){ width="1235px" }    

### 3 建议变更付费方式云主机

!!! Abstract " "
    -   包年包月资源持续关机大于10天，建议转按需按量；
    -   按需按量资源持续开机大于5天，建议转包年包月。
![建议变更付费方式优化策略](./img/operation-analytics/resource optimization/建议变更付费方式优化策略.png){ width="1235px" }  

### 4 建议回收云主机

!!! Abstract " "
    持续关机大于30天的闲置资源，建议回收。
![建议回收云主机优化策略](./img/operation-analytics/resource optimization/建议回收云主机优化策略.png){ width="1235px" }   

## 操作说明

!!! Abstract " "
    -   系统支持优化策略自定义设置；
    -   操作入口：点击优化策略模块“小齿轮”图标，进入查询策略页面；
    -   资源查询策略保存后，再次进入会按照最新的策略进行筛选资源；
    -   点击优化策略后，列表会按照所选的优化策略展示相关资源。
![修改查询策略入口](./img/operation-analytics/resource optimization/修改查询策略入口.png){ width="1235px" }  