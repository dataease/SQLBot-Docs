# 其他问题

## 1 RAG 在 SQLBot 的哪些地方起到作用了呢？

!!! Abstract ""
    目前 RAG 主要用于 SQLBot 根据问题自动匹配数据源、数据表、术语、SQL 示例，减少问数时对大模型的 token 消耗。
    
    - 问题中明确指定了使用哪个数据源
    - 在 SQLBot 数据源的描述信息中添加了与问题相关的信息，问数时会将问题与数据源描述信息进行相似度匹配，以此确定使用哪个数据源
    - SQLBot 中仅有一个数据源时无需确认，会默认使用该数据源

## 2 SQLBot 支持 API 调用吗？

!!! Abstract ""
    SQLBot 支持通过 API 进行调用，认证过程采用 RSA 加密机制。调用时，用户名和密码需使用 RSA 公钥进行加密，而对应的密钥存储在 PostgreSQL 数据库的 rsa 表中。
    
    如需了解 SQLBot 自身的 API 调用方式，可通过浏览器开发者工具查看其网络请求。目前系统尚未提供基于 API Key 的认证方式，但支持通过 Token 进行接口调用。用户可在登录过程中获取 Token，相应的获取接口可在登录时观察请求获得。

    完整的 API 接口列表可在 SQLBot 访问地址后追加 /docs进行查看，例如：https://your-sqlbot-domain/docs。