---
title: 使用 Cloudflare Tunnel 绕过备案，安全暴露本地服务
date: 2026-08-31 20:40:41
tags:
  - Cloudflare
  - 服务器
categories:
  - 服务器
---
![image](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/watermelon.jpg)



## 使用 Cloudflare Tunnel 绕过备案，安全暴露本地服务
适用场景：服务器在国内无法备案，无法使用 80/443 端口，需要通过域名安全访问内网服务。

📌 目录
1. [背景与原理](#cv1)

2. [安装 cloudflared](#cv2)

3. [认证与创建隧道](#cv3)

4. [配置隧道与启动](#cv4)

5. [PM2 进程管理（持久化运行）](#cv5)

6. [多域名多端口配置](#cv6)

7. [常见问题排查](#cv7)

8. [命令速查表](#cv8)

### <span id="cv1">背景与原理</span>
#### 为什么会有这个问题？
- 国内服务器未备案 → ISP 封禁 80/443 端口

- 直接用 IP:端口 访问可以，但域名访问被云厂商拦截（如京东云返回 403 备案拦截页面）

- 常规反向代理 + Cloudflare 灵活模式会报 525/1014 等错误

#### ***Cloudflare Tunnel*** 解决方案
原理：服务器主动向 Cloudflare 建立加密隧道，所有外部请求通过 Cloudflare 转发到本地服务。

优点：

- 不需要开放任何入站端口（防火墙全关都可以）

- 自动管理 SSL 证书（全自动 HTTPS）

- 完全绕过备案检测（流量走 Cloudflare，云厂商看不到域名）

- 一个隧道可以绑定无数个域名和端口

之前的链路：
```text
用户 → Cloudflare（灵活模式）→ 宝塔反向代理（监听某个端口）→ 你的服务（:2053）
```
这条链路非常脆弱，受制于备案、端口、证书、Cloudflare 回源等一系列问题
现在的新链路是：
```text
用户 → Cloudflare（自动HTTPS）→ Tunnel（加密通道）→ 你的服务（:2053）
```
这条新链路完全绕过了宝塔，直接由 cloudflared 把请求转发给本地服务。宝塔在这个过程中已经没有任何作用了，反向代理也可以删除。

### <span id="cv2">安装 cloudflared</span>
下载并安装

#### 下载 cloudflared（如果下载慢，可以换成镜像源）
```shell
wget -O cloudflared https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64
```
#### 添加执行权限
```shell
chmod +x cloudflared
```
#### 移动到系统路径
```shell
sudo mv cloudflared /usr/local/bin/
```
#### 验证安装
```
cloudflared --version
```
国内下载加速（备选）
如果 GitHub 下载太慢，可以查找国内镜像站下载

### <span id="cv3">认证与创建隧道</span>
#### 第一步：登录 Cloudflare 授权
```shell
cloudflared tunnel login
```
执行后会输出一个链接，在浏览器中打开，登录 Cloudflare 账号，选择对应的域名进行授权。

✅ 授权成功后，会在 ~/.cloudflared/cert.pem 生成证书文件。(将授权下载的文件移动到服务器指定文件夹内)

#### 第二步：创建命名隧道
```shell
cloudflared tunnel create xxx-tunnel
```
输出示例：

```text
Tunnel credentials written to /root/.cloudflared/xxxxx-xxxxx-xxxxx-xxxxx.json
Created tunnel xxx-tunnel with ID xxxxx-xxxxx-xxxxx-xxxxx
```
⚠️ 记下这个 Tunnel ID，后面配置文件中要用。

#### 第三步：验证隧道是否创建成功
```shell
cloudflared tunnel list
```
应该能看到刚创建的 xxx-tunnel。

### <span id="cv4">配置隧道与启动</span>
#### 第一步：创建配置文件
```shell
nano ~/.cloudflared/config.yml
```
填入以下内容（替换 xxxxx-xxxxx-xxxxx-xxxxx 为你的 Tunnel ID）：

```yaml
tunnel: xxxxx-xxxxx-xxxxx-xxxxx
credentials-file: /root/.cloudflared/xxxxx-xxxxx-xxxxx-xxxxx.json

ingress:
- hostname: xxx.yyy
  service: http://localhost:端口号
- service: http_status:404
```
ingress 规则按顺序匹配，越靠前优先级越高。最后一条 http_status:404 是兜底规则。
#### 第二步：添加 DNS 路由
```shell
cloudflared tunnel route dns xxx-tunnel xxx.yyy

```
这条命令会自动在 Cloudflare DNS 中添加 CNAME 记录。

⚠️ 注意：如果是在添加全新的主域名（如 abc.cn），不要用此命令，应手动在 Cloudflare 后台添加 CNAME 记录，指向 你的隧道UUID.cf-tunnel.xyz。

#### 第三步：删除旧的 A 记录
在 Cloudflare 后台 DNS → 记录 中，删除所有指向服务器 IP 的 A 记录，只保留 CNAME 记录指向隧道。

如果不删除 A 记录，DNS 解析会冲突，导致访问不稳定。

#### 第四步：前台测试运行
```shell
cloudflared tunnel run xxx-tunnel
```
如果看到 Registered tunnel connection 日志，说明配置正确。按 Ctrl+C 停止。

### <span id="cv5">PM2 进程管理（持久化运行）</span>
#### 启动隧道
```shell
pm2 start cloudflared --name "cloudflare-tunnel" -- tunnel run xxx-tunnel
```
#### 常用管理命令
```shell
# 查看进程状态
pm2 list
# 查看日志
pm2 logs cloudflare-tunnel
# 查看最近 20 行日志
pm2 logs cloudflare-tunnel --lines 20
# 重启隧道
pm2 restart cloudflare-tunnel
# 停止隧道
pm2 stop cloudflare-tunnel
# 删除进程
pm2 delete cloudflare-tunnel
```

#### 设置开机自启（可选）
```shell
pm2 save
pm2 startup
```


### <span id="cv6">多域名多端口配置</span>
场景：一个隧道绑定多个域名和端口
示例：

- xxx.yyy → 本地 2053 端口
- abc.cn → 本地 9999 端口

#### 第一步：添加 DNS 路由
##### 子域名（如 app.xxx.yyy）：
```shell
cloudflared tunnel route dns xxx-tunnel app.xxx.yyy

```
##### 新主域名（如 abc.cn）：
手动在 Cloudflare 后台添加 CNAME 记录：

- 类型：CNAME

- 名称：abc.cn（或 @）

- 目标：你的隧道UUID.cf-tunnel.xyz

⚠️ 记得删除该域名下所有指向 IP 的 A 记录，只保留 CNAME。

#### 第二步：修改配置文件
```shell
nano ~/.cloudflared/config.yml
```

```yaml
tunnel: xxxxx-xxxxx-xxxxx-xxxxx
credentials-file: /root/.cloudflared/xxxxx-xxxxx-xxxxx-xxxxx.json

ingress:
- hostname: abc.cn
  service: http://localhost:9999
- hostname: app.xxx.yyy
  service: http://localhost:3000
- hostname: xxx.yyy
  service: http://localhost:2053
- service: http_status:404
```
规则按顺序匹配，把最常用的或最精确的域名放在最前面。
#### 第三步：重启生效
```shell
pm2 restart cloudflare-tunnel
```
### <span id="cv7">常见问题排查</span>
| 错误现象 | 可能原因 | 解决方法 |
| :--- | :--- | :--- |
| `ERR_SSL_PROTOCOL_ERROR` | 用 HTTPS 访问了 HTTP 服务 | 确认 `service` 用的是 `http://` 不是 `https://` |
| `Error 525` | SSL 握手失败 | 改用 Tunnel，放弃反向代理方案 |
| `Error 1014 CNAME Cross-User Banned` | CNAME 指向跨账户域名 | 改用命名隧道，不要用 `trycloudflare.com` |
| `403 Forbidden` 京东云拦截页面 | DNS 还是 A 记录指向服务器 IP | 删除 A 记录，只保留 CNAME 指向隧道 |
| `cloudflared tunnel create` 报错找不到 cert.pem | 未完成登录授权 | 执行 `cloudflared tunnel login` 并完成浏览器授权 |
| 新主域名被添加成子域名 | 命令自动加到了默认主域名下 | 手动在 Cloudflare 后台添加 CNAME 记录 |
| 网页打开慢 | 流量经过 Cloudflare 边缘节点 | 正常现象，可考虑升级 Cloudflare 套餐 |
### <span id="cv8">命令速查表</span>
#### 隧道管理
```shell
# 登录授权
cloudflared tunnel login
# 创建命名隧道
cloudflared tunnel create <隧道名>
# 查看隧道列表
cloudflared tunnel list

# 查看隧道详情
cloudflared tunnel info <隧道名>

# 删除隧道
cloudflared tunnel delete <隧道名>

# 添加 DNS 路由（子域名用）
cloudflared tunnel route dns <隧道名> <域名>

# 前台运行测试
cloudflared tunnel run <隧道名>
```

#### PM2 管理
```shell
# 启动
pm2 start cloudflared --name "cloudflare-tunnel" -- tunnel run <隧道名>

# 查看状态
pm2 list

# 查看日志
pm2 logs cloudflare-tunnel

# 重启
pm2 restart cloudflare-tunnel

# 停止
pm2 stop cloudflare-tunnel

# 删除进程
pm2 delete cloudflare-tunnel
```

#### DNS 验证
```shell
# 检查域名解析
dig <你的域名>

# 查看 cloudflared 日志
pm2 logs cloudflare-tunnel --lines 20
```

### 最后的建议
1. 优先使用命名隧道（Named Tunnel），不要用临时隧道（trycloudflare.com），生产环境更稳定。

2. 每次添加新域名，记得先登录授权（如果是新主域名），然后修改 config.yml，最后重启 PM2。

3. DNS 记录只保留 CNAME，删除所有 A 记录，避免解析冲突。

4. 配置文件 config.yml 的 ingress 规则按优先级排序，精确匹配的放前面，兜底规则放最后。

✅ Cloudflare Tunnel → 完美绕过所有限制 🎉
📌 基于实际操作经验整理，适用于 Ubuntu/Debian 系统
