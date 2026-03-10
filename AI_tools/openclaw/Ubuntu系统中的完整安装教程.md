OpenClaw 是一款轻量高效的本地个人 AI 助手框架，可实现自动执行任务、调用工具、网页浏览、流程组合等功能，适配 Ubuntu 20.04 LTS 及以上所有主流版本（含 Server 版和 Desktop 版）。

## 一、前置准备（必做，避免后续安装失败）

### 1.1 系统与硬件要求

确保你的 Ubuntu 系统满足以下最低要求，推荐配置可提升运行流畅度，适配所有安装方案：

| 组件     | 最低要求                     | 推荐配置                             |
| -------- | ---------------------------- | ------------------------------------ |
| 操作系统 | Ubuntu 20.04 LTS 及以上      | Ubuntu 22.04/24.04 LTS（稳定性最优） |
| CPU      | 2 核及以上                   | 4 核及以上                           |
| 内存     | 4GB RAM                      | 8GB RAM（避免运行卡顿）              |
| 存储     | 2GB 可用空间                 | 10GB 可用空间（预留模型与插件存储）  |
| 其他     | 稳定网络、管理员权限（sudo） | kx上网（可选，加速部分依赖下载）     |

### 1.2 系统环境预处理

无论选择哪种安装方式，先执行以下命令更新系统、安装基础依赖，解决大部分“依赖缺失”报错：

```
# 更新系统软件包列表（必做）
sudo apt update && sudo apt upgrade -y
 
# 安装基础依赖（编译工具、网络工具等，避免卡在 node-gyp rebuild）
sudo apt install -y curl wget git python3 build-essential libssl-dev ufw
```

### 1.3 安装 Node.js（核心依赖，必做）

OpenClaw 要求 Node.js 版本 ≥ 22.0.0，推荐使用 NodeSource 源安装（稳定且可快速升级），避免使用 Ubuntu 官方源的低版本 Node.js：

```markdown
# 导入 NodeSource 22.x 源
sudo curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
 
# 安装 Node.js 和 npm（npm 用于包管理）
sudo apt install -y nodejs
 
# 验证安装是否成功（显示版本号即正常）
node --version  # 需显示 v22.x.x
npm --version   # 需显示 10.x.x 及以上
```

### 1.4 国内环境优化（可选，解决下载卡顿）

国内用户直接下载 OpenClaw 依赖可能卡顿，建议配置 npm 国内镜像（阿里云源），加速下载速度：

```
# 配置 npm 国内镜像
npm config set registry https://registry.npmmirror.com
 
# 验证镜像配置是否生效
npm config get registry  # 显示 https://registry.npmmirror.com 即成功
```

## 二、三种安装方式（按需选择，新手首选一键安装）

本文提供 3 种安装方案，覆盖不同用户需求：一键安装（新手首选，最简单）、npm 全局安装（灵活可控）、Docker 部署（适合开发者，隔离环境），任选其一即可，安装完成后功能完全一致。

### 方案一：一键安装（官方推荐，新手首选）

官方提供一键安装脚本，自动完成所有配置，无需手动操作，适合首次接触 OpenClaw 的新手：

1. 执行一键安装命令，脚本会自动下载、安装 OpenClaw 及所有依赖

    `curl -fsSL https://gitee.com/zhuiai/openclaw/blob/main/scripts/install.sh | bash` 

    资源不可用就再去搜一下，只要是同步最新的github的就行

3. 安装过程说明：

4. 安装过程中会提示确认配置（如模型选择、Channel 配置），默认按 Enter 键即可，后续可在 UI 界面修改；

5. 若出现“权限不足”报错，在命令前添加 sudo（sudo curl -fsSL ... | bash）；

6. 安装耗时约 3-10 分钟，取决于网络速度，耐心等待，不要中途中断命令。

7. 验证安装成功：安装完成后，终端会显示“OpenClaw installed successfully”，并给出 Control UI 登录地址（默认 http://127.0.0.1:18789/openclaw），即表示安装成功。

### 方案二：npm 全局安装（灵活可控，适合进阶用户）

通过 npm 全局安装，可手动控制版本、灵活配置参数，适合需要自定义安装路径或版本的用户：

