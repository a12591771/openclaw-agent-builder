# OpenClaw Agent Builder Skill

为 OpenClaw 创建和配置 AI Agent 的完整技能指南。

## 功能特性

- 📁 **Agent 架构指南** - 完整的目录结构说明
- 📝 **Bootstrap 文件模板** - AGENTS.md, SOUL.md, TOOLS.md, USER.md
- 🔀 **多 Agent 路由** - Bindings 配置和消息路由
- 🔒 **会话隔离** - dmScope, identityLinks 配置
- 🛡️ **安全沙箱** - 每 Agent 沙箱和工具权限限制
- 📊 **会话维护** - 自动清理和过期策略
- 💬 **飞书集成** - 群组绑定和 Mention 配置
- ⚙️ **配置请求模板** - 交互式配置收集

## 安装

### 方式 1：ClawHub（推荐）

```bash
clawhub install openclaw-agent-builder
```

### 方式 2：手动安装

```bash
# 克隆到本地技能目录
git clone https://github.com/a12591771/openclaw-agent-builder.git ~/.openclaw/skills/openclaw-agent-builder

# 或者克隆到工作空间技能目录
git clone https://github.com/a12591771/openclaw-agent-builder.git ~/my-workspace/skills/openclaw-agent-builder
```

### 方式 3：下载单个文件

直接下载 [`SKILL.md`](SKILL.md) 到技能目录：

```bash
mkdir -p ~/.openclaw/skills/openclaw-agent-builder
curl -o ~/.openclaw/skills/openclaw-agent-builder/SKILL.md \
  https://raw.githubusercontent.com/a12591771/openclaw-agent-builder/main/SKILL.md
```

## 快速开始

安装后，当需要创建或配置 OpenClaw Agent 时，AI 会自动加载此技能。

### 创建新 Agent

```bash
# 1. 使用向导创建新 Agent
openclaw agents add my-agent

# 2. AI 会根据技能指南帮你配置：
#    - 工作空间结构
#    - Bootstrap 文件
#    - 模型配置
#    - 会话隔离策略
```

### 配置多 Agent 路由

```json5
// ~/.openclaw/openclaw.json
{
  agents: {
    list: [
      { id: "home", workspace: "~/.openclaw/workspace-home" },
      { id: "work", workspace: "~/.openclaw/workspace-work" },
    ],
  },
  bindings: [
    { agentId: "home", match: { channel: "whatsapp" } },
    { agentId: "work", match: { channel: "feishu" } },
  ],
}
```

## 核心配置场景

| 场景 | 配置机制 |
|------|---------|
| 多人共用聊天账号 | `session.dmScope: "per-channel-peer"` |
| 跨频道共享会话 | `session.identityLinks` |
| 飞书群绑定特定 Agent | `bindings[]` + `channels.feishu.groups` |
| 限制 Agent 工具权限 | `agents[].tools.allow/deny` |
| 安全沙箱隔离 | `agents[].sandbox.mode: "all"` |
| 自动清理过期会话 | `session.maintenance.mode: "enforce"` |

## 文件结构

```
openclaw-agent-builder/
├── SKILL.md         # 主技能文档（655 行）
├── package.json     # ClawHub 元数据
└── README.md        # 本文件
```

## 使用示例

### 示例 1：家庭 Agent（安全限制）

```json5
{
  agents: {
    list: [
      {
        id: "family",
        workspace: "~/.openclaw/workspace-family",
        sandbox: { mode: "all", scope: "agent" },
        tools: {
          allow: ["read", "exec"],
          deny: ["write", "edit", "browser"],
        },
      },
    ],
  },
  bindings: [
    {
      agentId: "family",
      match: { channel: "whatsapp", peer: { kind: "group", id: "120xxx@g.us" } },
    },
  ],
}
```

### 示例 2：工作 Agent（飞书集成）

```json5
{
  agents: {
    list: [
      {
        id: "work",
        workspace: "~/.openclaw/workspace-work",
        model: "anthropic/claude-opus-4-5-20250929",
      },
    ],
  },
  channels: {
    feishu: {
      groupPolicy: "allowlist",
      groupAllowFrom: ["oc_work_group"],
      groups: {
        "oc_work_group": {
          requireMention: true,
          allowFrom: ["ou_manager1", "ou_manager2"],
        },
      },
    },
  },
  bindings: [
    { agentId: "work", match: { channel: "feishu", peer: { kind: "group", id: "oc_work_group" } } },
  ],
}
```

### 示例 3：多人私密对话

```json5
{
  session: {
    // 每人独立会话，防止隐私泄露
    dmScope: "per-channel-peer",
    // 同一用户跨频道共享会话
    identityLinks: {
      alice: ["telegram:123456", "feishu:ou_xxx"],
      bob: ["telegram:789012", "feishu:ou_yyy"],
    },
  },
}
```

## 配置请求模板

创建新 Agent 前，AI 会向你请求以下信息：

```markdown
**创建 Agent 配置请求**

**必填：**
- Agent 名称：[如 finance/supervisor]
- 用途：[如"财务管理"或"飞书群组机器人"]

**可选：**
- [ ] 需要绑定特定飞书群/用户
- [ ] 需要多 Agent 路由
- [ ] 需要安全沙箱隔离
- [ ] 需要限制工具权限
- [ ] 多人共用聊天账号
```

## 相关资源

- [OpenClaw 官方文档](https://docs.openclaw.ai/)
- [Agent Runtime](https://docs.openclaw.ai/concepts/agent.md)
- [多 Agent 路由](https://docs.openclaw.ai/concepts/multi-agent.md)
- [会话管理](https://docs.openclaw.ai/concepts/session.md)
- [飞书频道](https://docs.openclaw.ai/channels/feishu.md)

## 贡献

欢迎提交 Issue 和 Pull Request！

## 许可证

MIT License
