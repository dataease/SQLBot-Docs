!!! Tip ""
    SQLBot 支持通过 MCP 进行服务端图表渲染与智能问数处理。

## 1 服务配置
!!! Tip ""
    SQLBot MCP Server 默认监听端口为 8001，使用 SSE 协议进行通信。基本配置如下：

    ```
    {
       "sqlbot_mcp": {
           "url": "http://<your-server-ip>:8001/mcp", // 将 IP 替换为部署机器的地址
           "transport": "sse"
        }
    }
    ```

!!! Tip ""
    修改 SQLBot 的 .env 配置文件：

    ```
    # MCP 服务图片路径，默认路径
    MCP_IMAGE_PATH=/opt/sqlbot/images

    # MCP 后端渲染服务地址，默认路径
    MCP_IMAGE_HOST=http://localhost:3000

    # 图片访问路径， {sqlbot mcp 服务ip/域名}[: {sqlbot mcp 服务端⼝}]/images/ 
    # 注意跨域 、https 、http协议安全等可能导致图片无法加载的问题
    SERVER_IMAGE_HOST=https://<your-server-ip>/images/

    ```
## 2 MCP 工具说明
!!! Tip ""
    SQLBot 的 MCP Server 提供两个内置工具：mcp_start 和 mcp_question，分别用于初始化对话和提交问题。

### 2.1 mcp_start 工具
!!! Tip ""
    用于启动一次智能问数对话，获取 access_token 和 chat_id。

    | 参数名      | 说明          |
    | --------    | ----------- |
    | username | SQLBot 用户名   |
    | password |  SQLBot 用户密码 |


    返回示例：
    ```
    {
        "code": 0,
        "data": {
            "access_token": "<JWT_TOKEN>",
            "chat_id": 1330
        },
        "msg": null
    }
    ```
     - access_token：身份验证使用；

     - chat_id：唯一对话上下文 ID，后续问数保持一致可维持上下文状态。


### 2.2 mcp_question 工具
!!! Tip ""
    用于在已初始化的问数上下文中提交用户问题，并返回对应 SQL、可视化结果及图表图片地址。
    
| 参数名      | 说明                             |
    | -------- | ------------------------------ |
    | token    |  `mcp_start` 返回的 `access_token` |
    | chat\_id | `mcp_start` 返回的 `chat_id`      |
    | question |  用户的提问问题 |

!!! Tip ""
    返回结果示例（Markdown 格式）：

    ```
    ```sql SELECT "s"."区域", COUNT(*) AS "count" FROM "public"."Sheet1_c27345b66e" "s" GROUP BY "s"."区域" ORDER BY "s"."区域" ``` | 区域 | 数量 | |:-----|-----:| | 东区 | 269 | | 北区 | 321 | | 南区 | 275 | | | 4 | ### generated chart picture ![column](https://sqlbot.fit2cloud.cn/images/c_1330_r_2976.png)
    ```
### 2.3 对接流程说明
!!! Tip ""
    对接步骤：

    - 步骤 1：调用 mcp_start 工具。 获取初始化问数所需的 access_token 和 chat_id。
    - 步骤 2：调用 mcp_question 工具使用上一步返回的 access_token 和 chat_id，传入用户自然语言问题 question，提交问题请求。

    建议：

    - 每次调用 mcp_start 会生成新的 chat_id；
    - 对话过程中保持 chat_id 一致，才能维持上下文；
    - 建议缓存 access_token 和 chat_id，在一次对话中只调用一次 mcp_start。

## 3 使用示例

###  3.1 MaxKB 集成示例
!!! Tip ""
    示例一:
    步骤⼀： 创建⼀个高级编排 ，添加用户输⼊用于在问数开始时输⼊ SQLBot 用户名和密码 。

    步骤⼆： 添加⼀个 AI 对话 ，启用⼯具中的 MCP 功能 ，填⼊ MCP Sever 配置 。

    步骤三： 选择 AI 模型和编辑提示词 ，提示词参考如下：
    
    ```
    # 回答要求：
    按需调用 mcp_start 和 mcp_question ⼯具获取信息回答问题 。
    mcp_start 账号密码：
    username:{{global.username}} password:{{global.password}}
    ⼯具调用逻辑：
    首先调用 mcp_start ⼯具， 获取 access_token 和 chat_id ， 帮我记住这两个参数， 之后不要重复调用 mcp_start ，直接使用 即可； 然后再调用 mcp_question ⼯具， 其中 token 和 chat_id 参数是调用 mcp_start ⼯具返回， question 是用户提问 。
    # 用户提问：
    {{开始.question}}
    # 输出要求：
    mcp_question 的返回 ，请直接输出展示 。

    ```

