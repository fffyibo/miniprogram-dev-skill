# 后端部署指南

> **⚠️ 免责声明**：本文档中的价格、配置步骤和功能说明基于编写时的各云服务商信息整理，可能随服务商政策调整而变化。实际部署前请以各服务商官网最新信息为准。

## 部署方案概览

| 方案 | 适用场景 | 成本 | 运维难度 |
|------|---------|------|---------|
| **本地开发** | 开发调试阶段 | 免费 | 无 |
| **云服务器（CVM/ECS）** | 中大型项目，需要完全控制 | 按配置付费 | 较高 |
| **Serverless（云函数）** | 小型项目，按量付费 | 按调用次数付费 | 低 |
| **容器服务（Docker）** | 微服务架构，需要灵活扩展 | 按配置付费 | 中等 |
| **Vercel/Netlify** | 前端项目 + API Routes | 免费额度够用 | 最低 |

## 本地开发阶段

本地开发时，直接在本机运行后端服务，前端小程序在开发者工具中请求 `http://localhost:端口号`。

**注意事项**：
- 需要在开发者工具中关闭域名校验（各平台设置不同，详见 SKILL.md）
- 手机真机预览时，手机和电脑需在同一局域网，且使用电脑的局域网 IP（如 `http://192.168.1.100:3000`）
- 本地开发仅用于调试，不能用于正式上线

## 云服务器部署（以 Node.js 为例）

### 购买云服务器

**腾讯云 CVM**：
1. 访问 https://cloud.tencent.com/product/cvm
2. 选择配置：推荐 2 核 4G 起步
3. 选择系统：推荐 Ubuntu 22.04 LTS
4. 设置密码或 SSH 密钥

**阿里云 ECS**：
1. 访问 https://www.aliyun.com/product/ecs
2. 选择配置：推荐 2 核 4G 起步
3. 选择系统：推荐 Ubuntu 22.04 LTS

### 安装 Node.js 环境

```bash
# 连接服务器
ssh root@你的服务器IP

# 安装 Node.js（使用 nvm）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm install 20
nvm use 20

# 验证
node -v
npm -v

# 安装 pnpm
npm install -g pnpm
```

### 上传代码

```bash
# 方式一：使用 Git
git clone https://github.com/your-username/your-project.git
cd your-project
pnpm install

# 方式二：使用 scp
scp -r ./your-project root@你的服务器IP:/home/
```

### 使用 PM2 管理进程

PM2 是 Node.js 进程管理工具，能自动重启、日志管理、负载均衡。

```bash
# 安装 PM2
npm install -g pm2

# 启动应用
pm2 start server.js --name my-app

# 常用命令
pm2 list          # 查看所有进程
pm2 logs my-app   # 查看日志
pm2 restart my-app # 重启
pm2 stop my-app    # 停止
pm2 delete my-app  # 删除

# 设置开机自启
pm2 startup
pm2 save
```

### 配置 Nginx 反向代理

```bash
# 安装 Nginx
apt update
apt install nginx -y

# 配置
nano /etc/nginx/sites-available/my-app
```

配置内容：

```nginx
server {
    listen 80;
    server_name your-domain.com;  # 你的域名

    location / {
        proxy_pass http://127.0.0.1:3000;  # Node.js 服务端口
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```bash
# 启用配置
ln -s /etc/nginx/sites-available/my-app /etc/nginx/sites-enabled/
nginx -t  # 测试配置
systemctl restart nginx
```

### 配置 HTTPS（必须）

小程序要求后端接口必须使用 HTTPS。

```bash
# 安装 Certbot
apt install certbot python3-certbot-nginx -y

# 申请证书（需要域名已解析到服务器）
certbot --nginx -d your-domain.com

# 自动续期
certbot renew --dry-run
```

## Serverless 部署（云函数）

### 微信云开发

微信小程序自带的云开发能力，无需单独部署服务器。

```javascript
// 云函数示例：cloudfunctions/login/index.js
const cloud = require('wx-server-sdk')
cloud.init()