1. 执行 npm 安装命令，全局安装最新版 OpenClaw： `sudo npm install -g openclaw@latest`
2. 解决下载卡顿问题：
3. 若命令执行后卡住（尤其是下载预编译二进制文件时），可中断命令（Ctrl+C），重新执行；
4. 若仍卡顿，可使用浏览器下载离线安装包（参考国内镜像站），再手动安装。
5. 验证安装成功：执行以下命令，显示版本号即正常： `openclaw --version`

### 方案三：Docker 部署（适合开发者，环境隔离）

若你的系统已安装 Docker，可使用容器化部署，避免影响本地系统环境，适合开发、测试场景：

1. 确保 Docker 已安装（未安装可执行以下命令快速安装）： `# 安装 Docker``curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun``# 启动 Docker 并设置开机自启``sudo systemctl enable --now docker``# 验证 Docker 安装成功``sudo docker --version`

2. 克隆 OpenClaw 仓库并启动容器（若执行git clone提示“没有文件”“仓库无法访问”，原因是GitHub海外仓库国内访问受限，替换为国内Gitee镜像仓库，解决文件缺失）： 

   `# 克隆 OpenClaw 国内Gitee镜像仓库（避免海外仓库文件无法获取）`

   `git clone https://gitee.com/openclaw-mirror/openclaw.git`

   `# 进入仓库目录``cd openclaw``# 启动 OpenClaw Gateway 容器（后台运行）``sudo docker compose up -d openclaw-gateway`

3. 验证容器启动成功：执行 sudo docker ps，若看到“openclaw-gateway”容器状态为“Up”，即表示部署成功。若启动后容器频繁重启，可执行 `sudo docker logs openclaw-gateway` 查看错误日志，多数原因是配置文件错误或端口占用。

## 三、初始化配置（安装完成后必做）

安装完成后，需完成简单初始化配置（模型选择、Channel 配置、端口放行），才能正常使用 OpenClaw 的所有功能，以下步骤适配所有安装方式。

### 3.1 端口放行（必做，解决无法访问 UI 问题）

OpenClaw Gateway 默认监听 18789 端口，Ubuntu 防火墙（ufw）默认会拦截该端口，需手动放行：

```
# 放行 18789 端口（TCP 协议）
sudo ufw allow 18789/tcp
# 重启防火墙，使配置生效
sudo ufw reload
# 验证端口是否放行成功
sudo ufw status | grep 18789  # 显示“ALLOW”即成功
```

### 3.2 启动 Gateway 服务

Gateway 是 OpenClaw 的核心服务，负责接收请求、调用工具，需确保其正常启动，分两种场景操作：

#### 场景 1：一键安装/ npm 安装用户

```
# 方式 1：前台运行（适合测试，关闭终端即停止）
openclaw gateway --port 18789 --verbose

#错误信息：Gateway start blocked: set gateway.mode=local (current: unset) or pass --allow-unconfigured.
#修改配置文件: /home/xxx/.openclaw/openclaw.json
gateway:
  mode: local
#或者 openclaw gateway start --allow-unconfigured
 
# 方式 2：后台运行（推荐，持久化启动）
# 安装 PM2 进程管理器（用于后台守护）
sudo npm install -g pm2
# 注意：若已执行过此命令，不要重复执行，避免启动多个openclaw进程（导致端口冲突、UI无法访问）
pm2 start "openclaw gateway" --name openclaw  # 仅启动1个进程
pm2 startup
pm2 save
 
# 验证服务状态（显示“online”且只有1个openclaw进程即正常）
pm2 status openclaw
# 若出现多个openclaw进程，执行以下命令清理（补充）
# pm2 stop openclaw && pm2 delete openclaw && pm2 start "openclaw gateway" --name openclaw
# 若服务一直重启，执行 pm2 logs openclaw 查看错误日志，定位重启原因

#方式 3：onboard （推荐，持久化启动，问题比较少）
openclaw onboard  进入配置页面， 做简单的配置，很多的项可以先跳过
只需要执行一次，不要再跑 openclaw onboard，除非你想重置所有配置。如果确实需要重跑onboard，之后记得执行：
  openclaw config set agents.defaults.model.primary anthropic/claude-sonnet-4-6
  openclaw gateway restart

openclaw gateway status
openclaw gateway start #启动网关服务

openclaw dashboard #启动web页面
```

#### 场景 2：Docker 部署用户

Docker 容器启动后会自动运行 Gateway 服务，无需手动启动，若需重启服务：

