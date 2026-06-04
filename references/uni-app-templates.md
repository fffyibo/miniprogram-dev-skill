# uni-app 跨平台代码模板

> **⚠️ 免责声明**：本文档中的版本号和 API 基于编写时的 uni-app 官方文档整理，uni-app 版本更新频繁，推荐版本可能已过时。实际开发时请查阅 [uni-app 官方文档](https://uniapp.dcloud.net.cn/) 确认最新版本和用法。

## 项目结构

```
my-uniapp/
├── src/
│   ├── pages/
│   │   ├── index/
│   │   │   └── index.vue          # 首页
│   │   └── detail/
│   │       └── detail.vue         # 详情页
│   ├── components/
│   │   └── my-component.vue       # 公共组件
│   ├── static/                    # 静态资源
│   ├── store/                     # Vuex/Pinia 状态管理
│   ├── utils/                     # 工具函数
│   ├── App.vue                    # 应用入口
│   ├── main.js                    # 主入口
│   ├── manifest.json              # 应用配置
│   ├── pages.json                 # 页面路由配置
│   └── uni.scss                   # 全局样式变量
├── package.json
└── vite.config.js
```

## 关键配置

### pages.json

```json
{
  "pages": [
    {
      "path": "pages/index/index",
      "style": {
        "navigationBarTitleText": "首页"
      }
    },
    {
      "path": "pages/detail/detail",
      "style": {
        "navigationBarTitleText": "详情"
      }
    }
  ],
  "tabBar": {
    "color": "#999",
    "selectedColor": "#FF5A5F",
    "list": [
      {
        "pagePath": "pages/index/index",
        "text": "首页",
        "iconPath": "static/tab-home.png",
        "selectedIconPath": "static/tab-home-active.png"
      }
    ]
  },
  "globalStyle": {
    "navigationBarTextStyle": "black",
    "navigationBarTitleText": "小程序",
    "navigationBarBackgroundColor": "#ffffff",
    "backgroundColor": "#f5f5f5"
  }
}
```

### manifest.json（关键字段）

```json
{
  "name": "我的小程序",
  "appid": "__UNI__XXXXXXX",
  "versionName": "1.0.0",
  "mp-weixin": {
    "appid": "wx_appid_here",
    "setting": { "urlCheck": false },
    "usingComponents": true
  },
  "mp-alipay": {
    "appid": "alipay_appid_here"
  },
  "mp-toutiao": {
    "appid": "toutiao_appid_here"
  },
  "mp-baidu": {
    "appid": "baidu_appid_here"
  },
  "mp-qq": {
    "appid": "qq_appid_here"
  },
  "mp-kuaishou": {
    "appid": "kuaishou_appid_here"
  }
}
```

## 页面模板

### 基础页面

```vue
<template>
  <view class="container">
    <view class="card" v-for="item in list" :key="item.id">
      <image :src="item.poster" mode="aspectFill" lazy-load />
      <text>{{ item.title }}</text>
      <button @tap="goDetail(item.id)">查看详情</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      list: []
    }
  },
  onLoad() {
    this.loadData()
  },
  methods: {
    loadData() {
      // 加载数据
    },
    goDetail(id) {
      uni.navigateTo({
        url: `/pages/detail/detail?id=${id}`
      })
    }
  }
}
</script>

<style scoped>
.container {
  padding: 24rpx;
}
.card {
  background: #fff;
  border-radius: 16rpx;
  padding: 24rpx;
  margin-bottom: 20rpx;
}
</style>
```

### 登录页面（微信一键登录）

```vue
<template>
  <view class="login-page">
    <button open-type="getPhoneNumber" @getphonenumber="onGetPhone">
      微信一键登录
    </button>
  </view>
</template>

<script>
export default {
  methods: {
    onGetPhone(e) {
      if (e.detail.code) {
        // 将 code 发送到后端换取手机号
        this.loginWithPhone(e.detail.code)
      }
    },
    async loginWithPhone(code) {
      // 调用后端接口
    }
  }
}
</script>
```

### 列表页面（下拉刷新 + 上拉加载）

```vue
<template>
  <view>
    <view v-for="item in list" :key="item.id">
      <text>{{ item.title }}</text>
    </view>
    <view v-if="loading" class="loading">加载中...</view>
    <view v-if="noMore" class="no-more">没有更多了</view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      list: [],
      page: 1,
      loading: false,
      noMore: false
    }
  },
  onLoad() {
    this.loadData()
  },
  onPullDownRefresh() {
    this.page = 1
    this.noMore = false
    this.loadData().then(() => {
      uni.stopPullDownRefresh()
    })
  },
  onReachBottom() {
    if (!this.loading && !this.noMore) {
      this.page++
      this.loadData()
    }
  },
  methods: {
    async loadData() {
      this.loading = true
      // 模拟请求
      const newData = await this.fetchData(this.page)
      if (newData.length === 0) {
        this.noMore = true
      } else {
        this.list = this.page === 1 ? newData : [...this.list, ...newData]
      }
      this.loading = false
    }
  }
}
</script>
```

## 条件编译

### 平台标识

| 平台 | 标识 |
|------|------|
| 微信小程序 | `MP-WEIXIN` |
| QQ小程序 | `MP-QQ` |
| 抖音小程序 | `MP-TOUTIAO` |
| 支付宝小程序 | `MP-ALIPAY` |
| 百度小程序 | `MP-BAIDU` |
| 快手小程序 | `MP-KUAISHOU` |
| 所有小程序 | `MP` |
| H5 | `H5` |
| App | `APP-PLUS` |

### 模板中的条件编译

```vue
<template>
  <view>
    <!-- 通用内容 -->
    <text>所有平台显示</text>

    <!-- #ifdef MP-WEIXIN -->
    <button open-type="getUserInfo">微信专属按钮</button>
    <!-- #endif -->

    <!-- #ifdef MP-ALIPAY -->
    <button open-type="getAuthorize">支付宝专属按钮</button>
    <!-- #endif -->

    <!-- #ifdef MP -->
    <view>所有小程序平台显示</view>
    <!-- #endif -->
  </view>
</template>
```

### JS 中的条件编译

```javascript
export default {
  methods: {
    login() {
      // #ifdef MP-WEIXIN
      uni.login({
        provider: 'weixin',
        success: (res) => {
          console.log('微信登录', res.code)
        }
      })
      // #endif

      // #ifdef MP-ALIPAY
      my.getAuthCode({
        success: (res) => {
          console.log('支付宝登录', res.authCode)
        }
      })
      // #endif

      // #ifdef MP-TOUTIAO
      tt.login({
        success: (res) => {
          console.log('抖音登录', res.code)
        }
      })
      // #endif
    }
  }
}
```

### CSS 中的条件编译

```vue
<style>
/* 通用样式 */
.title {
  font-size: 32rpx;
}

/* #ifdef MP-WEIXIN */
.title {
  /* 微信专属样式 */
}
/* #endif */

/* #ifdef MP-ALIPAY */
.title {
  /* 支付宝专属样式 */
}
/* #endif */
</style>
```

## 常用 API 封装

### 网络请求封装

```javascript
// utils/request.js
const BASE_URL = 'https://api.example.com'

const request = (options) => {
  return new Promise((resolve, reject) => {
    uni.request({
      url: BASE_URL + options.url,
      method: options.method || 'GET',
      data: options.data || {},
      header: {
        'Authorization': uni.getStorageSync('token') || '',
        ...options.header
      },
      success: (res) => {
        if (res.statusCode === 200) {
          resolve(res.data)
        } else if (res.statusCode === 401) {
          // token 过期，重新登录
          uni.navigateTo({ url: '/pages/login/login' })
          reject(new Error('未登录'))
        } else {
          reject(res)
        }
      },
      fail: reject
    })
  })
}

export default request
```

### 状态管理（Vuex）

```javascript
// store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    userInfo: uni.getStorageSync('userInfo') || null,
    token: uni.getStorageSync('token') || ''
  },
  mutations: {
    SET_USER_INFO(state, info) {
      state.userInfo = info
      uni.setStorageSync('userInfo', info)
    },
    SET_TOKEN(state, token) {
      state.token = token
      uni.setStorageSync('token', token)
    },
    LOGOUT(state) {
      state.userInfo = null
      state.token = ''
      uni.removeStorageSync('userInfo')
      uni.removeStorageSync('token')
    }
  },
  actions: {
    async login({ commit }, loginCode) {
      // 调用登录接口
      // const res = await api.login(loginCode)
      // commit('SET_USER_INFO', res.userInfo)
      // commit('SET_TOKEN', res.token)
    }
  }
})
```

## 运行命令

```bash
# 开发 - 微信小程序
pnpm dev:mp-weixin

# 开发 - 支付宝小程序
pnpm dev:mp-alipay

# 开发 - 抖音小程序
pnpm dev:mp-toutiao

# 开发 - 百度小程序
pnpm dev:mp-baidu

# 开发 - H5
pnpm dev:h5

# 构建 - 微信小程序
pnpm build:mp-weixin
```

编译产物在 `dist/dev/mp-weixin/` 等目录下，用对应开发者工具导入即可预览。
