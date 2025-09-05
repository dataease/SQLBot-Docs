!!! Abstract ""
    请使用 v1.1.1 等已发布分支的代码运行 SQLBot。请勿使用 main 等分支，main 等分支代码均处于开发或测试阶段，可能存在较明显功能缺陷。   
    本文所使用源码为 SQLBot main 分支，操作系统为 Ubuntu 24.04，举例说明如何以源码的形式运行 SQLBot 工程。所有操作均在阿里云（新加坡区） 4核8G 环境中执行。

!!! Abstract ""
    目前支持的源码运行环境有： Windows (x86)、Linux（x86 & arm64）、MacOS（x86 & arm64）。

## 1 项目结构

     ```
    ├── backend                                     # 后端 Python 源码
    ├── docker-compose.yaml                         # docker compose 一键运行文件
    ├── Dockerfile                                  # 构建容器镜像使用的 Dockerfile
    ├── frontend                                    # 前端 Vue 源码
    ├── g2-ssr                                      # MCP 使用到的出图工具
    ├── installer                                   # 安装工程源码
    ├── LICENSE                                     # License 申明
    ├── README.md
    ├── sqlbot-assistant-demo.html                  # SQLBot 嵌入式小助手示例
    └── start.sh                                    
     ```

## 2 配置环境

### 2.1 安装 Python

!!! Abstract ""
    默认情况下，Ubuntu 24.04 环境中自带了Python，如果没有 Python 环境，可以通过以下命令安装。
    ```
    apt update -y
    apt upgrade -y
    add-apt-repository ppa:deadsnakes/ppa
    apt install -y python3.11 python3.11-full
    ```

### 2.2 安装 Git
!!! Abstract ""
    Ubuntu 24.04 默认已安装 Git，如果环境中没有 Git，可执行命令安装 Git。
    ```
    apt-get install -y git 
    ```
!!! Abstract ""
    验证 Git。
    ```
    root@iZt4ndy6544y6f1i99ahw0Z:~# git --version
    git version 2.43.0
    ```

### 2.3 安装配置 uv
!!! Abstract ""
    执行命令安装 uv。
    ```
    # 安装 uv
    root@iZt4ndy6544y6f1i99ahw0Z:~# curl -LsSf https://astral.sh/uv/install.sh | sh
    downloading uv 0.8.13 x86_64-unknown-linux-gnu
    no checksums to verify
    installing to /root/.local/bin
    uv
    uvx
    everything's installed!

    To add $HOME/.local/bin to your PATH, either restart your shell or run:

        source $HOME/.local/bin/env (sh, bash, zsh)
        source $HOME/.local/bin/env.fish (fish)

    # 使环境变量生效
    root@iZt4ndy6544y6f1i99ahw0Z:~# source $HOME/.local/bin/env

    # 验证 uv 命令
    root@iZt4ndy6544y6f1i99ahw0Z:~# uv -V
    uv 0.8.13
    ```

### 2.4  安装配置 nodejs
!!! Abstract ""
    执行命令安装 nodejs。
    ```
    # 下载并安装 nodejs，node 版本可能会变化，如版本升级，请更新连接
    wget https://nodejs.org/dist/latest-v22.x/node-v22.19.0-linux-x64.tar.gz
    tar xvf node-v22.19.0-linux-x64.tar.gz
    mv node-v22.19.0-linux-x64 /opt/node-v22.19.0
    
    # 添加环境变量
    echo "export PATH=\$PATH:/opt/node-v22.19.0/bin" >> ~/.bashrc
    
    # 使环境变量生效
    source ~/.bashrc
    ```
!!! Abstract ""
    验证 nodejs。
    ```
    root@iZt4ndy6544y6f1i99ahw0Z:~# node --version
    v22.18.0
    
    root@iZt4ndy6544y6f1i99ahw0Z:~# npm version
    {
        npm: '10.9.3',
        node: '22.18.0',
        acorn: '8.15.0',
        ada: '2.9.2',
        amaro: '1.1.0',
        ares: '1.34.5',
        brotli: '1.1.0',
        cjs_module_lexer: '2.1.0',
        cldr: '47.0',
        icu: '77.1',
        llhttp: '9.3.0',
        modules: '127',
        napi: '10',
        nbytes: '0.1.1',
        ncrypto: '0.0.1',
        nghttp2: '1.64.0',
        openssl: '3.0.16',
        simdjson: '3.13.0',
        simdutf: '6.4.2',
        sqlite: '3.50.2',
        tz: '2025b',
        undici: '6.21.2',
        unicode: '16.0',
        uv: '1.51.0',
        uvwasi: '0.0.21',
        v8: '12.4.254.21-node.27',
        zlib: '1.3.1-470d3a2',
        zstd: '1.5.7'
    }
    ```

