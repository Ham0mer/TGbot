# Telegram 消息转发机器人

具有图片验证码和用户管理功能的 Telegram 消息转发机器人。

## ✨ 特性

- 🔐 图片验证码防垃圾消息
- 💬 主人回复功能（直接回复转发消息）
- 🚫 用户拉黑/解除拉黑（`/block` 和 `/unblock` 命令）
- ☁️ Supabase 云数据库（数据永不丢失）
- 🐳 Docker 支持
- 🤖 GitHub Actions 自动构建镜像

## 🚀 快速开始
先配置数据库
### 1. 配置 Supabase 数据库

在 [Supabase](https://supabase.com) 创建项目，然后在 SQL Editor 中执行：

```sql
-- 消息映射表
CREATE TABLE message_mappings (
  id BIGSERIAL PRIMARY KEY,
  forwarded_message_id BIGINT UNIQUE NOT NULL,
  user_id BIGINT NOT NULL,
  username TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 已验证用户表
CREATE TABLE verified_users (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT UNIQUE NOT NULL,
  username TEXT,
  verified_at TIMESTAMPTZ DEFAULT NOW()
);

-- 待验证用户表
CREATE TABLE pending_verifications (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT UNIQUE NOT NULL,
  code TEXT NOT NULL,
  attempts INTEGER DEFAULT 0,
  expires_at TIMESTAMPTZ NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 拉黑用户表
CREATE TABLE blocked_users (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT UNIQUE NOT NULL,
  blocked_at TIMESTAMPTZ DEFAULT NOW()
);

-- 创建索引
CREATE INDEX idx_message_mappings_forwarded_id ON message_mappings(forwarded_message_id);
CREATE INDEX idx_verified_users_user_id ON verified_users(user_id);
CREATE INDEX idx_pending_verifications_user_id ON pending_verifications(user_id);
CREATE INDEX idx_blocked_users_user_id ON blocked_users(user_id);
```

### 方法一：使用 GitHub 镜像（推荐）

```bash
# 1. 拉取镜像
docker pull ghcr.io/ham0mer/tgbot:latest

# 2. 运行
docker run -d \
  --name telegram-bot \
  -e BOT_TOKEN="你的Bot_Token" \
  -e OWNER_ID="你的用户ID" \
  -e SUPABASE_URL="你的Supabase_URL" \
  -e SUPABASE_KEY="你的Supabase_Key" \
  ghcr.io/ham0mer/tgbot:latest
```

> 📖 详细说明请查看 [DOCKER_BUILD.md](./DOCKER_BUILD.md)

### 方法二：本地构建

### 1. 配置环境变量

编辑 `.env` 文件：

```env
BOT_TOKEN=你的Bot_Token              # 从 @BotFather 获取
OWNER_ID=你的用户ID                  # 从 @userinfobot 获取
SUPABASE_URL=你的Supabase_URL        # 从 Supabase Dashboard 获取
SUPABASE_KEY=你的Supabase_Key        # 从 Supabase Dashboard 获取
```

### 2. 启动机器人

```bash
# 安装依赖
npm install

# 开发模式
npm run dev

# 生产模式
npm start
```

## 🐳 Docker 部署

```bash
# 配置 .env 文件后
docker compose up -d

# 查看日志
docker compose logs -f
```

##  使用说明

### 用户使用
1. 发送 `/start` 获取验证码
2. 回复验证码完成验证
3. 验证后可正常发送消息

### 主人功能
- **回复用户**：直接回复转发的消息
- **拉黑用户**：回复用户消息并发送 `/block`
- **解除拉黑**：回复用户消息并发送 `/unblock`

##  项目结构

```
TGbot/
 src/
    bot.js                    # Bot 核心
    handlers/messageHandler.js # 消息处理
    filters/adFilter.js       # 验证码系统
    utils/
        supabaseClient.js     # Supabase 客户端
        supabaseDatabase.js   # 数据库管理
 database/supabase_schema.sql  # 数据库表结构
 .env                          # 环境变量
 docker-compose.yml            # Docker 配置
```

## License

MIT