```
sudo docker compose restart openclaw-gateway
# 若容器一直重启，查看错误日志（核心排查步骤）
sudo docker logs openclaw-gateway
```



### 3.3 配置 Control UI 可远程访问（可选）

默认情况下，Control UI 仅允许本地（127.0.0.1）访问，若你的 Ubuntu 是 Server 版（无桌面），或想从其他设备访问，需修改配置文件： /home/username/.openclaw/openclaw.json

1. 找到“gateway”配置项，修改为以下内容（**自定义 token** 和端口可按需调整）：

   ```json
   {
       "auth": {
           "profiles": {
             "anthropic:default": {
               "provider": "anthropic",
               "mode": "api_key"  //key不可见
             }
           }
       },
       "agents": {
           "defaults": {
               "model": {
                   "primary": "anthropic/claude-sonnet-4-6"
               },
               "models": {
                   "anthropic/claude-sonnet-4-6": {
                     "alias": "sonnet",
                     "params": {
                       "cacheRetention": "short"
                     }
                   }
               },
               "workspace": "/whb/openclaw/.openclaw/workspace"
           }
       },
       "meta": {
           "lastTouchedVersion": "2026.3.7",
           "lastTouchedAt": "2026-03-8T13:06:24.900Z"
       },
       "gateway": {
           "port": 18789,
           "mode": "local",
           "bind": "lan",
           "controlUi": {
               "enabled": true,
               "basePath": "/openclaw",
               "allowInsecureAuth": true,
               "dangerouslyDisableDeviceAuth": true
           },
           "auth": {
               "mode": "token",
               "token": "xxxxx" 
           },
           "tailscale": {
               "mode": "off",
               "resetOnExit": false
           },
           "nodes": {
               "allowCommands": [
                   "ls",
                   "pwd",
                   "cat"
               ]
           }
       }
   }
   ```

   

2. 保存配置并重启 Gateway 服务：

   ```
   # 保存配置（nano 编辑器：Ctrl+O 保存，Ctrl+X 退出）
   # 重启服务（npm 安装用户）
   pm2 restart openclaw
   
   # 重启服务（Docker 用户）
   sudo docker compose restart openclaw-gateway
   ```

3. 编辑 OpenClaw 配置文件（路径固定，若执行命令提示“没有文件”，核心原因是 OpenClaw 首次安装后未生成配置文件，需先执行配置向导生成，步骤如下）： 

   第一步：先执行配置向导，生成 /root/.openclaw 目录及 openclaw.json 配置文件（必做）： `openclaw setup` 执行后按终端提示完成基础配置（如模型选择、Channel 初始化），配置完成后会自动生成配置文件，此步骤是解决文件缺失的核心； 

   

   第二步：再执行编辑命令，此时文件已存在，可正常编辑： `sudo nano /root/.openclaw/openclaw.json` 补充：若执行 openclaw setup 后仍提示无文件，可执行 openclaw configure 重新初始化配置，或手动创建目录后生成文件（命令如下）： `# 手动创建配置文件目录``sudo mkdir -p /root/.openclaw``# 重新生成默认配置文件``openclaw config init``# 再次执行编辑命令``sudo nano /root/.openclaw/openclaw.json`

### 3.4 初始化向导配置

访问 Control UI 后，需完成简单向导配置，才能正常使用 AI 功能：

1. 访问 Control UI：

2. 本地访问（Ubuntu 有桌面）：打开浏览器，输入  http://192.168.16.128:18789/openclaw/#token=xxxxx ；

3. 远程访问（其他设备）：输入 http://Ubuntu 服务器 IP:18789/openclaw（服务器 IP 可通过 ip addr show eth0 查看，必须带完整路径）。

4. 完成向导配置：

