# 数据源问题

## 1 达梦数据源无法连接？

!!! Abstract ""
    达梦数据库在启用安全特性后，在添加 SQLBot 数据源时会遇到下面的错误：
    
    ![示例](../img/faq/datasource_dameng.jpg)

    由于不同数据库版本、不同平台架构、不同操作系统的达梦处理方式不同，无法在 SQLBot 镜像里统一处理，所以需要用户根据达梦官方的方案来解决。

    可以参考达梦官方的解决方案处理：

    https://eco.dameng.com/community/question/ec52c8c5b36d5445db1ed8399728fb97

    docker-compose.yml文件修改示例：

    ![示例](../img/faq/dameng_issue.jpg)

## 2 数据源连接无效？

!!! Abstract ""
    数据源本身服务正常，但添加到 SQLBot 中时，提示数据源连接无效。可能的原因：

    - SQLBot 默认以 docker 方式运行，在 SQLBot 容器中访问 localhost，或者 127.0.0.1 等本地地址时，均指向 SQLBot 容器自身，而不是 SQLBot 容器外的其他服务。此时可以将数据源的 IP 换成服务器的内网或公网 IP 即可，如果是本机服务，也可以输入 host.docker.internal
    - docker network 配置有问题，导致容器内无法访问到容器外的地址，可以试着将 docker network mode 调整为 host，然后重启服务
    - 数据源服务器的防火墙是否开放了对应的端口，公有云的服务器还需要检查服务器对应的安全组规则是否开放了对应端口