# 各平台原生代码模板

> **⚠️ 免责声明**：本文档中的模板语法和 API 基于编写时的各平台文档整理，可能随平台更新而变化。实际开发时请以各平台最新官方文档为准。如发现过时信息，请及时更新本文档或查阅官方文档确认。

## 目录结构对照

| 平台 | 模板文件 | 样式文件 | 逻辑文件 | 配置文件 |
|------|---------|---------|---------|---------|
| 微信 | .wxml | .wxss | .js | .json |
| QQ | .qml | .qss | .js | .json |
| 抖音 | .ttml | .ttss | .js | .json |
| 支付宝 | .axml | .acss | .js | .json |
| 百度 | .swan | .css | .js | .json |
| 快手 | .kml | .kss | .js | .json |

## 项目结构（以微信为例，其他平台类似）

```
miniprogram/
├── app.js                  # 应用入口
├── app.json                # 全局配置
├── app.wxss                # 全局样式
├── project.config.json     # 项目配置
├── sitemap.json            # 搜索配置
├── pages/
│   ├── index/
│   │   ├── index.wxml      # 页面模板
│   │   ├── index.wxss      # 页面样式
│   │   ├── index.js        # 页面逻辑
│   │   └── index.json      # 页面配置
│   └── detail/
│       ├── detail.wxml
│       ├── detail.wxss
│       ├── detail.js
│       └── detail.json
├── components/
│   └── my-component/
│       ├── my-component.wxml
│       ├── my-component.wxss
│       ├── my-component.js
│       └── my-component.json
└── utils/
    └── util.js
```

## 页面模板语法对照

### 数据绑定

```xml
<!-- 微信/QQ/抖音/快手 (WXML/QML/TML/KML 语法相同) -->
<view>{{message}}</view>
<view>{{user.name}}</view>
<image src="{{avatarUrl}}" />

<!-- 支付宝 (AXML) -->
<view>{{message}}</view>
<view>{{user.name}}</view>
<image src="{{avatarUrl}}" />

<!-- 百度 (SWAN) -->
<view>{{message}}</view>
<view>{{user.name}}</view>
<image src="{{avatarUrl}}" />
```

### 条件渲染

```xml
<!-- 微信/QQ/抖音/快手 -->
<view wx:if="{{isLoggedIn}}">已登录</view>
<view wx:elif="{{loading}}">加载中</view>
<view wx:else>未登录</view>

<!-- 支付宝 -->
<view a:if="{{isLoggedIn}}">已登录</view>
<view a:elif="{{loading}}">加载中</view>
<view a:else>未登录</view>

<!-- 百度 -->
<view s-if="isLoggedIn">已登录</view>
<view s-elif="loading">加载中</view>
<view s-else>未登录</view>
```

### 列表渲染

```xml
<!-- 微信/QQ/抖音/快手 -->
<view wx:for="{{items}}" wx:key="id">
  <text>{{index}}: {{item.name}}</text>
</view>

<!-- 支付宝 -->
<view a:for="{{items}}" a:key="id">
  <text>{{index}}: {{item.name}}</text>
</view>

<!-- 百度 -->
<view s-for="items" s-key="id">
  <text>{{index}}: {{item.name}}</text>
</view>
```

### 事件绑定

```xml
<!-- 微信/QQ/抖音/快手 -->
<button bindtap="handleTap">点击</button>
<input bindinput="handleInput" />
<form bindsubmit="handleSubmit">

<!-- 支付宝 -->
<button onTap="handleTap">点击</button>
<input onInput="handleInput" />
<form onSubmit="handleSubmit">

<!-- 百度 -->
<button bindtap="handleTap">点击</button>
<input bindinput="handleInput" />
<form bindsubmit="handleSubmit">
```

### 组件引用

```json
// 微信/QQ/抖音/快手 - 页面 json
{
  "usingComponents": {
    "my-component": "/components/my-component/my-component"
  }
}

// 支付宝 - 页面 json
{
  "usingComponents": {
    "my-component": "/components/my-component/my-component"
  }
}

// 百度 - 页面 json
{
  "usingComponents": {
    "my-component": "/components/my-component/my-component"
  }
}
```

## 页面逻辑（JS）对照