### 2.5 安装配置 PostgreSQL
!!! Abstract ""
    从 v1.1.0 版本开始，SQLBot 需要使用到 PG 的向量扩展。为了方便，这里我们使用 docker 镜像来安装 PG。
    如果没有安装 docker 环境，可以先[安装相应的 docker 环境](#41-docker)。

    执行以下命令启动 PG:
    ```
    docker run -d \
        --name pg \
        -p 5432:5432 \
        -v ./data/postgresql:/var/lib/postgresql/data \
        -e POSTGRES_DB=sqlbot \
        -e POSTGRES_USER=root \
        -e POSTGRES_PASSWORD=Password123@pg \
        pgvector/pgvector:pg17
    ```

    验证:
    ```
    root@iZt4n8u2tu6392mezufc9rZ:~# docker exec -it pg psql --version
    psql (PostgreSQL) 17.6 (Debian 17.6-1.pgdg12+1)
    ```


## 3  代码运行

### 3.1 源码准备
!!! Abstract ""
    下载源码到本地
    ```
    root@iZt4ndy6544y6f1i99ahw0Z:~# git clone -b main https://github.com/dataease/SQLBot.git
    Cloning into 'SQLBot'...
    remote: Enumerating objects: 12884, done.
    remote: Counting objects: 100% (589/589), done.
    remote: Compressing objects: 100% (219/219), done.
    remote: Total 12884 (delta 453), reused 425 (delta 369), pack-reused 12295 (from 2)
    Receiving objects: 100% (12884/12884), 19.42 MiB | 16.47 MiB/s, done.
    Resolving deltas: 100% (9092/9092), done.
    ```

### 3.2 配置运行环境

!!! Abstract ""
    **.env 配置**

    在工程目录下创建配置文件 .env，内容如下（根据自己实际情况修改相应配置）：
    ```
    root@iZt4ndy6544y6f1i99ahw0Z:~/SQLBot# cat .env
    PROJECT_NAME="SQLBot"

    # Backend
    BACKEND_CORS_ORIGINS="http://localhost,http://localhost:5173,https://localhost,https://localhost:5173"
    SECRET_KEY=y5txe1mRmS_JpOrUzFzHEu-kIQn3lf7ll0AOv9DQh0s

    DEFAULT_PWD="SQLBot@123456"

    LOG_LEVEL="DEBUG"  # DEBUG, INFO, WARNING, ERROR
    SQL_DEBUG=False

    CACHE_TYPE="memory"

    # Postgres
    POSTGRES_SERVER=localhost
    POSTGRES_PORT=5432
    POSTGRES_DB=sqlbot
    POSTGRES_USER=root
    POSTGRES_PASSWORD=Password123@pg # Change this to your pwd

    SERVER_IMAGE_HOST=http://192.168.1.112:8001/images/
    ```

### 3.3 源码编译
!!! Abstract ""

    ```
    # 编译前端
    cd frontend
    npm install && npm run build
    
    # 编译后端
    cd ../backend
    uv sync
    ```

### 3.4 运行
!!! Abstract ""
    
    ```
    source .venv/bin/activate

    # 启动 g2-ssr，用来为 mcp 生成图形（可选）
    nohup node ../g2-ssr/app.js &

    # 启动 MCP Server（可选）
    nohup uvicorn main:mcp_app --host 0.0.0.0 --port 8001 &

    # 启动 SQLBot
    nohup uvicorn main:app --host 0.0.0.0 --port 8000 --workers 1 &
    ```


## 4 镜像制作
### 4.1 安装 Docker
!!! Abstract ""
    在服务器上安装 Docker，本文使用 DataEase 项目组编写安装脚本进行安装，用户可自行选择如何安装 Docker（需安装 docker compsose）。
    ```
    curl -fsSL https://resource.fit2cloud.com/get-docker-linux.sh | bash
    
    # 设置 docker 开机自启，并启动 docker 服务
    systemctl enable docker; systemctl daemon-reload; service docker start
    ```
### 4.2 制作镜像
!!! Abstract ""
    进入 SQLBot 项目根目录，执行镜像制作命令。
    ```
    docker build -t registry.cn-qingdao.aliyuncs.com/dataease/sqlbot:dev-rc1 .
    ```
!!! Abstract ""
    如下输出日志参考。
    ```
    root@iZt4n65gnl36fhkxbx0qgrZ:~/SQLBot# docker build -t registry.cn-qingdao.aliyuncs.com/dataease/sqlbot:dev-rc1 .
    [+] Building 383.1s (29/29) FINISHED                                                                                                                                                                                                                           docker:default
    => [internal] load build definition from Dockerfile                                                                                                                                                                                                                     0.0s
    => => transferring dockerfile: 2.44kB                                                                                                                                                                                                                                   0.0s
    => [internal] load metadata for registry.cn-qingdao.aliyuncs.com/dataease/sqlbot-python-pg:latest                                                                                                                                                                       1.5s
    => [internal] load metadata for ghcr.io/1panel-dev/maxkb-vector-model:v1.0.1                                                                                                                                                                                            2.3s
    => [internal] load metadata for registry.cn-qingdao.aliyuncs.com/dataease/sqlbot-base:latest                                                                                                                                                                            1.4s
    => [internal] load .dockerignore                                                                                                                                                                                                                                        0.0s
    => => transferring context: 244B                                                                                                                                                                                                                                        0.0s
    => [internal] load build context                                                                                                                                                                                                                                        6.7s
    => => transferring context: 350.82MB                                                                                                                                                                                                                                    6.5s
    => [vector-model 1/1] FROM ghcr.io/1panel-dev/maxkb-vector-model:v1.0.1@sha256:da730ff243f5502304c390ec5a52cf79b2b3a0a46e52100e288850dafbf2c8bf                                                                                                                       116.0s
    => => resolve ghcr.io/1panel-dev/maxkb-vector-model:v1.0.1@sha256:da730ff243f5502304c390ec5a52cf79b2b3a0a46e52100e288850dafbf2c8bf                                                                                                                                      0.0s
    => => sha256:da730ff243f5502304c390ec5a52cf79b2b3a0a46e52100e288850dafbf2c8bf 1.61kB / 1.61kB                                                                                                                                                                           0.0s
    => => sha256:40a3399a26800998a98e7d339fbac319a9705bc761a5614ff0fe7ca95931f03f 482B / 482B                                                                                                                                                                               0.0s
     => => sha256:e9371e013945879dd76389bb40ecec858072064f06d2c7623e12c3e32e9dd40f 448B / 448B                                                                                                                                                                               0.0s
     => => sha256:81824665db64d25d3105e8f62a2b3807ff88faa96a62b0a0e1cfc3aeb6c12e6b 762.38MB / 762.38MB                                                                                                                                                                     107.2s
    => => extracting sha256:81824665db64d25d3105e8f62a2b3807ff88faa96a62b0a0e1cfc3aeb6c12e6b                                                                                                                                                                                6.6s
    => [stage-3 1/9] FROM registry.cn-qingdao.aliyuncs.com/dataease/sqlbot-python-pg:latest@sha256:36cf8054190210d91ffcd648014d9f93510ca122b2d4e056c33ce7c29bdb4b93                                                                                                        38.0s
    => => resolve registry.cn-qingdao.aliyuncs.com/dataease/sqlbot-python-pg:latest@sha256:36cf8054190210d91ffcd648014d9f93510ca122b2d4e056c33ce7c29bdb4b93                                                                                                                 0.0s
    => => sha256:36cf8054190210d91ffcd648014d9f93510ca122b2d4e056c33ce7c29bdb4b93 856B / 856B                                                                                                                                                                               0.0s
    => => sha256:cccfbb03d0ee118c7491a6bad94649013a8ec0faf58fe91b81272280f326fa55 4.11kB / 4.11kB                                                                                                                                                                           0.0s
    => => sha256:25387b42b9f27e647631f4c721791239bfff95c1768a0a0d0651525f9884212c 12.67kB / 12.67kB                                                                                                                                                                         0.0s
    => => sha256:d16299ff3f87ff575cc6869657c06c344343b2941738c73387c21b28e0d45908 122.25kB / 122.25kB                                                                                                                                                                       6.7s
    => => extracting sha256:d16299ff3f87ff575cc6869657c06c344343b2941738c73387c21b28e0d45908                                                                                                                                                                                0.0s
    => => sha256:2b715279f55e071fc7c3c5d7b117ce1d087cf17260416a43887b030f5c9b8036 1.45MB / 1.45MB                                                                                                                                                                           7.3s
    => => extracting sha256:2b715279f55e071fc7c3c5d7b117ce1d087cf17260416a43887b030f5c9b8036                                                                                                                                                                                0.1s
    => => sha256:94c286c44c81cc64bc39bb85da3c704f4a3a818d4f4dd019dc2fea8d032471b8 13.68MB / 13.68MB                                                                                                                                                                         9.8s
    => => extracting sha256:94c286c44c81cc64bc39bb85da3c704f4a3a818d4f4dd019dc2fea8d032471b8                                                                                                                                                                                1.4s
    => => sha256:20d375d36dbdbbe154faf8eb0b03152b0236d44ea90d9666a1d41e693976d369 3.15MB / 3.15MB                                                                                                                                                                          10.8s
    => => sha256:50d19ad3b50cf75827ef36f72fe41ca6077621ceda8f29d49f3b7637340457ff 17.39MB / 17.39MB                                                                                                                                                                        13.6s
    => => extracting sha256:20d375d36dbdbbe154faf8eb0b03152b0236d44ea90d9666a1d41e693976d369                                                                                                                                                                                1.0s
    => => extracting sha256:50d19ad3b50cf75827ef36f72fe41ca6077621ceda8f29d49f3b7637340457ff                                                                                                                                                                                0.2s
    => => sha256:23f08e3574c23361ebf69d3db0cdb1693b48e181f01e9b20a81c93e108543f1f 171.45MB / 171.45MB                                                                                                                                                                      31.6s
    => => extracting sha256:23f08e3574c23361ebf69d3db0cdb1693b48e181f01e9b20a81c93e108543f1f                                                                                                                                                                                5.8s
    => [ssr-builder 1/5] FROM registry.cn-qingdao.aliyuncs.com/dataease/sqlbot-base:latest@sha256:5f1602bbb306a4f24fb73797eeaa41f539f6dd236936a896063265a9cd655faf                                                                                                         61.3s
    => => resolve registry.cn-qingdao.aliyuncs.com/dataease/sqlbot-base:latest@sha256:5f1602bbb306a4f24fb73797eeaa41f539f6dd236936a896063265a9cd655faf                                                                                                                      0.0s
    => => sha256:59e22667830bf04fb35e15ed9c70023e9d121719bb87f0db7f3159ee7c7e0b8d 28.23MB / 28.23MB                                                                                                                                                                         5.9s
    => => sha256:5f1602bbb306a4f24fb73797eeaa41f539f6dd236936a896063265a9cd655faf 1.61kB / 1.61kB                                                                                                                                                                           0.0s
    => => sha256:60e3dbde8a7285935e47e6122ee0c440d935df1af5277a89e45ed3af8c7f9da0 1.44kB / 1.44kB                                                                                                                                                                           0.0s
    => => sha256:d4112c627f99470a588ac096d77321e2941f1ae66f297b3eae40cbaae0212041 6.30kB / 6.30kB                                                                                                                                                                           0.0s
    => => sha256:abd846fa1cdb2ae1ef7731213cd4f0c40b05fdbeeaef9301a4dc9575b2088ece 3.51MB / 3.51MB                                                                                                                                                                           1.3s
    => => sha256:b7b61708209ad8f9b9a11c61dc9df90f74c1e39eddc169936146259febc2ec24 16.21MB / 16.21MB                                                                                                                                                                         3.8s
    => => sha256:4085babbc5702254267393a22fc7f0d644efddd41dc328f81b1549c13a210b4e 249B / 249B                                                                                                                                                                               4.0s
    => => sha256:41aa4b05abc967567fcc949d31d533c12de0112e53749a28b28bedd32002d89a 17.39MB / 17.39MB                                                                                                                                                                         6.3s
    => => sha256:4ae0ababaedadcb88fa606c101cac74b0544d9b97bc347f637d85f9168eb7b7e 220.27MB / 220.27MB                                                                                                                                                                      53.1s
    => => extracting sha256:59e22667830bf04fb35e15ed9c70023e9d121719bb87f0db7f3159ee7c7e0b8d                                                                                                                                                                                1.7s
    => => extracting sha256:abd846fa1cdb2ae1ef7731213cd4f0c40b05fdbeeaef9301a4dc9575b2088ece                                                                                                                                                                                0.1s
    => => extracting sha256:b7b61708209ad8f9b9a11c61dc9df90f74c1e39eddc169936146259febc2ec24                                                                                                                                                                                0.8s
    => => extracting sha256:4085babbc5702254267393a22fc7f0d644efddd41dc328f81b1549c13a210b4e                                                                                                                                                                                0.0s
    => => extracting sha256:41aa4b05abc967567fcc949d31d533c12de0112e53749a28b28bedd32002d89a                                                                                                                                                                                0.6s
    => => extracting sha256:4ae0ababaedadcb88fa606c101cac74b0544d9b97bc347f637d85f9168eb7b7e                                                                                                                                                                                7.7s
    => [stage-3 2/9] RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime &&     echo "Asia/Shanghai" > /etc/timezone                                                                                                                                                5.3s
    => [stage-3 3/9] COPY start.sh /opt/sqlbot/app/start.sh                                                                                                                                                                                                                 0.1s
    => [stage-3 4/9] COPY g2-ssr/*.ttf /usr/share/fonts/truetype/liberation/                                                                                                                                                                                                0.2s
    => [sqlbot-builder 2/8] RUN mkdir -p /opt/sqlbot/app /opt/sqlbot/frontend                                                                                                                                                                                               5.1s
    => [ssr-builder 2/5] WORKDIR /app                                                                                                                                                                                                                                       5.1s
    => [ssr-builder 3/5] COPY g2-ssr/app.js g2-ssr/package.json /app/                                                                                                                                                                                                       0.1s
    => [sqlbot-builder 3/8] WORKDIR /opt/sqlbot/app                                                                                                                                                                                                                         0.1s
    => [sqlbot-builder 4/8] COPY frontend /tmp/frontend                                                                                                                                                                                                                    56.8s
    => [ssr-builder 4/5] COPY g2-ssr/charts/* /app/charts/                                                                                                                                                                                                                  0.1s
    => [ssr-builder 5/5] RUN npm install                                                                                                                                                                                                                                   52.6s
    => [sqlbot-builder 5/8] RUN cd /tmp/frontend; npm install; npm run build; mv dist /opt/sqlbot/frontend/dist                                                                                                                                                            46.6s
    => [sqlbot-builder 6/8] RUN test -f "./uv.lock" &&     --mount=type=cache,target=/root/.cache/uv     --mount=type=bind,source=backend/uv.lock,target=uv.lock     --mount=type=bind,source=backend/pyproject.toml,target=pyproject.toml     uv sync --frozen --no-insta  0.2s
    => [sqlbot-builder 7/8] COPY ./backend /opt/sqlbot/app                                                                                                                                                                                                                  0.2s
    => [sqlbot-builder 8/8] RUN --mount=type=cache,target=/root/.cache/uv    uv sync --extra cpu                                                                                                                                                                           77.2s
    => [stage-3 5/9] COPY --from=sqlbot-builder /opt/sqlbot /opt/sqlbot                                                                                                                                                                                                    36.6s
    => [stage-3 6/9] COPY --from=ssr-builder /app /opt/sqlbot/g2-ssr                                                                                                                                                                                                       15.0s
    => [stage-3 7/9] COPY --from=vector-model /opt/maxkb/app/model /opt/sqlbot/models                                                                                                                                                                                      11.8s
    => [stage-3 8/9] WORKDIR /opt/sqlbot/app                                                                                                                                                                                                                                0.0s
    => [stage-3 9/9] RUN mkdir -p /opt/sqlbot/images /opt/sqlbot/g2-ssr                                                                                                                                                                                                     0.2s
    => exporting to image                                                                                                                                                                                                                                                  55.1s
    => => exporting layers                                                                                                                                                                                                                                                 55.1s
    => => writing image sha256:dfe2e3227a661b8be4d2ac611b1d50c688151e917396a2f4636ecd540f7ad35e                                                                                                                                                                             0.0s
    => => naming to registry.cn-qingdao.aliyuncs.com/dataease/sqlbot:dev-rc1                                                                                                                                                                                                0.0s

    1 warning found (use docker --debug to expand):
    - SecretsUsedInArgOrEnv: Do not use ARG or ENV instructions for sensitive data (ENV "POSTGRES_PASSWORD") (line 62)
    ```
    
    使用 docker images 可查看镜像是否成功打包.
    ```
    root@iZt4n65gnl36fhkxbx0qgrZ:~/SQLBot# docker images
    REPOSITORY                                         TAG       IMAGE ID       CREATED         SIZE
    registry.cn-qingdao.aliyuncs.com/dataease/sqlbot   dev-rc1   dfe2e3227a66   8 minutes ago   3.98GB
    ```