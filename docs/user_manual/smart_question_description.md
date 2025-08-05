# 智能问数
## 1 功能概述

!!! Tip ""
    **智能问数是 SQLBot 的核心功能模块，支持用户通过自然语言与大模型对话，自动生成图表展示和分析结果。**   
    提供如下核心能力：

    - 自然语言提问：无需 SQL 基础，可直接自然语言提问；【猜你想问】提供智能推荐，降低提问门槛；
    - 图表自动生成：模型根据问题意图智能选择合适图表类型（如柱状图、折线图、表格等）；
    - 图表使用与管理：支持图表类型切换、放大、导出为图片，或添加至仪表板；
    - 数据明细查看与导出：所有图表支持查看数据明细与导出；
    - SQL 查询可见：每次问数均自动生成对应 SQL 查询语句，支持查看与复制；
    - 多轮上下文理解：支持连续提问，自动记忆上下文，实现更自然的分析过程；
    - 历史对话记录：支持查看、重命名、搜索、删除过往对话，便于复用与追溯分析。
    
    **使用前提：需先完成 [AI 模型配置]()，并添加至少一个可用[数据源](../user_manual/datasource_description.md)可发起问数操作。**

![智能问数管理页面](../img/user_manual/chat/chat_index.png)

## 2 创建与管理对话
    
### 2.1 创建对话

!!! Tip ""
    点击【序号 1】 、【序号 2】位置新建对话。
![智能问数管理页面](../img/user_manual/chat/creat_chat.png)

!!! Tip ""
    新建对话步骤：

    - 点击新建对话的按钮；
    - 选择一个数据源；
    - 点击【确认】，进入下一步。
![智能问数管理页面](../img/user_manual/chat/create_chat_choes_data.png)


#### 2.1.1 快捷提问

!!! Tip ""
    系统会根据用户当前选择的数据源结构、字段信息以及过往的提问习惯，智能生成推荐问题。  
    用户可直接点击任一推荐问题，一键发起问数操作，快速获得分析结果。

![智能问数管理页面](../img/user_manual/chat/recommend_questions.png)

!!! Tip ""
    统还会基于当前对话内容自动推荐后续可追问的问题，支持连续深入分析。

![智能问数管理页面](../img/user_manual/chat/related_issues.png)


#### 2.1.2 手动提问
!!! Tip ""
    支持用户通过自然语言手动输入业务问题，示例提问：

    - “近一周各品类的订单量趋势”
    - “哪个渠道的转化率最高？”
    - “相比上周增长最快的部门是哪个？”

![智能问数管理页面](../img/user_manual/chat/manual_question.png)


### 2.2 历史对话
!!! Tip ""
    左侧栏展示所有历史问数记录，支持搜索对话、重命名、删除。

![智能问数管理页面](../img/user_manual/chat/chat_opt.png)

!!! Tip ""
    点击任意历史对话，可以回溯对话内容。

![智能问数管理页面](../img/user_manual/chat/history_chat.png)

## 3 图表展示与操作
### 3.1 图表类型切换
!!! Tip ""
    系统根据问题意图自动选择图表类型，支持可手动切换折线图、柱状图、折线图等。

    **注意：根据展示的数据类型不同，可以切换的图表类型也不一样。**

![智能问数管理页面](../img/user_manual/chat/switch_chart.png)

!!! Tip ""
    图表支持导出和放大查看。


![智能问数管理页面](../img/user_manual/chat/export_img.png)

![智能问数管理页面](../img/user_manual/chat/enlarge_image.png)


### 3.2 明细数据与导出
!!! Tip ""
    点击图表查看数据明细（表格形式），并支持导出为 Excel 文件。

![智能问数管理页面](../img/user_manual/chat/view_details.png)

![智能问数管理页面](../img/user_manual/chat/export_excel.png)

### 3.3 查看 SQL 查询
!!! Tip ""
    每个问答结果均有对呀 SQL 查询语句， 点击图表右上角【查看 SQL】，可复制语句用于验证。

![智能问数管理页面](../img/user_manual/chat/view_sql.png)

![智能问数管理页面](../img/user_manual/chat/sql_details.png)



### 3.4 添加至仪表板
!!! Tip ""
    可将图表收藏至已创建的仪表板，便于集中查看、整理或共享。