![集成示例](img/maxkb_ai_effect.png)
!!! Tip ""
    示例二：

    步骤⼀： 创建或进入一个高级编排类型的应用。

    步骤二： 添加输入节点在，节点中定义输入变量： username（必填） 、password（必填）

    步骤三： 添加条件判断，在开始节点后添加条件分支（IF）。判断条件：access_token 是否为空。为空：执行登录逻辑，调用 MCP 工具 mcp_start。    

    配置 MCP 工具：

    - 输入全局变量：
    ```
    {
    "username": "{{username}}",
    "password": "{{password}}"
    }
    
    ```
     - MCP Server Config 服务配置：
    ```
    {
    "sqlbot_mcp": {
    "uri": "http://<SQLBot_MCP_IP>:8001/mcp",
    "transport": "sse"
    }
    }
    ```
    - MCP 工具返回包含 chat_id 和 access_token 的 JSON。解析返回值，添加工具节点（Python）来解析 JSON：
    ```
    import json
    def main1(data):
    json_obj = json.loads(data[0])
    return {"token":json_obj["data"]["access_token"], "chat_id":json_obj["data"]["chat_id"]}
    ```
     添加变量赋值节点，将 chat_id 和 access_token 存储为会话变量，供后续 MCP 调用使用。

    - 执行后续 MCP 业务调用，MCP Server Config 配置：

          ```
          {
          "sqlbot_mcp": {
          "uri": "http://<SQLBot_MCP_IP>:8001/mcp",
          "transport": "sse"
              }
          }
          ```
     步骤四：在流程末尾添加指定回复节点，将 MCP 的输出结果作为回复内容。输入有效的 username 与 password 测试登录及 MCP 功能调用是否正常。

![集成示例](img/maxkb_effect.png)

###  3.2 Dify 集成示例
!!! Tip ""
    步骤⼀： 进入需要配置的工作空间，创建或进入一个 Chatflow 类型的应用。

    步骤二： 添加输入节点在，节点中定义输入变量： username（必填） 、password（必填）

    步骤三： 添加条件判断，在开始节点后添加条件分支（IF）。判断条件：access_token 是否为空。为空：执行登录逻辑，调用 MCP 工具 mcp_start。    

    配置 MCP 工具参数（以 mcp_start 为例）：

    - 输入参数：
    ```
    {
    "username": "{{username}}",
    "password": "{{password}}"
    }
    
    ```
     - MCP 服务配置：
    ```
    {
    "sqlbot_mcp": {
    "uri": "http://<SQLBot_MCP_IP>:8001/mcp",
    "transport": "sse"
    }
    }
    ```
    - MCP 工具返回包含 chat_id 和 access_token 的 JSON。解析返回值，添加 代码执行 节点（Python）来解析 JSON：
    ```
    import json
    
    def main(arg1: str) -> dict:
    json_obj = json.loads(arg1)
    return {
    "chat_id": json_obj["data"]["chat_id"],
    "access_token": json_obj["data"]["access_token"]
    }

    ```
     添加变量赋值节点，将 chat_id 和 access_token 存储为全局变量，供后续 MCP 调用使用。

    - 执行后续 MCP 业务调用

          ```
          {
          "sqlbot_mcp": {
          "uri": "http://<SQLBot_MCP_IP>:8001/mcp",
          "transport": "sse"
              }
          }
          ```
     步骤四：在流程末尾添加回答节点，将 MCP 返回的内容回复给用户。输入有效的 username 与 password 测试登录及 MCP 功能调用是否正常。


![集成示例](img/dify_mcp.png)

![集成示例](img/dify_mcp_effect.png)