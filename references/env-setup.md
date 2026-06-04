# 开发环境搭建指南

> **⚠️ 免责声明**：本文档中的版本号和下载链接基于编写时的官方信息整理，可能随版本更新而变化。实际安装时请访问各工具官网确认最新版本。

## 基础环境

### Node.js 安装

推荐使用 LTS 版本（当前为 Node.js 20.x）。

**Windows**：
1. 访问 https://nodejs.org 下载 LTS 安装包
2. 运行安装程序，勾选"Add to PATH"
3. 安装完成后验证：`node -v` 和 `npm -v`

**macOS**：
```bash
# 使用 Homebrew
brew install node@20
# 或使用 nvm
nvm install 20
nvm use 20
```

### 包管理器推荐

推荐使用 pnpm（更快、更省磁盘空间）：
```bash
npm install -g pnpm
```

## uni-app 开发环境

### 使用 HBuilderX（推荐新手）

1. 下载 HBuilderX：https://www.dcloud.io/hbuilderx.html
2. 安装后，新建项目 → 选择 uni-app 模板
3. 内置了各平台的编译和预览功能

### 使用 CLI（推荐进阶）

```bash
# 创建 Vue 3 + Vite 项目
npx degit dcloudio/uni-preset-vue#vite my-miniprogram
cd my-miniprogram
pnpm install

# 运行到微信小程序
pnpm dev:mp-weixin

# 运行到支付宝小程序
pnpm dev:mp-alipay

# 运行到抖音小程序
pnpm dev:mp-toutiao
```

编译产物在 `dist/dev/mp-weixin/` 等目录下，需要在对应开发者工具中导入。

## Taro 开发环境

```bash
# 安装 Taro CLI
npm install -g @tarojs/cli

# 创建项目
taro init my-miniprogram
# 选择 React + TypeScript + 你喜欢的 CSS 预处理器

cd my-miniprogram
pnpm install

# 编译到微信小程序
pnpm dev:weapp

# 编译到支付宝小程序
pnpm dev:alipay

# 编译到抖音小程序
pnpm dev:tt
```

## 各平台开发者工具

### 微信开发者工具
- 下载：https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html
- 需要注册微信小程序账号获取 AppID
- 导入项目时选择 `dist/dev/mp-weixin` 目录

### 支付宝小程序开发者工具
- 下载：https://open.alipay.com/mini/developer
- 需要注册支付宝小程序账号
- 导入项目时选择 `dist/dev/mp-alipay` 目录

### 抖音小程序开发者工具
- 下载：https://developer.open-douyin.com/docs/resource/zh-CN/mini-app/develop/developer-instrument/overview
- 需要注册抖音小程序账号
- 导入项目时选择 `dist/dev/mp-toutiao` 目录

### 百度开发者工具
- 下载：https://smartprogram.baidu.com/docs/develop/devtools/download/
- 需要注册百度智能小程序账号

### QQ 小程序开发者工具
- 下载：https://q.qq.com/#/devTools
- 需要注册 QQ 小程序账号

### 快手小程序开发者工具
- 下载：https://mp.kuaishou.com/docs/develop/devTools/
- 需要注册快手小程序账号

## IDE 推荐配置

### VS Code 插件

必备插件：
- **uni-app**：uni-app 语法支持（HBuilderX 用户可跳过）
- **Taro**：Taro 语法支持
- **ESLint**：代码规范检查
- **Prettier**：代码格式化
- **minapp-vscode**：小程序语法高亮（WXML 等）

推荐插件：
- **GitLens**：Git 历史查看
- **Error Lens**：行内错误提示
- **Auto Rename Tag**：自动重命名配对标签

### TypeScript 配置

推荐在项目中启用 TypeScript 以获得更好的类型检查：

```json
// tsconfig.json (uni-app)
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    "strict": true,
    "jsx": "preserve",
    "moduleResolution": "node",
    "esModuleInterop": true,
    "sourceMap": true,
    "skipLibCheck": true,
    "resolveJsonModule": true
  }
}
```

## 常见问题排查

**问题：编译报错找不到模块**
- 检查 `node_modules` 是否安装完整，尝试删除后重新 `pnpm install`

**问题：微信开发者工具无法预览**
- 检查 AppID 是否配置正确
- 确认编译产物目录路径是否正确

**问题：样式在不同平台显示不一致**
- 避免使用 rpx 以外的绝对单位
- 检查是否使用了平台不支持的 CSS 属性
