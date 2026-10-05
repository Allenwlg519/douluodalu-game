# 斗罗大陆：星隙迷局 DeepSeek AI 接入版

这个包已经把原单页文字游戏接入统一 AI 后端：

- 前端仍然是单页 `index.html`，不会保存或暴露 API Key。
- 玩家在关系页底部输入“对唐三说：……”或“问小舞……”时，会调用 `/api/chat`。
- `/api/chat` 是 Cloudflare Pages Function，默认调用 DeepSeek 官方 OpenAI 兼容接口。
- 默认模型：`deepseek-flash`。

## 文件结构

```text
public/
  index.html
  _headers
functions/
  api/
    chat.js
wrangler.toml
README-部署说明.md
```

## Cloudflare 环境变量

在 Cloudflare Pages 项目里设置：

```text
DEEPSEEK_API_KEY=你的 DeepSeek API Key
```

可选：

```text
AI_PROVIDER=deepseek
AI_MODEL=deepseek-flash
AI_BASE_URL=https://api.deepseek.com
```

以后如果要换成另一个 OpenAI 兼容服务，可以继续让前端调用同一个 `/api/chat`，只改后端环境变量和少量后端配置。

## 推荐部署方式

因为本版本包含 `/functions/api/chat.js`，不要只用普通静态网页拖拽上传，否则 `/api/chat` 可能不会生效。推荐两种方式：

### 方式一：Wrangler 部署

在解压后的项目根目录运行：

```bash
npx wrangler pages deploy public --functions functions
```

然后到 Cloudflare Pages 的 Settings -> Variables and Secrets 添加 `DEEPSEEK_API_KEY`，保存后重新部署一次。

### 方式二：Git 集成

把整个项目目录推到 GitHub/Gitee，再在 Cloudflare Pages 连接仓库：

```text
Build command: 留空
Build output directory: public
Functions directory: functions
```

同样在 Variables and Secrets 中添加 `DEEPSEEK_API_KEY`。

## 使用方式

进入游戏后打开“关系”页，在底部统一输入框写：

```text
对唐三说：我不想只按主线走，你怎么看？
问小舞：如果身份被发现，你最担心谁受伤？
向大师请教：越级吸收魂环的真正风险是什么？
```

这些会走 AI 自由交谈。拥抱、送礼、切磋、杀死等规则动作仍由原本本地系统结算，避免 AI 直接改乱主线或数值。

## 成本控制

前端每次只发送当前 NPC、玩家摘要、地点、关系值和最近几条对话，不会把完整游戏源码或全部存档发给模型。后端也限制了输出长度和关系变化幅度。
