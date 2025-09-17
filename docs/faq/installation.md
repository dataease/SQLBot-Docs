# 安装部署常见问题

## 1 部署的过程中，找不到依赖包：sqlbot-xpack 

!!! Abstract ""
    打开地址： https://test.pypi.org/simple/sqlbot-xpack/ ，找到对应操作系统的依赖，下载即可。

## 2 出现了报错信息：Signature has expired  

!!! Abstract ""
    升级版本至 v1.1.2 及以上。如果不升级版本，清理缓存后可正常运行。

## 3 镜像拉取超时

!!! Abstract ""
    如果是 1Panel 方式安装 SQLBot 遇到镜像拉取超时的问题，可以[参考文档](https://bbs.fit2cloud.com/t/topic/5886)。

    如果以 docker 命令，或者 docker-compose 命令启动时遇到镜像拉取超时，可以给 docker 设置镜像加速，具体步骤：
    
    - 在 /etc/docker/daemon.json 文件中添加镜像地址，若没有此文件，则新建即可。在该文件中添加 registry-mirrors 部分内容，示例如下：
    ```json
    {
        "log-driver":"json-file",
        "log-opts": {"max-size":"50m", "max-file":"3"},
        "registry-mirrors": [
            "https://docker.chenby.cn",
            "https://docker.1panel.live",
            "https://dockerproxy.com",
            "https://docker.mirrors.ustc.edu.cn",
            "https://docker.nju.edu.cn"
        ]
    }
    ```
    - 修改完成后，重启 docker 服务即可


