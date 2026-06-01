# miniprogram-dev

跨平台小程序全流程开发教练——从需求分析到成功上线的完整开发指导。

## 功能特性

- 覆盖微信、QQ、抖音、支付宝、百度、快手六大平台
- 支持原生开发和 uni-app/Taro 跨平台方案
- 结构化提问引导，逐步明确需求
- 自动生成项目代码和图标资源
- 包含平台代码模板、核心功能对照等参考文档
- 从开发到上线的全流程指导

## 安装

```bash
npx skills add fffyibo/miniprogram-dev-skill
```

或手动安装：

```bash
cd ~/.claude/skills/
git clone https://github.com/fffyibo/miniprogram-dev-skill.git miniprogram-dev
```

## 兼容智能体

本 skill 适用于以下 AI 智能体工具，安装后会自动识别：

| 智能体 | 安装路径 | 说明 |
|-------|---------|------|
| **Claude Code** | `~/.claude/skills/miniprogram-dev` | Anthropic 官方 CLI 工具 |
| **Codex** | `~/.codex/skills/miniprogram-dev` | OpenAI 代码智能体 |
| **OpenClaw** | `~/.openclaw/skills/miniprogram-dev` | 开源智能体框架 |
| **Hermes** | `~/.hermes/skills/miniprogram-dev` | 多模态智能体 |

> 其他支持 skills 规范的智能体也可使用，将 skill 目录放到对应路径即可。

## 使用

在智能体中直接描述你的小程序需求即可自动触发：

```
我想做一个外卖点餐的微信小程序
```

```
帮我开发一个影院购票小程序
```

## 包含内容

```
miniprogram-dev/
├── SKILL.md                  # 主指令文件
└── references/
    ├── platform-guide.md     # 各平台 API 差异
    ├── native-templates.md   # 各平台原生代码模板
    ├── uni-app-templates.md  # uni-app 跨平台模板
    ├── taro-templates.md     # Taro 跨平台模板
    ├── core-features.md      # 核心功能代码对照（登录/支付/分享/地图）
    ├── env-setup.md          # 环境搭建指南
    └── publish-guide.md      # 上线发布指南
```

## 关于本项目

本 skill 目前仍在持续使用和测试调优中，会不断迭代改进。如果你在使用过程中遇到问题或有改进建议，欢迎：

- 提交 [Issue](https://github.com/fffyibo/miniprogram-dev-skill/issues) 反馈 bug
- 提交 [Pull Request](https://github.com/fffyibo/miniprogram-dev-skill/pulls) 贡献代码
- 在 Discussions 中交流讨论

## 许可证

MIT
