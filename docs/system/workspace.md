# 工作空间

!!! Abstract ""
    工作空间是 SQLBot 中资源隔离与权限管理的基本单元。通过工作空间，可以创建独立的数据分析环境，实现资源的隔离、权限的分层管理。

    - 工作空间之间相互隔离，包含的数据源、会话记录、图表、仪表板等资源互不可见；

    - 同一用户可加入多个工作空间，拥有不同的角色与权限；

    - 系统管理员可创建、配置和管理所有工作空间；

    - 工作空间管理员仅可管理其所属空间

![空间管理](../img/user_manual/system/workspace.png)


## 1 空间管理

!!! Abstract ""
    系统管理员可创建新的工作空间。创打开创建工作空间对话框，填写工作空间名称。

![空间管理](../img/user_manual/system/reate_workspace.png)

!!! Abstract ""
    重命名工作空间：选择目标工作空间，点击【重命名】进行修改。

![空间管理](../img/user_manual/system/rename_workspace.png)

!!! Abstract ""
    系统管理员可删除不再使用的工作空间。

    **注意：删除后，该空间及其资源将被彻底清除，操作不可恢复。**

![空间管理](../img/user_manual/system/delete_workspace.png)



## 2 成员管理

!!! Abstract ""
    工作空间支持添加多个成员，并为其分配空间内的角色（管理员、普通成员），以实现协作与权限控制。

    - 管理员：可管理该空间的成员与资源，进行空间权限配置。
    - 普通成员：仅可访问和使用空间资源，权限受限。

![空间管理](../img/user_manual/system/create_workspacenumber.png)


!!! Abstract ""
    移除成员点击【移除】后，该用户从所授权空间移除。

![空间管理](../img/user_manual/system/delete_workspacenumber.png)

