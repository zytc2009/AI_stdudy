## **一、部署前必做：环境与资料准备**

工欲善其事必先利其器，提前准备好软硬件环境和关键密钥，能让部署过程事半功倍，全程无需专业开发知识，跟着步骤来即可。

- **系统与基础环境**：Windows 10/11 64 位系统，提前安装 Node.js 22 + 版本（不懂安装可直接让 AI 工具协助，一键完成）；推荐使用 PowerShell 操作，后续命令均在该终端执行。

  ```
  #安装uv
  powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
  uv --version
  ```

  

- **GLM 模型 API Key**：国内部署首选 GLM 模型，无需翻墙。打开智谱 AI 开放平台，注册后点击「API Key」-「添加新的 API Key」，命名后保存密钥，后续配置会频繁用到。

- **飞书机器人基础信息**：打开飞书开放平台，用有企业管理员权限的账号登录，创建「企业自建应用」，填写名称和图标后，在「凭证与基础信息」中记录App ID和App Secret（务必保密，切勿泄露）。

  

## **二、核心步骤：OpenClaw 安装 + GLM 模型对接**

这一步是部署的核心，一行命令完成 OpenClaw 安装，再通过向导模式快速对接 GLM 模型，全程可视化操作，零基础也能跟上。

**1.一键安装 OpenClaw**：以管理员身份打开 PowerShell，执行以下命令，等待几分钟完成安装（若遇网络卡顿，可稍作等待重试）：

```
iwr -useb https://clawd.org.cn/install.ps1 | iex
```

**2.启动配置向导**：安装完成后，在 CMD 中输入命令启动向导，后续按提示逐步配置：

```
openclaw-cn onboard
```

**3.对接 GLM 模型**

- 向导中选择「QuickStart」快速模式，回车继续；
- 模型选择环节找到「Z.AI」（对应 GLM 模型），区域选择「CN」；
- 选择「Paste API key now」，粘贴之前在智谱 AI 获取的 GLM API Key；
- 默认模型选择「glm-5」，回车确认，完成 GLM 模型与 OpenClaw 的绑定。

**4.验证安装成功**：向导完成后，OpenClaw 会自动安装 Gateway 网关并打开 WEB UI 页面，访问地址为http://127.0.0.1:18789/，在对话框输入「你使用的模型是什么」，若收到模型回复，说明基础部署 + GLM 对接成功！

## **三、关键拓展：OpenClaw Skills 两种安装方法**

Skills 是 OpenClaw 的核心能力模块，装上后才能解锁文件处理、网页总结、自动化操作等生产力功能，支持**提示词指令安装**和**手动安装**两种方式，适配不同网络环境。

### **方式 1：提示词指令安装（高效进阶，网络通畅首选）**

无需复杂操作，直接向已部署的 OpenClaw 发送对话指令，系统自动完成下载、验证、安装，高危技能会触发二次确认，示例命令：帮我用Clawhub装一个skill，名称是mcd

注：技能名需前往ClawHub 技能库查询确认，确保名称准确。

### **方式 2：手动安装（应对网络限频 / SSH 访问失败）**

适合网络受限、无法通过指令自动拉取技能的场景，步骤清晰，手动上传即可：

