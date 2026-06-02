# miniprogram-dev

跨平台小程序全流程开发指导——从0到1上线自己的小程序。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![skills.sh](https://skills.sh/b/fffyibo/miniprogram-dev-skill)](https://skills.sh/fffyibo/miniprogram-dev-skill)

**支持平台：**
![微信小程序](https://img.shields.io/badge/微信小程序-07C160?style=flat&logo=wechat&logoColor=white)
![支付宝小程序](https://img.shields.io/badge/支付宝小程序-1677FF?style=flat&logo=alipay&logoColor=white)
![抖音小程序](https://img.shields.io/badge/抖音小程序-000000?style=flat&logo=tiktok&logoColor=white)
![QQ小程序](https://img.shields.io/badge/QQ小程序-12B7F5?style=flat&logo=tencentqq&logoColor=white)
![百度小程序](https://img.shields.io/badge/百度小程序-2319DC?style=flat&logo=baidu&logoColor=white)
![快手小程序](https://img.shields.io/badge/快手小程序-FF4906?style=flat&logo=kuaishou&logoColor=white)

## 功能特性

- 覆盖微信、QQ、抖音、支付宝、百度、快手六大平台
- 支持原生开发和 uni-app/Taro 跨平台方案
- 结构化提问引导，逐步明确需求
- 自动生成项目代码和图标资源
- 包含平台代码模板、核心功能对照等参考文档
- 从开发到上线的全流程指导

## 安装

```bash
# 全局安装（推荐，所有项目可用）
npx skills add fffyibo/miniprogram-dev-skill -g

# 项目级安装（仅当前项目可用）
npx skills add fffyibo/miniprogram-dev-skill
```

或手动安装：

```bash
cd ~/.claude/skills/
git clone https://github.com/fffyibo/miniprogram-dev-skill.git miniprogram-dev
```

## 兼容智能体

本 skill 基于 [skills.sh](https://skills.sh) 规范开发，支持 50+ 个 AI 智能体工具，安装时自动检测已安装的智能体。常用兼容列表：

| 智能体 | `--agent` 参数 | 全局安装路径 |
|-------|---------------|-------------|
| **Claude Code** | `claude-code` | `~/.claude/skills/` |
| **Codex** | `codex` | `~/.codex/skills/` |
| **OpenClaw** | `openclaw` | `~/.openclaw/skills/` |
| **Hermes Agent** | `hermes-agent` | `~/.hermes/skills/` |
| **Cursor** | `cursor` | `~/.cursor/skills/` |
| **Windsurf** | `windsurf` | `~/.codeium/windsurf/skills/` |
| **GitHub Copilot** | `github-copilot` | `~/.copilot/skills/` |
| **Gemini CLI** | `gemini-cli` | `~/.gemini/skills/` |
| **Roo Code** | `roo` | `~/.roo/skills/` |

> 完整支持列表见 [skills 官方文档](https://github.com/vercel-labs/skills)。

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
