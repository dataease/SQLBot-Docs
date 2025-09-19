!!! Abstract ""
    嵌入式对接分为三类，分别是：

    - 小助手-基础应用
    - 小助手-高级应用
    - 页面嵌入
    
    其中小助手分为两小类，分别是：浮窗嵌入、全屏嵌入。
    
    嵌入式 Demo 地址：https://github.com/dataease/sqlbot-embedded-demo

    三类方式的区别主要按照权限划分，请对照选择合适的嵌入方式：

    | 类别 | 权限 | 数据源 | 场景 |
    |------|---|------|----------|
    | 基础应用 | 游客/员工 | SQLBot | 数据源 | 简单嵌入，无权限要求 |
    | 高级应用 | 宿主权限 | 宿主数据源(api 接口) | 有权限要求，有自己的权限体系 |
    | 页面嵌入 | SQLBot 权限 | SQLBot 数据源 | 有权限要求，无自己的权限体系 |

## 新建应用

### 小助手-基础应用

!!! Abstract ""
    使用 admin 账号登录 SQLBot，切到系统设置菜单-嵌入式管理，新建对应的应用。
    ![示例](../img/embedding/sqlbot_basic_assistant.png)

    填写名称、描述、以及跨域设置
    ![示例](../img/embedding/sqlbot_basic_info.png)

    选择对应的工作空间并设置数据源权限
    ![示例](../img/embedding/sqlbot_basic_datasource.png)
    小助手-基础应用有“游客/员工”简单权限模式，游客只能访问“公共”数据源

### 小助手-高级应用

!!! Abstract ""
    高级应用在新建环节和基础应用的区别就是数据源，通过 API 接口的方式获取。
    ![示例](../img/embedding/sqlbot_advanced_info.png)
    ![示例](../img/embedding/sqlbot_advanced_interface.png)

    开启 AES 加密，那么宿主系统提供的 API 接口中需要对相应的字段进行 AES 加密。
    一般场景下宿主 API 接口会有认证机制，例如 token、cookie 等。这些凭证信息一般情况下会存储在前端页面，填写接口凭证，SQLBot 会在宿主页面上根据填写的凭证信息去获取凭证但不存储，获取的时机是在每次“问数”。SQLBot 根据获取到的凭证去调用宿主系统的 API 接口。有些基于 httpOnly 的 cookie 凭证，SQLBot 无法获取，可以为数据源接口额外定义一个认证凭证。
    ![示例](../img/embedding/sqlbot_advanced_interface2.png)

    注意：目标凭证字段（非必填）支持 js 表达式。

    以 Demo 系统为例：
    ![示例](../img/embedding/sqlbot_advanced_demo.png)

    分别解释接口凭证的字段

    源系统凭证类型 localStorage，凭证名称 sqlbot-embedded-token。

    解释：从 localStirage 中获取 key 为 sqlbot-embedded-token 的信息。代码：

    ```
    var source_val = localStorage.getItem('sqlbot-embedded-token')
    ```

    目标凭证`Bearer ${JSON.parse(JSON.parse(source_val).v)}`。

    解释：根据上一步获取到的 source_val 进一步拼接凭证，代码：
    ```
    source_val = `Bearer ${JSON.parse(JSON.parse(source_val).v)}` 
    ```
    
    目标凭证位置 header，名称 sqlbot-embedded-token。

    解释：把上一部获取到的凭证放在请求头的 sqlbot-embedded-token 中去调 API。

### 页面嵌入

!!! Abstract ""
    填写名称、跨域设置
    ![示例](../img/embedding/sqlbot_page.png)

    记录 APP ID 以及 APP Secret，后面编码环节用得到。

## 宿主系统实现

!!! Abstract ""
    下载 Demo 代码 https://github.com/dataease/sqlbot-embedded-demo

    配置数据库信息
    ![示例](../img/embedding/project_config.png)

    在 frontend 目录执行
    ```
    npm install;npm run build
    ```

    在 backend 目录执行 
    ```
    npm install;npm run dev
    ```

    访问 http://localhost:3000，如下图即运行正常
    ![示例](../img/embedding/project_demo.png)

    根据 SQLBot 中填写的信息填写系统设置表单，保存。当前是游客模式，登录后是 online 模式。

    代码层面基础应用和高级应用嵌入方式基本没有区别。