5. 模型配置：选择适合自己的 AI 模型（按需选择，后续可修改）；

   OpenClaw 需要连接到大语言模型才能工作。Openclaw 比较费token，国外模型成本高，门槛也高，这里我选择国内的智谱的 GLM 4.7

   > 如果没有智谱的API Key，点击官方地址自己注册账号获取API key：[https://www.bigmodel.cn/glm-coding?ic=RBSKXMPNJP](https://cloud.tencent.com/developer/tools/blog-entry?target=https%3A%2F%2Fwww.bigmodel.cn%2Fglm-coding%3Fic%3DRBSKXMPNJP&objectId=2626160&objectType=1&contentType=undefined)

6. Channel 配置：Channel 是 OpenClaw 的交互接口，可配置常用聊天工具，实现手机/电脑远程控制；

7. Skill 配置：安装所需的技能插件（如文件处理、网页浏览等），按需勾选即可。

8. 登录验证：若配置了 token，在登录界面输入自定义 token，即可进入 Control UI 主界面，完成初始化配置。

## 四、启动与基础使用（快速上手）

### 4.1 启动 OpenClaw

- npm 安装 + PM2 守护：无需手动启动，系统重启后会自动运行，可通过 pm2 status openclaw 查看状态；**重点提醒：不要重复执行 pm2 start 命令，否则会启动多个openclaw进程，导致端口冲突、UI无法访问**；若出现多个进程，执行 `pm2 stop openclaw && pm2 delete openclaw && pm2 start "openclaw gateway" --name openclaw` 清理重启；若服务一直重启，优先执行`pm2 logs openclaw` 查看错误日志，定位重启原因；
- 一键安装用户：默认已配置开机自启，若未启动，执行 openclaw gateway start；若一直重启，执行 `openclaw gateway --port 18789 --verbose` 前台运行，查看实时报错；
- Docker 用户：执行 sudo docker compose up -d openclaw-gateway 启动，sudo docker compose stop 停止；若容器一直重启，执行 `sudo docker logs openclaw-gateway` 查看错误日志，多数为配置错误或端口占用导致。

### 4.2 基础使用操作

1. 进入 Control UI 主界面，点击“New Chat”创建新对话；
2. 输入指令测试（如“你好，你是？”“检查 Channel 配置是否正确”），OpenClaw 会自动执行并反馈结果；
3. 配置 Channel 后，可通过常用聊天工具远程发送指令，控制 OpenClaw 执行任务（如文件处理、网页摘要等）；
4. 查看日志：执行 openclaw gateway --verbose 可查看实时运行日志，排查使用中的问题（尤其适合排查服务重启问题）。

## 五、常见问题排查（避坑重点，解决所有高频报错）

### 1. 安装时卡住，提示“node-gyp rebuild”失败

原因：缺少 Python 和 C++ 编译工具，或 Node.js 版本过低。

解决方案：重新安装基础依赖，确保 Node.js 版本 ≥22.0.0： `sudo apt install -y python3 build-essential ``# 重新安装 Node.js（确保版本正确） ``curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash - ``sudo apt install -y nodejs`

### 2. 启动 Gateway 后，无法访问 18789 端口

原因：防火墙未放行端口，或 Gateway 未正常启动，或配置文件中 bind 未设为 lan。

解决方案： 

`# 1. 放行端口 ``sudo ufw allow 18789/tcp ``sudo ufw reload `
`# 2. 检查 Gateway 状态 `
`pm2 status openclaw # npm 安装用户，显示online仅代表服务启动，不代表UI正常监听；若出现两个openclaw进程，会导致端口冲突、UI无法访问 `
`sudo docker ps # Docker 用户 `
`# 3. 若未启动，重启服务；若出现两个openclaw进程，先清理冗余进程（核心操作） `
`pm2 stop openclaw # 停止所有名为openclaw的进程 `
`pm2 delete openclaw # 删除所有名为openclaw的进程（彻底清理冗余） `
`pm2 start "openclaw gateway" --name openclaw # 仅启动1个进程 `
`# 4. 确认配置文件中 bind 为 lan（参考 3.3 节）；若提示无配置文件，先执行 openclaw setup 生成文件 `
`openclaw setup # 生成 /root/.openclaw/openclaw.json 配置文件 `
`sudo nano /root/.openclaw/openclaw.json # 再编辑配置`

补充：重点解决「pm2 status 出现两个openclaw进程+UI访问不到」联动问题（高频场景），按以下步骤排查，先解决进程冲突，再排查UI问题： 

1. 进程冲突原因：多次执行 pm2 start 命令，导致重复启动openclaw进程，抢占18789端口，进而导致UI无法加载（端口被占用，服务无法正常监听）；
2.  彻底清理冗余进程（必做）：执行上述 pm2 stop + pm2 delete 命令后，再执行 pm2 status 确认只有1个openclaw进程（状态为online）；若仍有多个，执行 `pm2 kill` 终止所有pm2管理的进程，再重新启动openclaw； 
3.  端口冲突排查：清理进程后，执行 `sudo lsof -i:18789`，确保只有1个node进程（对应openclaw）监听18789端口；若仍有其他进程占用，执行 `sudo kill -9 进程ID` 释放端口，再重启openclaw； 
4. UI访问补充排查：清理进程、重启服务后，必须输入完整地址 http://127.0.0.1:18789/openclaw；若仍无法访问，确认配置中 `"controlUi": {"enabled": true, "basePath": "/openclaw"}`，执行 `npm install -g openclaw@latest --force` 修复UI资源，重启服务即可。

### 3. 访问 Control UI 报错，提示“Missing config”

原因：未完成初始化配置，或配置文件损坏。

解决方案：执行配置向导，重新生成配置文件（同时解决“openclaw.json 没有文件”的问题）：`openclaw setup # 启动配置向导，按提示完成配置，自动生成/root/.openclaw/openclaw.json ``openclaw config set gateway.mode local # 设置模式为本地 ``pm2 restart openclaw # 重启服务` 补充：若执行 openclaw setup 无反应，可替换为 openclaw configure 命令（功能一致，均能生成配置文件）；若仍无法生成，可手动创建目录后重新执行向导，命令如下： `sudo mkdir -p /root/.openclaw ``openclaw config init ``openclaw setup`

### 4. 下载依赖卡顿，无法完成安装

原因：网络问题，或未配置国内镜像。

解决方案：配置 npm 国内镜像，或使用kx上网，重新执行安装命令（若仍提示“没有文件”，替换为国内镜像命令）： `npm config set registry https://registry.npmmirror.com ``# 重新安装（npm 安装用户，若提示文件缺失，执行此命令） ``npm install -g openclaw@latest --registry=https://registry.npmmirror.com ``# 重新安装（一键安装用户，彻底解决文件缺失，替换原命令） ``curl -fsSL https://gitee.com/openclaw-mirror/install-script/raw/main/install.sh | bash`

### 5. Docker 部署后，容器无法启动

原因：Docker 版本过低，或端口被占用，或配置文件错误。

解决方案：升级 Docker，释放 18789 端口，排查配置文件错误： `# 升级 Docker ``curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun ``# 查看 18789 端口占用情况，杀死占用进程 ``sudo lsof -i:18789 ``sudo kill -9 进程ID ``# 查看容器错误日志，排查配置问题 ``sudo docker logs openclaw-gateway ``# 重启容器 ``sudo docker compose up -d openclaw-gateway` 补充：若日志提示配置错误，可删除原有配置文件，重新执行初始化配置后重启容器。

### 6. OpenClaw 一直重启（高频新增问题）

核心原因：分两种安装场景，共4类高频原因（优先级：日志报错 > 配置错误 > 端口/进程冲突 > 依赖/版本问题），**先查看错误日志，再针对性解决**，避免盲目操作。

#### 场景 1：npm 安装/一键安装用户（PM2 管理）

1. 第一步：查看错误日志（核心，必做），定位重启根源： 

   `# 查看 PM2 管理的 openclaw 错误日志（实时刷新，按 Ctrl+C 退出） ``pm2 logs openclaw ``# 若 PM2 异常，用前台启动查看更详细报错（直接显示启动失败原因） ``

   `pm2 stop openclaw && pm2 delete openclaw`  

   `openclaw gateway --port 18789 --verbose` 

   常见日志报错对应原因：

   1. “port in use”：18789 端口被占用（其他进程或重复的 openclaw 进程）；
   2. “invalid config”/“config not found”：配置文件错误（修改后未保存、格式错误）或未生成；
   3. “missing dependency”：依赖缺失（安装不完整，或 Node.js 版本不匹配）；
   4. “out of memory”：内存不足（低于 4GB 最低要求，优先扩容或关闭其他占用内存的进程）。

2. 第二步：针对性解决（按日志报错选择，无需全部执行）：
   1. 解决端口/进程冲突（最高频）： `# 1. 查看 18789 端口占用，杀死占用进程``sudo lsof -i:18789``sudo kill -9 进程ID # 替换为占用端口的进程ID``# 2. 彻底清理 openclaw 冗余进程``pm2 kill # 终止所有 PM2 管理的进程``pm2 start "openclaw gateway" --name openclaw # 重新启动单个进程``pm2 save`
   2. 解决配置文件错误： `# 删除损坏的配置文件，重新生成``sudo rm -rf /root/.openclaw/openclaw.json``openclaw setup # 重新执行配置向导，生成正确配置``pm2 restart openclaw`
   3. 解决依赖/版本问题： `# 重新安装完整依赖，确保 Node.js 版本 ≥22.0.0``npm install -g openclaw@latest --force # 强制重新安装，修复缺失依赖``node --version # 验证版本，若低于 22.x.x，重新安装 Node.js（参考 1.3 节）``pm2 restart openclaw`
   4. 解决内存不足： `# 查看内存占用，关闭无用进程（如大型软件、其他后台服务）``free -h``sudo kill -9 占用内存高的进程ID # 谨慎操作，避免杀死系统关键进程``# 重新启动 openclaw``pm2 restart openclaw`

3. 第三步：验证是否恢复正常： `pm2 status openclaw # 显示“online”且不再重启即为正常``curl http://127.0.0.1:18789/openclaw # 测试完整路径端口是否正常响应`

#### 场景 2：Docker 部署用户（容器重启）

1. 第一步：查看容器错误日志（核心，必做），参考 Docker 部署日志排查规范： `# 查看 openclaw-gateway 容器错误日志（显示启动失败/重启原因） ``sudo docker logs openclaw-gateway ``# 查看容器重启记录，确认重启频率 ``sudo docker inspect openclaw-gateway | grep RestartCount` 常见日志报错对应原因：
   1. “port is already allocated”：18789 端口被宿主机其他进程占用；
   2. “config file error”：挂载的配置文件格式错误，或未正确映射；
   3. “image pull failed”：Docker 镜像拉取不完整（国内网络问题）；
   4. “container exit code 1”：容器启动脚本异常，需重新部署。
2. 第二步：针对性解决（按日志报错选择）：
   1. 解决端口占用/容器冲突： `# 1. 查看端口占用，杀死占用进程``sudo lsof -i:18789``sudo kill -9 进程ID``# 2. 停止并删除异常容器，重新启动``sudo docker compose stop openclaw-gateway``sudo docker compose rm openclaw-gateway``sudo docker compose up -d openclaw-gateway`
   2. 解决配置文件错误（参考 Docker 部署规范）： `# 进入容器配置目录，删除错误配置``sudo docker exec -it openclaw-gateway /bin/bash``rm -rf /root/.openclaw/openclaw.json``exit``# 重新执行初始化配置，重启容器``openclaw setup``sudo docker compose restart openclaw-gateway`
   3. 解决镜像/部署问题： `# 重新拉取国内镜像，重新部署``sudo docker compose down``git pull https://gitee.com/openclaw-mirror/openclaw.git # 更新仓库配置``sudo docker compose up -d openclaw-gateway`
3. 第三步：验证容器是否正常运行： `sudo docker ps | grep openclaw-gateway # 状态显示“Up”且无频繁重启即为正常`

补充：若以上步骤仍无法解决，执行“彻底重置”操作（备份配置后），重新安装初始化： `# npm/一键安装用户重置 ``pm2 kill && npm uninstall -g openclaw ``sudo rm -rf /root/.openclaw ``npm install -g openclaw@latest && openclaw setup && pm2 start "openclaw gateway" --name openclaw ``# Docker 用户重置 ``sudo docker compose down && sudo docker rmi openclaw-gateway ``sudo rm -rf openclaw # 删除仓库目录 ``git clone https://gitee.com/openclaw-mirror/openclaw.git && cd openclaw && sudo docker compose up -d openclaw-gateway`

### 常用命令汇总

```bash
openclaw models status
openclaw models list --provider anthropic
#设置模型
openclaw models set anthropic/claude-opus-4-6

# 查看 gateway 运行状态
openclaw gateway status
# 重启 gateway（修改配置后需执行）
openclaw gateway restart
# 查看实时日志
openclaw logs --follow
openclaw channels add

openclaw dashboard #自动打开web ui

# 查看服务日志（systemd）
journalctl --user -u openclaw-gateway.service -f

openclaw gateway install
openclaw gateway
systemctl --user start openclaw-gateway.service
```

### Skill依赖安装

```
#安装brew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"

#安装uv
curl -LsSf https://astral.sh/uv/install.sh | sh
source $HOME/.local/bin/env

#安装nano-pdf
uv tool install nano-pdf

#summarize
brew install pipx
pipx install sumy
```



参考:https://www.cnblogs.com/databank/p/19610007