1. 前往 ClawHub 技能库，找到需要的技能（如 summarize 网页总结），点击「Download zip」下载压缩包；
2. 解压压缩包至 OpenClaw 指定技能目录，Windows 路径为：`C:\Users\你的用户名\.openclaw\workspace\skills\`；
3. 重启 OpenClaw 网关，在 PowerShell 执行命令：`openclaw gateway restart`；
4. 向 OpenClaw 发送「更新技能列表」指令，验证技能是否安装成功。



## **四、实战联动：手把手连接飞书机器人**

完成基础部署后，将 OpenClaw 与飞书机器人绑定，就能通过飞书聊天直接指挥 OpenClaw 执行操作，实现办公自动化，配置过程含权限设置和参数填写，一步都不丢。

**飞书机器人权限配置**：回到飞书开放平台的应用详情页，点击「权限管理」-「批量导入 / 导出权限」，粘贴以下权限代码，依次点击「下一步 - 确认新增权限 - 申请开通 - 确认」，完成全权限配置：

```
{ "scopes": { "tenant": ( "aily:file:read", "aily:file:write", "application:application.app_message_stats.overview:readonly", "application:application:self_manage", "application:bot.menu:write", "contact:contact.base:readonly", "contact:user.employee_id:readonly", "corehr:file:download", "event:ip_list", "im:chat.access_event.bot_p2p_chat:read", "im:chat.members:bot_access", "im:message", "im:message.group_at_msg:readonly", "im:message.p2p_msg:readonly", "im:message:readonly", "im:message:send_as_bot", "im:resource" ), "user": ( "aily:file:read", "aily:file:write", "im:chat.access_event.bot_p2p_chat:read" ) } }
```

**飞书插件安装与配置**：在 OpenClaw 配置向导的「频道选择」环节，选择「Feishu/Lark (飞书)」，回车后选择「Download from npm (@openclaw/feishu)」安装飞书插件；依次输入之前记录的飞书 App ID 和 App Secret，区域选择「Feishu (feishu.cn) - China」。

**发布飞书应用**：飞书开放平台中，点击「应用能力」-「机器人」，设置机器人名称（如 OpenClaw）并保存；点击「创建版本」，填写版本号和更新说明，发布后应用状态变为「已发布」。

**开启事件订阅**：回到飞书开放平台「事件与回调」，配置订阅方式，确保飞书能接收到 OpenClaw 的指令反馈，至此飞书机器人与 OpenClaw 完成绑定，可在飞书聊天中直接发送指令测试。

## **五、全网高频报错解决：避坑指南，踩过的坑全帮你填**

部署过程中，小伙伴最常遇到 SSH 连接失败、权限不足、GitHub 访问不稳定等问题，以下是针对性解决方法，实测有效！

### **问题 1：SSH 认证失败，提示「Permission denied (publickey)」**

这是最常见的问题，原因是本地 Git 缺少有效 SSH 密钥，无法访问 GitHub 拉取依赖，按以下步骤解决：

- **检查 SSH 密钥是否存在：**执行`ls -al ~/.ssh`，若无 id_rsa（私钥）和 id_rsa.pub（公钥），生成新密钥：`ssh-keygen -t rsa -b 4096 -C "你的邮箱"`；

- **添加密钥到 SSH 代理：**`eval "$(ssh-agent -s)"`、`ssh-add ~/.ssh/id_rsa`；

- **将公钥添加到 GitHub：**复制公钥内容`pbcopy < ~/.ssh/id_rsa.pub`，登录 GitHub 后在「Settings-SSH and GPG keys」中粘贴添加；

- **测试连接**：`ssh -T git@github.com`，出现「Hi username! You've successfully authenticated」即为成功。

  

### **问题 2：GitHub 访问不稳定，命令执行失败**

Windows 部署时易遇网络问题导致无法拉取 GitHub 资源，强行切换 HTTP 协议即可解决：

1. 全局替换 SSH 链接为 HTTPS：``

   ```
   git config --global url."https://github.com/".insteadOf "git@github.com:"
   git config --global url."https://github.com/".insteadOf "ssh://git@github.com/"
   ```

2. 禁用 SSL 验证避免证书错误：`git config --global http.sslverify "false"`

3. 清理缓存后重新安装：`npm cache clean--force`，再执行`npm install-gopenclaw@latest`。

### **问题 3：npm 安装提示「权限不足，无法创建文件夹」**

原因是终端未获取管理员权限，解决方法超简单：关闭当前 PowerShell/CMD，右键选择「以管理员身份运行」，重新执行安装命令即可。

### **问题 4：WEB UI 页面无法打开，网关服务停止**

若部署后无法访问`http://127.0.0.1:18789/`，说明 Gateway 网关服务停止，执行以下命令重启即可：`openclaw gateway start`（启动网关）`openclaw gateway stop`（后续若需停止，执行此命令）

## **六、后续使用：核心命令与技能推荐**

部署完成后，记住几个核心命令，方便后续管理 OpenClaw；同时推荐几款必装 Skills，快速提升生产力：

### **核心常用命令**

- 启动网关：`openclaw gateway start`

- 停止网关：`openclaw gateway stop`

- 安装 Clawhub CLI（技能管理工具）：

  ```
  npm install -g clawhub@latest --registry https://registry.npmmirror.com
  ```

- 搜索技能：`clawhub search "技能关键词"`

### **必装实用 Skills**

- `memory-search：轻量记忆检索，让 OpenClaw 记住你的操作偏好；`
- `memory-hygiene：自动清理垃圾记忆，保证运行流畅；`
- `summarize：网页 / 文档总结，快速提取核心信息；`
- `email：邮件自动化，自动回复、整理邮件内容。`

## **写在最后**

OpenClaw 在 Windows 上的部署，核心难点在于 SSH 配置和网络问题，只要按本文步骤准备好密钥、配置好环境，避开高频坑点，零基础也能在 15 分钟内完成部署 + GLM 对接 + 飞书绑定。作为开源的 AI 智能体平台，OpenClaw 的可玩性和实用性极强，后续可根据自己的需求安装更多 Skills，解锁文件处理、浏览器自动化、定时任务等更多功能，真正打造属于自己的专属 AI 助手。