### 浮窗模式

!!! Abstract ""
    参考 assistan/float.vue 文件
    ![示例](../img/embedding/project_float.png)

    把 sqlbot 提供的嵌入 js 加载到宿主系统。
    
    如果是高级应用，必须登录才可以，因为要从页面获取凭证信息.

    注意：
    
    - online 参数用来区别是否是游客；
    - userFlag 参数用来区分问数记录归属，userFlag 是大于 1 的数字；加载 js 资源最好判断一下是否已经加载过。

    如接入成功，访问小助手浮窗宿主页面，右下角会出现浮动图标，如下图：
    ![示例](../img/embedding/project_float_demo.png)

### 全屏模式

!!! Abstract ""
    参考 assistant/full.vue 文件
    ![示例](../img/embedding/project_fullscreen.png)
    
    如果是高级应用，必须登录才可以，因为要从页面获取凭证信息
    
    接入成功页面如下
    ![示例](../img/embedding/project_fullscreen_demo.png)

### 页面嵌入

!!! Abstract ""
    参考 embedded/index.vue 文件
    ![示例](../img/embedding/project_page.png)

    核心代码与全屏接入一样，只是调用 mounted 方法参数有区别。这里的 token 请在后端生成，避免泄漏 app secret。token 生成的逻辑是把 appId 和 account 作为 payload，appSecret 作为 secret。

    接入成功页面如下
    ![示例](../img/embedding/project_page_demo.png)

    问数需要先选择数据源，与 SQLBot 页面一致
    ![示例](../img/embedding/project_page_demo2.png)

### 高级应用 API 接口

!!! Abstract ""
    接口基本信息

    |项目 |描述|
    |---|---|
    |接口名称 |获取数据源信息|
    |接口地址 |自定义(例如：/datasource)|
    |请求方法 |GET|
    |Content-Type |application/json|
    |权限要求 |根据宿主系统|

    响应

    成功响应 http 200

    响应体

    |名称 |类型 |示例值 |描述|
    |---|---|---|---|
    |code |Integer |200 |状态码，200 或者 0 成功|
    |data |Array[ds] |[{...}, ...] |数据源信息|
    |ds.name |String |demo-ds |名称，require|
    |ds.type |String |pg |类型，require|
    |ds.host |String |localhost |require|
    |ds.port |Integer |5432 |require|
    |ds.dataBase |String |sqlbot_demo |require|
    |ds.user |String |root |require|
    |ds.password |String |xxxx |require|
    |ds.schema |String |public| |
    |ds.comment |String |sqlbot-test-ds |描述，require|
    |ds.tables |Array[table] |[{...},{...},...] |require|
    |table.name |String |categories |require|
    |table.sql |String | |sql 优先级高于 table.name|
    |table.comment |String |商品品类表 |表描述，requre|
    |table.fields |Array[field] |[{...},{...},...] |require|
    |field.name |String |category_name |require|
    |field.comment |String |品类名称 |require|
    |field.type |String |TEXT |db 字段类型 requre|

    api 接口可以考虑预留参数 dsId 以及 tableId，以限定数据源和表进行问数。在多数据源场景下，对提升执行速度以及准确率都有明显效果。

    问数之前，SQLBot 会在宿主页面获取凭证信息调用 API，可以利用 param 类型的凭证作为参数，但是要保证实时更新页面中的凭证。比如设置一个限定数据源 ID 的参数。
    ![示例](../img/embedding/advanced_interface.png)

    前端代码执行 localStorage.setItem(‘dsId’, xxx)

    使用问数功能时，SQLBot 会获取存储在 localStorage 中的 dsId 拼接到 API 接口地址中 http://localhost:3000/api/datasource?dsId=xxx

    如此 ， 我想精确到某个表进行问数时，只需要在宿主页面前端执行
    
    ```
    localStorage.setItem(‘dsId’, xxx);localStorage.setItem(‘tableId’, xxx)
    ```