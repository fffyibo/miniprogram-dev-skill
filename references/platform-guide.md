# 各平台 API 差异与适配指南

> **⚠️ 免责声明**：本文档中的平台信息（API 签名、包大小限制、审核规则等）基于编写时的官方文档整理，可能随平台更新而变化。实际开发时请以各平台最新官方文档为准。如发现过时信息，请及时更新本文档或查阅官方文档确认。

## 平台概览

| 平台 | 开发框架 | 包大小限制 | 主要语言 |
|------|---------|-----------|---------|
| 微信 | 微信小程序 | 主包 2MB，分包 20MB | WXML + WXSS + JS/TS |
| QQ | QQ 小程序 | 主包 2MB，分包 20MB | QML + QSS + JS/TS |
| 抖音 | 抖音小程序 | 主包 4MB，分包 8MB | TTML + TTSS + JS/TS |
| 支付宝 | 支付宝小程序 | 主包 4MB，分包 8MB | AXML + ACSS + JS/TS |
| 百度 | 百度智能小程序 | 主包 4MB，分包 8MB | SWAN + CSS + JS/TS |
| 快手 | 快手小程序 | 主包 4MB，分包 8MB | KML + KSS + JS/TS |

## 条件编译方案

### uni-app 条件编译

```javascript
// #ifdef MP-WEIXIM
console.log('微信小程序专属代码')
// #endif

// #ifdef MP-ALIPAY
console.log('支付宝小程序专属代码')
// #endif

// #ifdef MP
console.log('所有小程序平台通用代码')
// #endif
```

平台标识对照：
- 微信：`MP-WEIXIN`
- QQ：`MP-QQ`
- 抖音：`MP-TOUTIAO`
- 支付宝：`MP-ALIPAY`
- 百度：`MP-BAIDU`
- 快手：`MP-KUAISHOU`

### Taro 条件编译

```javascript
if (process.env.TARO_ENV === 'weapp') {
  console.log('微信小程序专属代码')
}
if (process.env.TARO_ENV === 'alipay') {
  console.log('支付宝小程序专属代码')
}
```

## 核心 API 差异

### 登录流程

**微信/QQ**：
1. `wx.login()` 获取临时 code
2. 将 code 发送到后端
3. 后端调用微信接口换取 openid/session_key

**支付宝**：
1. `my.getAuthCode()` 获取授权码
2. 将授权码发送到后端
3. 后端调用支付宝接口换取 user_id

**抖音**：
1. `tt.login()` 获取 code
2. 后端调用抖音接口换取 openid/session_key

**百度**：
1. `swan.login()` 获取 code
2. 后端调用百度接口换取 session_key

### 支付能力

**微信**：`wx.requestPayment` — 需要商户号，参数包含 timeStamp、nonceStr、package、signType、paySign

**支付宝**：`my.tradePay` — 需要签约支付宝小程序支付，参数为 tradeNO

**抖音**：`tt.pay` — 需要签约抖音支付，参数包含 orderInfo、orderType

**百度**：`swan.requestPolymerPayment` — 需要签约百度收银台

### 分享能力

**微信**：通过 `onShareAppMessage` 和 `onShareTimeline` 生命周期钩子配置

**支付宝**：通过 `my.showSharePanel` 主动调用，或页面配置 `options.menu.hasCollect`

**抖音**：通过 `tt.showShareMenu` 和 `onShareAppMessage` 配置

**百度**：通过 `swan.openShare` 或页面配置分享

### 文件上传

各平台均提供 `uploadFile` API，但参数名和返回值略有差异：
- 微信/QQ/百度/快手：`wx.uploadFile({ url, filePath, name, ... })`
- 支付宝：`my.uploadFile({ url, filePath, fileName, ... })`（注意 `name` 变为 `fileName`）
- 抖音：`tt.uploadFile({ url, filePath, name, ... })`

### 地图能力

- 微信：`wx.getLocation` + `wx.openLocation` / 内置 `map` 组件
- 支付宝：`my.getLocation` + `my.openLocation` / 内置 `map` 组件
- 抖音：`tt.getLocation` + `tt.openLocation`
- 百度：`swan.getLocation` + `swan.openLocation`

注意：各平台经纬度坐标系可能不同（WGS84 / GCJ02 / BD09），需要做坐标转换。

## 跨平台框架选择建议

### 选 uni-app 的场景
- 团队熟悉 Vue 语法
- 需要覆盖更多平台（uni-app 支持 H5、App 等）
- 需要丰富的插件生态
- 项目复杂度中等

### 选 Taro 的场景
- 团队熟悉 React 语法
- 已有 React 技术栈项目
- 需要与 React Native 复用部分逻辑
- 对 TypeScript 支持要求高

### 选原生开发的场景
- 只针对单一平台
- 需要深度使用平台特有能力
- 对性能有极致要求
- 需要使用平台专属组件（如微信的 live-player）
