# 平台对接

!!! Abstract ""

    支持企业微信、钉钉、飞书接入，支持扫码登录、免密登录等。

    **注意**：平台对接为 X-Pack 功能。

![对接企业微信](../img/user_manual/system/平台对接扫码登录.png)


## 1 企业微信设置
### 1.1 配置平台对接信息
!!! Abstract ""
    DataEase 对接企业微信，需要填写以下信息：

    - 企业 ID
    - 应用 ID
    - 应用密钥

    对接信息的获取，需要以管理员权限登录企业微信后台，如下图所示。

![对接企业微信](../img/user_manual/system/企业微信对接1.png)

!!! Abstract ""
    获取企业 ID，获取路径：企业微信后台->我的企业->企业信息，如下图所示。

![对接企业微信](../img/user_manual/system/企业微信对接2.png)

!!! Abstract ""
    获取应用 ID 与应用密钥，获取路径：企业微信后台->应用管理；  
    首先需要创建一个对应的应用，在【应用管理】栏的【应用】中，滚动到页面最下方，点击【创建应用】。

![对接企业微信](../img/user_manual/system/企业微信对接3.png)

!!! Abstract ""
    在弹出的【创建应用】对话框中输入应用的相关信息后确认即可，此处我们创建一个名叫“SQLBot 测试”的应用。

![对接企业微信](../img/user_manual/system/企业微信对接4.png)

!!! Abstract ""
    创建完成后会自动跳转到新建应用的详情界面，按照如下图所示获取应用 ID 与应用密钥即可。

![对接企业微信](../img/user_manual/system/企业微信对接5.png)

### 1.2 应用权限设置