exports.main = async (event, context) => {
  const wxContext = cloud.getWXContext()
  return {
    openid: wxContext.OPENID,
    unionid: wxContext.UNIONID
  }
}
```

**适用场景**：微信小程序专属项目，功能较简单
**限制**：仅限微信平台，复杂业务支持有限

### 腾讯云云函数（SCF）

```bash
# 安装 Serverless Framework
npm install -g serverless

# 创建项目
serverless create --template tencent-nodejs --name my-api

# 部署
serverless deploy
```

### 阿里云函数计算（FC）

```bash
# 安装 Funcraft
npm install -g @alicloud/fun

# 初始化项目
fun init

# 部署
fun deploy
```

## Docker 容器部署

### 编写 Dockerfile

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```

### 构建和运行

```bash
# 构建镜像
docker build -t my-app .

# 运行容器
docker run -d -p 3000:3000 --name my-app my-app

# 查看日志
docker logs my-app

# 停止容器
docker stop my-app
```

### 使用 Docker Compose（含数据库）

```yaml
# docker-compose.yml
version: '3'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
    environment:
      - DATABASE_URL=mysql://root:password@db:3306/miniprogram

  db:
    image: mysql:8
    environment:
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=miniprogram
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

```bash
docker-compose up -d
```

## Vercel 部署（适合 API Routes）

Vercel 适合部署前端项目和 Serverless API，免费额度足够小型项目使用。

```bash
# 安装 Vercel CLI
npm install -g vercel

# 登录
vercel login

# 部署
vercel

# 生产环境部署
vercel --prod
```

**适用场景**：
- 前端项目（H5 版本）
- 简单的 API 服务
- 不需要数据库的项目（或搭配 PlanetScale、Supabase 等云数据库）

## 域名与备案

### 购买域名

- 腾讯云：https://cloud.tencent.com/product/dns
- 阿里云：https://wanwang.aliyun.com/
- 华为云：https://www.huaweicloud.com/product/domain.html

### 域名备案（国内服务器必须）

- 国内服务器必须备案才能使用域名访问
- 备案周期：约 1-3 周
- 香港/海外服务器无需备案，但延迟较高

### 域名解析

在云服务商的 DNS 管理中添加 A 记录，将域名指向服务器 IP。

## 数据库部署

### MySQL 安装（Ubuntu）

```bash
apt install mysql-server -y
mysql_secure_installation

# 创建数据库和用户
mysql -u root -p
```

```sql
CREATE DATABASE miniprogram_db CHARACTER SET utf8mb4;
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON miniprogram_db.* TO 'app_user'@'localhost';
FLUSH PRIVILEGES;
```

### MongoDB 安装

```bash
# 导入公钥
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | apt-key add -

# 添加源
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-6.0.list

# 安装
apt update
apt install -y mongodb-org

# 启动
systemctl start mongod
systemctl enable mongod
```

## 部署检查清单

- [ ] 服务器环境已安装（Node.js、数据库等）
- [ ] 代码已上传并安装依赖
- [ ] PM2 已配置并启动
- [ ] Nginx 反向代理已配置
- [ ] HTTPS 证书已申请并配置
- [ ] 域名已解析（如需要）
- [ ] 数据库已初始化
- [ ] 环境变量已配置（数据库密码、API Key 等）
- [ ] 防火墙已开放必要端口（80、443、数据库端口）
- [ ] 日志监控已配置

## 常见问题

**Q：云服务器选什么配置？**
A：小型项目 2 核 4G 足够；中型项目 4 核 8G；大型项目根据实际并发量选择。可以先用低配，后续按需升级。

**Q：要不要用宝塔面板？**
A：宝塔面板能简化服务器管理（可视化操作），适合不熟悉 Linux 命令的开发者。但会占用一定系统资源。

**Q：数据库放哪里？**
A：小型项目可以和应用放同一台服务器；中大型项目建议使用云数据库（如腾讯云 CDS、阿里云 RDS），更稳定且有备份。

**Q：如何监控服务器？**
A：使用云服务商自带的监控（如腾讯云云监控、阿里云云监控），或安装 Prometheus + Grafana。
