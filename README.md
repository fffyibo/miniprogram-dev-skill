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
npx skills add 你的用户名/miniprogram-dev-skill
```

或手动安装：

```bash
cd ~/.claude/skills/
git clone https://github.com/你的用户名/miniprogram-dev-skill.git miniprogram-dev
```

## 使用

在 Claude Code 中直接描述你的小程序需求即可自动触发：

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

## 许可证

MIT
