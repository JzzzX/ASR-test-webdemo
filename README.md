# ASR-test-webdemo

Piedras 的 Web 原型，用来探索录音、转写、笔记与问答的界面和信息组织。当前保留 **2026 年 3 月 18 日的工作区与独立笔记页版本**：奶白色界面、侧边导航、文件夹与知识库、独立笔记工作台和 Recipes 问答入口。

**当前是本地演示与设计参考。ASR 和历史云端服务已断开；真实转写、AI 摘要、问答和云端同步均不作为当前可用能力。** 页面保留历史功能入口与提示文案，不代表对应服务已经接通。

仓库原名 `piedras-aiNotepad`，现与原生 [Piedras iOS](https://github.com/JzzzX/piedras-ios) 分开维护。

## 界面预览

以下均为恢复版本在本地浏览器中的真实截图，宽 **2880 px**，点击可查看原图。会议、转写和笔记为人工编写的演示数据，使用独立本地数据库；不是实际录音、ASR 识别或 AI 生成结果。没有连接历史云端，也没有录制功能演示视频。

### 首页与最近记录

[![首页与最近记录](docs/screenshots/01-home.png)](docs/screenshots/01-home.png)

### 工作区与会议列表

[![工作区与会议列表](docs/screenshots/02-workspace.png)](docs/screenshots/02-workspace.png)

### 独立笔记工作台

[![独立笔记工作台](docs/screenshots/03-meeting.png)](docs/screenshots/03-meeting.png)

<details>
<summary>更多界面：转写面板、AI 对话与设置</summary>

### 转写阅读面板（人工演示文本）

[![转写阅读面板](docs/screenshots/04-transcript.png)](docs/screenshots/04-transcript.png)

### AI 对话与 Recipes（未接通模型）

[![AI 对话与 Recipes](docs/screenshots/05-chat.png)](docs/screenshots/05-chat.png)

### 录音与 ASR 设置（历史配置入口）

[![录音与 ASR 设置](docs/screenshots/06-settings.png)](docs/screenshots/06-settings.png)

</details>

## 当前能力与边界

| 范围 | 当前情况 |
| --- | --- |
| 首页、工作区、会议列表、笔记编辑与转写阅读 | 可在本地数据库环境中查看和操作 |
| 文件夹、知识库、Recipes 与设置 | 保留这一版本的界面与实现，供继续设计 |
| 真实录音、上传音频与回放 | 实现保留；本次未验证真实音频全流程 |
| ASR | 外部 API 与历史代理未接通；Browser ASR 标签也不代表离线识别可用 |
| AI 摘要、单篇问答与跨工作区检索 | 需要重新配置模型与相关服务；截图没有展示真实模型输出 |
| 云端数据与同步 | 历史云端已断开；本次只使用本地数据 |

本仓库当前没有可用的在线 Demo 承诺。旧 Vercel、Zeabur 等地址只是历史部署记录。

## 本地开发

需要 Node.js 20.9+、npm，以及一个**独立的本地 PostgreSQL 数据库**。默认 Prisma schema 仍是 PostgreSQL；没有数据库时，页面数据读取与保存不能正常工作。

```bash
git clone https://github.com/JzzzX/ASR-test-webdemo.git
cd ASR-test-webdemo
npm ci --legacy-peer-deps
cp .env.example .env.local
```

编辑 `.env.local`：把 `DATABASE_URL` 改为本地演示库的连接地址，供应商密钥留空。不要沿用历史生产库连接。然后让 Prisma 使用同一地址初始化空演示库（Prisma CLI 不自动读取 `.env.local`）：

```bash
export DATABASE_URL='postgresql://USER:PASSWORD@127.0.0.1:5432/piedras_demo?schema=public'
npm run db:generate
npm run db:push
npm run dev:next -- --hostname 127.0.0.1
```

将示例中的 `USER`、`PASSWORD` 换成本地数据库配置。访问 `http://127.0.0.1:3000`，创建工作区、会议并手工输入笔记。`dev:next` 只启动 Next.js，不自动启动历史 ASR 代理。全新数据库不包含截图中的示例记录。

本次截图使用临时 SQLite 适配与人工数据，未把临时数据库、凭据或预览 schema 提交到仓库；仓库默认开发流程仍按上面的 PostgreSQL 配置执行。

## 代码与历史

- `app/`：页面与 API 路由。
- `components/`：导航、工作区、笔记、转写、问答等组件。
- `lib/`：状态、数据库、ASR 与 LLM 接口逻辑。
- `prisma/`：数据库 schema 与历史迁移。
- `scripts/`：历史 ASR 代理及启动脚本。

历史接入细节可查 [3 月 18 日原 README](https://github.com/JzzzX/ASR-test-webdemo/blob/51989f6/README.md) 和 [ASR 代理部署记录](docs/zeabur-asr-proxy.md)。其中的服务地址、供应商配置和功能描述需重新验证，不能视为当前线上状态。

## 恢复与验证

- 恢复基线：[`51989f6`](https://github.com/JzzzX/ASR-test-webdemo/commit/51989f6)，2026-03-18。
- 恢复提交：[`259acb9`](https://github.com/JzzzX/ASR-test-webdemo/commit/259acb9)。使用新提交恢复，保留完整历史。
- 改动前备份：[backup/before-restore-2026-10-26](https://github.com/JzzzX/ASR-test-webdemo/tree/backup/before-restore-2026-10-26)。
- 2026-10-26：生产构建通过；本地首页、工作区、会议笔记、转写面板、问答入口与设置已打开检查。
- 修正依赖锁文件中不匹配的 Floating UI 条目，并调整 ESLint 规则以匹配 CommonJS 脚本的 `require()` 格式；lint 通过，保留一项历史未使用函数警告。
- 本次没有验证真实 ASR、LLM、云同步、外部分享或生产部署。
