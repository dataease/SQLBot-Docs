!!! Abstract ""

    本文以阿里云新加坡区 ECS 实例为例，演示 ollama 安装及其与 SQLBot 对接。

## 安装ollama

执行以下命令安装 ollama：
```shell
curl -fsSL https://ollama.com/install.sh | sh
```

输出如下：
```shell
root@iZt4n4e3wmu6ddsc0hb3wbZ:~# curl -fsSL https://ollama.com/install.sh | sh
>>> Installing ollama to /usr/local
>>> Downloading Linux amd64 bundle
######################################################################## 100.0%
>>> Creating ollama user...
>>> Adding ollama user to render group...
>>> Adding ollama user to video group...
>>> Adding current user to ollama group...
>>> Creating ollama systemd service...
>>> Enabling and starting ollama service...
Created symlink /etc/systemd/system/default.target.wants/ollama.service → /etc/systemd/system/ollama.service.
>>> The Ollama API is now available at 127.0.0.1:11434.
>>> Install complete. Run "ollama" from the command line.
WARNING: No NVIDIA/AMD GPU detected. Ollama will run in CPU-only mode.
```

## 修改ollama配置

修改文件ollama.service，让 ollama 访问可被外部访问
```shell
vim /etc/systemd/system/ollama.service
```

加入以下内容：
```
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_ORIGINS=*
```

文件内容如下：
```
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_ORIGINS=*

[Install]
WantedBy=default.target
```

## 重启ollama服务

执行命令重启 ollama:
```shell
service ollama restart
```

## 安装运行大模型

此处以 qwen3-14b 为例，执行以下命令安装大模型：
```shell
ollama run qwen3:14b
```

输出如下：
```shell
root@iZt4n4e3wmu6ddsc0hb3wbZ:~# ollama run qwen3:14b
pulling manifest
pulling a8cc1361f314: 100% ▕████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏ 9.3 GB
pulling ae370d884f10: 100% ▕████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏ 1.7 KB
pulling d18a5cc71b84: 100% ▕████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏  11 KB
pulling cff3f395ef37: 100% ▕████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏  120 B
pulling 78b3b822087d: 100% ▕████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████▏  488 B
verifying sha256 digest
writing manifest
success
>>> Send a message (/? for help)
```

## 安装 OpenWebUI（可选）

!!! Abstract ""

    OpenWebUI 可在 web 界面上与大模型进行交互，非必需。

### 安装docker

在服务器上安装 Docker，本文使用 DataEase 项目组编写安装脚本进行安装，用户可自行选择如何安装 Docker。
```shell
curl -fsSL https://resource.fit2cloud.com/get-docker-linux.sh | bash

# 设置 docker 开机自启，并启动 docker 服务
systemctl enable docker; systemctl daemon-reload; service docker start
```

### 安装OpenWebUI

按官方示例，以 docker 直接启动 OpenWebUI，它会自动关联本地 ollama。
```shell
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

OpenWebUI启动需要一些时间，需确认容器运行状态为「healthy」：
```shell
root@iZt4n4e3wmu6ddsc0hb3wbZ:~# docker ps -a
CONTAINER ID   IMAGE                                COMMAND           CREATED          STATUS                   PORTS                                         NAMES
ba913b54d026   ghcr.io/open-webui/open-webui:main   "bash start.sh"   10 minutes ago   Up 9 minutes (healthy)   0.0.0.0:3000->8080/tcp, [::]:3000->8080/tcp   open-webui
```

启动完成后，可在浏览器上通过 ip:3000来访问，如下图所示：
![openwebui](../img/model_integration/openwebui.png)

## 确认服务状态

在 SQLBot 服务器上访问 ollama 服务，确认网络是通的：
```shell
root@iZt4n4e3wmu6ddsc0hb3wbZ:~#nc -zv 47.237.135.165 11434
Connection to 47.237.135.165 port 11434 [tcp/*] succeeded!
```

## 接入SQLBot

基础模型此处输入之前安装运行的 qwen3:14b。
ollama 默认运行在 11434 端口上，API 域名输入 http://47.237.135.165:11434/v1，注意47.237.135.165换成自己实际的 ip 地址。
API Key 可以随意填写，保存即可。
![ollama](../img/model_integration/ollama_sqlbot.png)