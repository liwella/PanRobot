# PanRobot

## 1. PanRobot 是做什么的

`PanRobot` 是一个面向网盘（目前仅支持了115）影视资源管理场景的私有化应用，核心目标是把“订阅发现、资源转存、媒体库联动、系统运维”整合到统一后台中，降低日常追剧追更和资源管理成本。

## 2. PanRobot 有哪些功能

- 热门推荐：提供电影与剧集的热门内容浏览、检索与详情查看。
- 我的订阅：支持电影/剧集订阅、状态管理与更新日历跟踪。
- 订阅源管理：支持多订阅源接入、配置测试与统一维护。
- 115 网盘集成：支持扫码登录、保存目录管理、转存任务查看。
- 自动化转存链路：包含资源抓取、元数据解析、过滤与转存状态追踪。
- 媒体库联动：支持 Emby 配置、连接测试与同步覆盖分析。
- 系统管理：支持用户角色权限、系统配置、计划任务与版本检查。

## 3. 部署方式

推荐使用 `docker-compose` 部署。

1. 新建目录并创建 `docker-compose.yml`。
2. 写入以下示例配置：

```yaml
services:
  panrobot:
    image: listyle36/panrobot:latest
    container_name: panrobot
    restart: unless-stopped
    ports:
      - 9529:80
      # transmission peer端口
      - 52413:52413/tcp
      - 52413:52413/udp
    environment:
      TZ: Asia/Shanghai
      APP_JWT_SECRET: qwer1234
      # 是否启用transmission磁力种子元数据解析
      APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_ENABLED: true
      # transmission地址（默认即可，内置了一个transmission-daemon）
      APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_RPC_URL: http://127.0.0.1:9091/transmission/rpc
      # 内置transmission-daemon账号
      APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_USERNAME: admin
      # 内置transmission-daemon密码
      APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_PASSWORD: qwer1234
      # 内置transmission-daemon peer端口
      TRANSMISSION_PEER_PORT: 52413
      # 内置transmission-daemon tracker
      TRANSMISSION_DEFAULT_TRACKERS: http://1337.abcvg.info:80/announce
        http://bt.okmp3.ru:2710/announce
        http://ipv4.rer.lol:2710/announce
        http://ipv6.rer.lol:6969/announce
        http://lucke.fenesisu.moe:6969/announce
        http://nyaa.tracker.wf:7777/announce
    volumes:
      # 后端配置
      - ./config:/opt/app/runtime/config
      # 前端配置
      - ./frontend:/opt/app/runtime/frontend
      # transmission-daemon 配置
      - ./transmission:/opt/app/runtime/transmission
      # 数据库
      - ./data:/opt/app/runtime/data
```

3. 启动服务：

```bash
docker compose up -d
```

访问地址：`http://<服务器IP>:9529`  
默认账号：`admin / admin`（首次登录请立即修改密码）。

4. ⚠️ 免责声明 & 合规说明

本应用仅为方便网盘分享与转存，所有资源均来自网络用户的公开分享内容：
- 开发者非资源的上传者、所有者或版权方，不对资源的合法性、准确性、完整性承担责任。
- 对于涉政、色情、暴力等违规资源的分享创建，非法内容传播，本应用坚决抵制。

用户在使用本工具时需知悉：
- 需自行核实资源版权归属，确保合规使用，避免侵犯第三方权益；
- 对下载、存储、传播资源可能引发的法律纠纷、数据安全风险（如病毒感染）等，由用户自行承担全部责任；
- 开发者不对上述风险导致的任何损失承担责任；
- 如您继续使用本应用，则视为已完整阅读、理解并接受以上所有声明内容。
