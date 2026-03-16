# PanRobot

> `PanRobot` 是 `mingsn115` 项目的简介项目，用于快速了解能力边界与部署方式。

## mingsn115 主要功能

- 影视热门推荐：支持电影/剧集热门内容浏览与搜索。
- 我的订阅：支持电影/剧集订阅管理与更新日历查看。
- 订阅源管理：支持多种订阅源接入、测试与统一配置。
- 115 网盘集成：支持扫码登录、保存目录管理、转存任务跟踪。
- 自动化转存链路：资源抓取、元数据解析、过滤与转存状态追踪。
- 媒体库联动：支持 Emby 媒体库配置、连接测试与同步覆盖分析。
- 系统管理：内置用户/角色权限、系统配置、计划任务与版本检查。

## 部署说明

### 使用 Docker Compose 部署（推荐）

前置要求：

- Docker
- Docker Compose

1. 在服务器或本地新建目录（如 `panrobot`），创建 `docker-compose.yml`。
2. 将下方内容粘贴到 `docker-compose.yml`。
3. 在该目录执行：

```bash
docker compose up -d
```

可选命令：

```bash
docker compose logs -f
docker compose down
```

启动后访问：`http://<你的主机IP>:8080`

默认初始账号：`admin / admin`（首次登录请立即修改密码）。

## docker-compose 文档

```yaml
services:
  panrss:
    container_name: panrss
    image: listyle36/panrss:latest
    restart: unless-stopped
    ports:
      - "${APP_HTTP_PORT:-8080}:80"
      - "${TRANSMISSION_PEER_PORT:-52413}:${TRANSMISSION_PEER_PORT:-52413}/tcp"
      - "${TRANSMISSION_PEER_PORT:-52413}:${TRANSMISSION_PEER_PORT:-52413}/udp"
    environment:
      TZ: ${TZ:-Asia/Shanghai}
      APP_DB_PATH: /opt/app/runtime/data/app.db
      APP_JWT_SECRET: ${APP_JWT_SECRET:-please-change-me}
      APP_COOKIE_SECURE: ${APP_COOKIE_SECURE:-false}
      APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_ENABLED: "true"
      APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_RPC_URL: http://127.0.0.1:9091/transmission/rpc
      APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_USERNAME: ${APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_USERNAME:-admin}
      APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_PASSWORD: ${APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_PASSWORD:-change-me}
      TRANSMISSION_PEER_PORT: ${TRANSMISSION_PEER_PORT:-52413}
      JAVA_OPTS: "${JAVA_OPTS:--Xms256m -Xmx512m}"
      SERVER_ADDRESS: 127.0.0.1
      SERVER_PORT: 8080
    volumes:
      - ./runtime/config:/opt/app/runtime/config
      - ./runtime/data:/opt/app/runtime/data
      - ./runtime/frontend:/opt/app/runtime/frontend
      - ./runtime/transmission:/opt/app/runtime/transmission
```

## 关键配置（建议）

- `APP_HTTP_PORT`：应用对外端口（默认 `8080`）
- `APP_JWT_SECRET`：JWT 密钥（生产环境务必修改）
- `APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_USERNAME`
- `APP_SUBSCRIPTION_TRANSFER_METADATA_TRANSMISSION_PASSWORD`
- `TZ`：时区（默认 `Asia/Shanghai`）

---

如需完整参数说明，可查看 `mingsn115/docs/docker-deploy.md` 与 `mingsn115/docs/transmission-setup.md`。