各平台的页面逻辑 API 基本一致，主要差异在 API 前缀：

```javascript
// 微信
Page({
  data: { message: 'Hello' },
  onLoad(options) { },
  onShow() { },
  onHide() { },
  onUnload() { },
  handleTap() {
    wx.showToast({ title: '点击了' })
  }
})

// QQ
Page({
  data: { message: 'Hello' },
  onLoad(options) { },
  onShow() { },
  handleTap() {
    qq.showToast({ title: '点击了' })
  }
})

// 抖音
Page({
  data: { message: 'Hello' },
  onLoad(options) { },
  onShow() { },
  handleTap() {
    tt.showToast({ title: '点击了' })
  }
})

// 支付宝
Page({
  data: { message: 'Hello' },
  onLoad(options) { },
  onShow() { },
  handleTap() {
    my.showToast({ title: '点击了' })
  }
})

// 百度
Page({
  data: { message: 'Hello' },
  onLoad(options) { },
  onShow() { },
  handleTap() {
    swan.showToast({ title: '点击了' })
  }
})

// 快手
Page({
  data: { message: 'Hello' },
  onLoad(options) { },
  onShow() { },
  handleTap() {
    ks.showToast({ title: '点击了' })
  }
})
```

## 全局配置（app.json）对照

```json
// 微信/QQ/抖音/快手 - 结构相同
{
  "pages": ["pages/index/index", "pages/detail/detail"],
  "window": {
    "navigationBarTitleText": "标题",
    "navigationBarBackgroundColor": "#ffffff"
  },
  "tabBar": {
    "color": "#999",
    "selectedColor": "#FF5A5F",
    "list": [
      { "pagePath": "pages/index/index", "text": "首页", "iconPath": "...", "selectedIconPath": "..." }
    ]
  }
}

// 支付宝 - tabBar 结构略有不同
{
  "pages": ["pages/index/index", "pages/detail/detail"],
  "window": {
    "titleText": "标题",
    "titleBarColor": "#ffffff"
  },
  "tabBar": {
    "textColor": "#999",
    "selectedColor": "#FF5A5F",
    "items": [
      { "pagePath": "pages/index/index", "name": "首页", "icon": "...", "activeIcon": "..." }
    ]
  }
}

// 百度 - tabBar 结构
{
  "pages": ["pages/index/index", "pages/detail/detail"],
  "window": {
    "navigationBarTitleText": "标题",
    "navigationBarBackgroundColor": "#ffffff"
  },
  "tabBar": {
    "color": "#999",
    "selectedColor": "#FF5A5F",
    "list": [
      { "pagePath": "pages/index/index", "text": "首页", "iconPath": "...", "selectedIconPath": "..." }
    ]
  }
}
```

## 样式（WXSS/ACSS/CSS）对照

各平台样式语法基本一致，都支持 rpx 单位：

```css
/* 微信 - .wxss */
.container {
  padding: 24rpx;
  display: flex;
}
.text {
  font-size: 28rpx;
  color: #333;
}

/* 支付宝 - .acss */
/* 与微信完全一致，支持 rpx */
.container {
  padding: 24rpx;
  display: flex;
}

/* 百度 - .css */
/* 与微信完全一致，支持 rpx */
.container {
  padding: 24rpx;
  display: flex;
}
```

## 组件开发对照

```javascript
// 微信小程序自定义组件
Component({
  properties: {
    title: { type: String, value: '' }
  },
  data: {
    count: 0
  },
  methods: {
    onTap() {
      this.setData({ count: this.data.count + 1 })
      this.triggerEvent('change', { count: this.data.count })
    }
  }
})

// 支付宝小程序自定义组件 - 几乎一致
Component({
  props: {
    title: ''
  },
  data: {
    count: 0
  },
  methods: {
    onTap() {
      this.setData({ count: this.data.count + 1 })
      this.props.onChange && this.props.onChange({ count: this.data.count })
    }
  }
})

// 百度小程序自定义组件 - 几乎一致
Component({
  properties: {
    title: { type: String, value: '' }
  },
  data: {
    count: 0
  },
  methods: {
    onTap() {
      this.setData({ count: this.data.count + 1 })
      this.triggerEvent('change', { count: this.data.count })
    }
  }
})
```