!!! Abstract ""
    SQLBot 对接企业微信，让安装 SQLBot 的服务器可以访问企业微信的接口，需要设置企业可信域名。可信域名若使用非标准端口需要带上端口号，此处不可使用 IP 地址。  
    可参考[可信域名验证配置](https://kb.fit2cloud.com/?p=915e0151-5581-406f-ac2e-22afb9b3b4bf  )操作步骤可参考。若校验不通过，可按照提示信息做相关操作即可。
![对接企业微信](../img/user_manual/system/企业微信对接6.png)

![对接企业微信](../img/user_manual/system/企业微信对接7.png)

!!! Abstract ""
    企业可信 IP 为本企业服务器的 IP 地址，仅所配 IP 可通过接口获取企业数据；  
    进入应用，在最下方可以看到“企业可信 IP”，输入 SQLBot 服务器 IP 即可，回调域名 sqlbot.fit2cloud.com 的 IP 为212.1.121.111，则在配置中填写该 IP。

![对接企业微信](../img/user_manual/system/企业微信对接8.png)

![对接企业微信](../img/user_manual/system/企业微信对接9.png)

!!! Abstract ""
    在 Web 网页里授权回调域，域名请根据实际情况进行修改。

![对接企业微信](../img/user_manual/system/企业微信对接10.png)

!!! Abstract ""
    要支持扫码登录，需要进行企业微信授权登录的设置。

![对接企业微信](../img/user_manual/system/企业微信对接11.png)

!!! Abstract ""
    根据以上信息完成下图界面的信息录入并校验、保存。

![对接企业微信](../img/user_manual/system/企业微信校验成功.png)

### 1.3 企业微信免登设置

!!! Abstract ""
    应用主页地址的构造可以参考企业微信的在线文档[《构造网页授权链接——构造企业oauth2链接》](https://developer.work.weixin.qq.com/document/path/91120#%E6%9E%84%E9%80%A0%E4%BC%81%E4%B8%9Aoauth2%E9%93%BE%E6%8E%A5)。  
    应用主页地址主要结构如下面的链接所示，注意下面的红字部分：  
    https://open.weixin.qq.com/connect/oauth2/authorize?appid=CORPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&agentid=AGENTID#&state=fit2cloud-wecom-client#wechat_redirect
    详细说明如下：

    - CORPID - 企业 ID
    - REDIRECT_URI - 回调地址，例如 https://sqlbot.fit2cloud.com/#/de-auto-login?type=wecom，调整域名部分即可
    - SCOPE - 填 snsapi_base 即可
    - AGENTID - 应用 ID

    主页地址示例：
    https://open.weixin.qq.com/connect/oauth2/authorize?appid=ww8c9076cfd8ea1fc1&redirect_uri=https://dataease.fit2cloud.com/#/de-auto-login?type=wecom&response_type=code&scope=snsapi_base&agentid=1000001#&state=fit2cloud-wecom-client#wechat_redirect

!!! Abstract ""
    在应用的【应用主页】栏点击【配置】，将上一步获取到的应用主页地址填入电脑端网址即可。

![对接企业微信](../img/user_manual/system/企业微信应用首页.png)

!!! Abstract ""
    在企业微信工作台中找到 DataEase 应用，PC 端和移动端点击该应用即可免登访问 SQLBot。

![对接企业微信](../img/user_manual/system/企业微信界面.png)
## 2 钉钉设置
### 2.1 配置平台对接信息
!!! Abstract ""
    DataEase 对接钉钉，需要填写以下信息：

    - 应用 ID
    - 应用 Key
    - 应用密钥

!!! Abstract ""
    获取应用 ID 和应用密钥，需要在钉钉工作台的【应用管理】中创建一个对应的应用，可以先登录工作台 https://oa.dingtalk.com/index.htm#/microApp/microAppList；  
    创建应用，需要在【应用管理】中，滚动到页面最下方，点击【自建应用】。

![对接钉钉平台](../img/user_manual/system/钉钉创建应用.png)

!!! Abstract ""
    在弹出的【创建企业内部应用】对话框中输入应用的相关信息后确认即可。

![对接钉钉平台](../img/user_manual/system/钉钉填写应用名称.png)

!!! Abstract ""
    获取应用信息，在应用创建完成后，可以在刚才创建的“SQLBot 测试应用”中的【应用凭证】里找到所需的信息，其中：

    - AgentId - 应用 ID
    - AppKey - 应用 Key
    - AppSecret - 应用密钥

![对接钉钉平台](../img/user_manual/system/钉钉应用信息.png){

### 2.2 应用权限设置

!!! Abstract ""
    授权登录，要支持扫码登录，需要进行钉钉授权登录的设置，进入应用“SQLBot 测试应用”，在【分享设置】的【回调域名】里中添加授权回调域，注意域名需要与访问 SQLBot 平台的域名一致。

![对接钉钉平台](../img/user_manual/system/钉钉分享设置.png)


!!! Abstract ""
    同时在【安全设置】的【重定向 URL】里中添加授权回调域，注意域名一致。
   

![对接钉钉平台](../img/user_manual/system/钉钉安全设置.png)

!!! Abstract ""
    权限控制，进入到【权限管理】中，添加权限。

![对接钉钉平台](../img/user_manual/system/钉钉权限开通1.png)

!!! Abstract ""
    所需权限如下图所示。

![对接钉钉平台](../img/user_manual/system/钉钉权限开通2.png)

!!! Abstract ""
    按照以上信息完成下图所示的平台对接配置即可。

![对接钉钉平台](../img/user_manual/system/钉钉校验成功.png)


### 2.3 钉钉免登设置

!!! Abstract ""
    在钉钉开放平台的【应用能力】栏中，进入【网页应用】，设置【应用首页】和【PC端首页地址】。地址格式为：http(s)://xxx.xxx.xxx/?client=dingtalk&corpId=CORPID。注意 corpId=CORPID 需替换成真正的 CORPID。可实现 PC 端和移动端免密登陆。

![对接钉钉平台](../img/user_manual/system/钉钉免密.png)

![对接钉钉平台](../img/user_manual/system/钉钉应用内部界面.png)


##  3 飞书设置


### 3.1 配置平台对接信息

!!! Abstract ""
    SQLBot 对接飞书，需要填写以下信息：

    - 应用 ID
    - 应用密钥

    获取应用 ID 和应用密钥，需要以管理员权限登录飞书管理后台，在【工作台】的【应用管理】中创建一个对应的应用。

![对接飞书平台](../img/user_manual/system/飞书对接1.png)

![对接飞书平台](../img/user_manual/system/飞书对接2.png)

![对接飞书平台](../img/user_manual/system/飞书对接3.png)

!!! Abstract ""
    创建应用，在弹出的【创建应用】对话框中输入应用的相关信息后确认即可，此处创建一个名叫“SQLBot 测试”的应用。

![对接飞书平台](../img/user_manual/system/飞书对接4.png)

!!! Abstract ""
    点击【确定创建】按钮后，完成应用创建并进入应用。提醒进行配置和发布，再进行配置后，进行版本发布。

![对接飞书平台](../img/user_manual/system/飞书对接5.png)

!!! Abstract ""
    新建 App，添加网页应用，设置桌面端主页为 SQLBot 服务器地址。如果是 IP 地址，则填入 http(s)://域名/。
![对接飞书平台](../img/user_manual/system/飞书对接6.png)

![对接飞书平台](../img/user_manual/system/飞书对接7.png)

### 3.2 应用权限设置

!!! Abstract ""
    在【安全设置】里添加重定向 URL，即 SQLBot 服务器地址。与网页应用设置的地址域名一致。

![对接飞书平台](../img/user_manual/system/飞书对接8.png)

!!! Abstract ""
    应用授权，在应用的【权限管理】里进行应用的相关授权，具体权限参考下图：
![对接飞书平台](../img/user_manual/system/飞书对接9.png)

!!! Abstract ""
    新建应用版本，在应用的【版本管理与发布】中创建应用版本，如下图所示。

![对接飞书平台](../img/user_manual/system/飞书对接10.png)

![对接飞书平台](../img/user_manual/system/飞书对接11.png)

!!! Abstract ""
    申请线上发布并自动审核。

![对接飞书平台](../img/user_manual/system/飞书对接12.png)


!!! Abstract ""
    获取应用 ID 和应用密钥，完成下图所示的平台对接配置即可。

![对接飞书平台](../img/user_manual/system/飞书对接校验成功.png)


### 3.3 飞书免登设置

!!! Abstract ""
    在飞书开放平台中，选择 DataEase 应用。在【应用功能】下的【网页】里，开启网页功能，并配置【桌面端主页】和【移动端主页】。地址格式：http(s)://xxx.xxx.xxx.xxx/?client=lark。可实现 PC 端和移动端免密登陆。

![对接飞书平台](../img/user_manual/system/飞书免密登录.png)

![对接飞书平台](../img/user_manual/system/飞书应用内部界面.png)

##  4 同步用户

### 4.1 组织用户同步

!!! Abstract ""
     在 SQLBot 的系统管理的用户管理界面中，点击右上角的【同步用户】按钮,选择相应的同步用户界面。

![同步用户](../img/user_manual/system/同步用户按钮.png)

!!! Abstract ""
     勾选所需同步的用户后,点击确认即可。

![同步用户](../img/user_manual/system/同步平台用户界面.png)

![同步用户](../img/user_manual/system/同步用户成功.png)
