## 📥 拉取镜像

### 1. 登录 GitHub Container Registry

```bash
# 使用 GitHub Personal Access Token
echo $GITHUB_TOKEN | docker login ghcr.io -u ham0mer --password-stdin

# 或使用密码登录
docker login ghcr.io
```

### 2. 拉取镜像

```bash
# 拉取最新版本
docker pull ghcr.io/ham0mer/tgbot:latest

# 拉取特定版本
docker pull ghcr.io/ham0mer/tgbot:v1.0.0
```

## 🔧 使用镜像

### 方法一：直接运行

```bash
docker run -d \
  --name telegram-bot \
  --restart unless-stopped \
  -e BOT_TOKEN="你的Bot_Token" \
  -e OWNER_ID="你的用户ID" \
  -e SUPABASE_URL="你的Supabase_URL" \
  -e SUPABASE_KEY="你的Supabase_Key" \
  -v $(pwd)/logs:/app/logs \
  ghcr.io/ham0mer/tgbot:latest
```

### 方法二：使用 docker-compose

修改 `docker-compose.yml`：

```yaml
services:
  telegram-bot:
    image: ghcr.io/ham0mer/tgbot:latest
    container_name: telegram-bot
    restart: unless-stopped
    environment:
      - BOT_TOKEN=${BOT_TOKEN}
      - OWNER_ID=${OWNER_ID}
      - SUPABASE_URL=${SUPABASE_URL}
      - SUPABASE_KEY=${SUPABASE_KEY}
      - LOG_LEVEL=info
    volumes:
      - ./logs:/app/logs
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

然后启动：

```bash
# 新版 Docker (推荐)
docker compose up -d

# 旧版 Docker Compose
docker-compose up -d
```

### 启动并查看日志

```bash
# 新版 Docker
docker compose up -d && docker compose logs -f

# 旧版 Docker Compose
docker-compose up -d && docker-compose logs -f

# 只查看日志（已启动的容器）
docker compose logs -f
docker-compose logs -f

# 查看最近 100 行日志
docker compose logs --tail=100 -f
```

## 🔄 更新镜像

### 更新到最新版本

```bash
# 1. 拉取最新镜像
docker pull ghcr.io/ham0mer/tgbot:latest

# 2. 停止并删除旧容器
docker compose down

# 3. 启动新容器
docker compose up -d

# 4. 查看日志确认运行
docker compose logs -f
```

### 更新到特定版本

```bash
# 1. 拉取特定版本
docker pull ghcr.io/ham0mer/tgbot:v1.0.1

# 2. 修改 docker-compose.yml 中的版本标签
# image: ghcr.io/ham0mer/tgbot:v1.0.1

# 3. 重启容器
docker compose down && docker compose up -d
```

### 一键更新脚本

```bash
# 创建更新脚本 update.sh
cat > update.sh << 'EOF'
#!/bin/bash
echo "🔄 正在更新 Telegram Bot..."
docker compose down
docker pull ghcr.io/ham0mer/tgbot:latest
docker compose up -d
echo "✅ 更新完成！"
docker compose logs --tail=50 -f
EOF

# 添加执行权限
chmod +x update.sh

# 执行更新
./update.sh
```
