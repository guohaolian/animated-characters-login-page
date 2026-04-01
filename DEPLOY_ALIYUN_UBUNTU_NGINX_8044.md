# 阿里云 Ubuntu + Nginx(8044) 部署文档（Vite dist 静态站点）

目标：
- Nginx 监听端口：`8044`
- 站点根目录（dist 内容落地）：`/opt/logininterested/www/frontend`
- 项目类型：Vite + Vue3（SPA），需要 `try_files ... /index.html` 兜底

---

## 1. 前置条件检查

### 1.1 服务器与网络
1. Ubuntu 服务器已能通过 SSH 登录。
2. 阿里云安全组放行 **8044/TCP**（入方向）。
3. 若服务器自身启用了防火墙（UFW），也要放行 8044（见下文）。

### 1.2 构建方式选择（推荐 A）
- **A（推荐）本地构建 + 只上传 dist：** 服务器只负责 Nginx 静态托管，依赖更少。
- B 服务器构建：服务器安装 Node.js + npm，在服务器上跑 `npm ci && npm run build`。

---

## 2. 在服务器上准备目录

```bash
sudo mkdir -p /opt/logininterested/www/frontend
sudo chown -R www-data:www-data /opt/logininterested
sudo chmod -R 755 /opt/logininterested
```

说明：
- Nginx 默认用户通常是 `www-data`（Ubuntu）。
- `www-data` 需要对目录具有读取权限。

---

## 3A. 方式 A：本地构建并上传 dist（推荐）

### 3A.1 在本地（你的开发机）构建
在项目根目录执行：

```bash
npm install
npm run build
```

确认生成 `dist/` 目录。

### 3A.2 上传 dist 到服务器
你可以用 `scp` 或 `rsync`。

#### 方案 1：scp
在本地执行（示例：服务器 IP 为 `1.2.3.4`，用户名 `root`）：

```bash
scp -r ./dist/* root@1.2.3.4:/opt/logininterested/www/frontend
```

#### 方案 2：rsync（更适合增量发布）

```bash
rsync -avz --delete ./dist/ root@1.2.3.4:/opt/logininterested/www/frontend
```

上传后在服务器上检查：

```bash
ls -la /opt/logininterested/www/frontend
```

应能看到 `index.html`、`assets/` 等文件。

---

## 3B. 方式 B：在服务器上构建（可选）

> Vite 5 通常要求 Node.js 版本较新（建议 Node.js 18+）。

### 3B.1 安装 Node.js（示例：使用 Ubuntu 源/或 NodeSource）
这里给出一种常见做法（NodeSource，Node 18）：

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
node -v
npm -v
```

### 3B.2 拉代码并构建
把项目上传到服务器某目录（例如 `/opt/logininterested/src`），然后：

```bash
cd /opt/logininterested/src
npm ci
npm run build
sudo rsync -avz --delete ./dist/ /opt/logininterested/www/frontend
sudo chown -R www-data:www-data /opt/logininterested/www/frontend
```

---

## 4. 安装与配置 Nginx

### 4.1 安装 Nginx

```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl enable --now nginx
```

### 4.2 放行服务器防火墙（如使用 UFW）

```bash
sudo ufw allow 8044/tcp
sudo ufw status
```

> 如果你只依赖阿里云安全组、未启用 UFW，可跳过。

### 4.3 写入 Nginx 配置（使用 sites-available）
将本仓库的示例配置文件内容写入到服务器：

- 站点配置文件：`/etc/nginx/sites-available/logininterested`

然后启用站点（创建软链接到 `sites-enabled`）：

```bash
sudo ln -sfn /etc/nginx/sites-available/logininterested /etc/nginx/sites-enabled/logininterested
```

最后测试并重载：

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## 5. 验证访问

在你本地或任意能访问服务器的机器上：

```bash
curl -I http://<服务器IP>:8044/
```

浏览器访问：
- `http://<服务器IP>:8044/`

若你配置了域名解析，也可用：
- `http://<域名>:8044/`

---

## 6. 常见问题排查

### 6.1 访问不了端口（超时/拒绝）
- 确认阿里云安全组已放行 8044/TCP。
- 确认服务器防火墙（UFW/iptables）放行 8044。
- 确认 Nginx 正在监听 8044：

```bash
sudo ss -lntp | grep 8044
```

### 6.2 403 Forbidden
- 多数是目录权限/属主问题：

```bash
sudo chown -R www-data:www-data /opt/logininterested/www/frontend
sudo find /opt/logininterested/www/frontend -type d -exec chmod 755 {} \;
sudo find /opt/logininterested/www/frontend -type f -exec chmod 644 {} \;
sudo systemctl reload nginx
```

### 6.3 刷新子路由 404（SPA 常见）
- 必须保证 Nginx 配置里有：

```nginx
try_files $uri $uri/ /index.html;
```

---

## 7. 最小发布流程（建议）

每次更新：
1. 本地 `npm run build`
2. `rsync -avz --delete ./dist/ root@<服务器IP>:/opt/logininterested/www/frontend`
3. （一般不需要重启）如改过 Nginx 配置才 `sudo systemctl reload nginx`
