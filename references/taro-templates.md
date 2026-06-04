# Taro 跨平台代码模板

> **⚠️ 免责声明**：本文档中的版本号和 API 基于编写时的 Taro 官方文档整理，Taro 版本更新频繁，推荐版本可能已过时。实际开发时请查阅 [Taro 官方文档](https://taro-docs.jd.com/) 确认最新版本和用法。

## 项目结构

```
my-taro/
├── src/
│   ├── pages/
│   │   ├── index/
│   │   │   ├── index.tsx          # 首页
│   │   │   └── index.module.scss  # 样式
│   │   └── detail/
│   │       ├── index.tsx
│   │       └── index.module.scss
│   ├── components/
│   │   └── MyComponent/
│   │       ├── index.tsx
│   │       └── index.module.scss
│   ├── store/                     # 状态管理
│   ├── services/                  # API 请求
│   ├── utils/                     # 工具函数
│   ├── app.tsx                    # 应用入口
│   ├── app.config.ts              # 应用配置
│   └── app.scss                   # 全局样式
├── config/                        # 构建配置
│   ├── index.ts
│   ├── dev.ts
│   └── prod.ts
├── project.config.json            # 微信项目配置
├── package.json
└── tsconfig.json
```

## 关键配置

### app.config.ts

```typescript
export default defineAppConfig({
  pages: [
    'pages/index/index',
    'pages/detail/detail'
  ],
  tabBar: {
    color: '#999',
    selectedColor: '#FF5A5F',
    list: [
      {
        pagePath: 'pages/index/index',
        text: '首页',
        iconPath: 'assets/tab-home.png',
        selectedIconPath: 'assets/tab-home-active.png'
      }
    ]
  },
  window: {
    backgroundTextStyle: 'light',
    navigationBarBackgroundColor: '#ffffff',
    navigationBarTitleText: '小程序',
    navigationBarTextStyle: 'black'
  }
})
```

### 页面配置（页面级 .config.ts）

```typescript
// pages/index/index.config.ts
export default definePageConfig({
  navigationBarTitleText: '首页',
  enablePullDownRefresh: true
})
```

## 页面模板

### 基础页面（React + TypeScript）

```tsx
// pages/index/index.tsx
import { View, Text, Image, Button } from '@tarojs/components'
import { useLoad, navigateTo } from '@tarojs/taro'
import { useState } from 'react'
import './index.module.scss'

interface MovieItem {
  id: number
  title: string
  poster: string
}

export default function Index() {
  const [list, setList] = useState<MovieItem[]>([])

  useLoad(() => {
    loadData()
  })

  const loadData = () => {
    // 加载数据
  }

  const goDetail = (id: number) => {
    navigateTo({ url: `/pages/detail/detail?id=${id}` })
  }

  return (
    <View className='container'>
      {list.map(item => (
        <View key={item.id} className='card'>
          <Image src={item.poster} mode='aspectFill' lazyLoad />
          <Text>{item.title}</Text>
          <Button onClick={() => goDetail(item.id)}>查看详情</Button>
        </View>
      ))}
    </View>
  )
}
```

```scss
// pages/index/index.module.scss
.container {
  padding: 24rpx;
}

.card {
  background: #fff;
  border-radius: 16rpx;
  padding: 24rpx;
  margin-bottom: 20rpx;

  image {
    width: 100%;
    height: 360rpx;
    border-radius: 12rpx;
  }
}
```

### 详情页面（接收参数）

```tsx
// pages/detail/detail.tsx
import { View, Text } from '@tarojs/components'
import { useRouter } from '@tarojs/taro'
import { useState, useEffect } from 'react'
import './index.module.scss'

export default function Detail() {
  const router = useRouter()
  const [detail, setDetail] = useState<any>(null)

  useEffect(() => {
    const id = router.params.id
    if (id) {
      loadDetail(id)
    }
  }, [])

  const loadDetail = async (id: string) => {
    // 加载详情数据
  }

  return (
    <View className='detail-page'>
      {detail && (
        <>
          <Text className='title'>{detail.title}</Text>
          <Text className='desc'>{detail.description}</Text>
        </>
      )}
    </View>
  )
}
```

### 登录页面

```tsx
// pages/login/login.tsx
import { View, Button, Input } from '@tarojs/components'
import { login, getUserProfile } from '@tarojs/taro'
import { useState } from 'react'

export default function Login() {
  const [phone, setPhone] = useState('')

  // 微信一键登录
  const handleWxLogin = async () => {
    try {
      const { code } = await login()
      const userInfo = await getUserProfile({ desc: '用于完善用户资料' })
      // 将 code 和 userInfo 发送到后端
    } catch (err) {
      console.error('登录失败', err)
    }
  }

  // 手机号登录
  const handlePhoneLogin = async () => {
    // 发送验证码 + 登录逻辑
  }

  return (
    <View className='login-page'>
      <Button onClick={handleWxLogin}>微信一键登录</Button>
      <Input
        type='number'
        placeholder='请输入手机号'
        value={phone}
        onInput={(e) => setPhone(e.detail.value)}
      />
      <Button onClick={handlePhoneLogin}>手机号登录</Button>
    </View>
  )
}
```

## 条件编译

### 平台标识

| 平台 | 标识 |
|------|------|
| 微信小程序 | `weapp` |
| QQ小程序 | `qq` |
| 抖音小程序 | `tt` |
| 支付宝小程序 | `alipay` |
| 百度小程序 | `swan` |
| 快手小程序 | `ks` |
| H5 | `h5` |
| React Native | `rn` |

### JS/TS 中的条件编译

```typescript
// 方式一：process.env.TARO_ENV
if (process.env.TARO_ENV === 'weapp') {
  console.log('微信小程序')
}

if (process.env.TARO_ENV === 'alipay') {
  console.log('支付宝小程序')
}

// 方式二：编译注释
// 以下代码只在微信小程序中编译
// #if defined(weapp)
console.log('微信专属')
// #endif
```

### 模板中的条件编译

```tsx
import { View, Button } from '@tarojs/components'

export default function MyPage() {
  return (
    <View>
      {/* 通用内容 */}
      <View>所有平台显示</View>

      {/* 条件渲染 */}
      {process.env.TARO_ENV === 'weapp' && (
        <Button open-type='getUserInfo'>微信专属按钮</Button>
      )}

      {process.env.TARO_ENV === 'alipay' && (
        <Button open-type='getAuthorize'>支付宝专属按钮</Button>
      )}
    </View>
  )
}
```

## 常用 Hooks

### useRequest

```typescript
// hooks/useRequest.ts
import { useState, useEffect } from 'react'

function useRequest<T>(fetcher: () => Promise<T>) {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    fetcher()
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false))
  }, [])

  return { data, loading, error }
}

export default useRequest
```

### useAppStore（简易状态管理）

```typescript
// store/index.ts
import { useState, useCallback } from 'react'

interface AppState {
  userInfo: any | null
  token: string
}

const initialState: AppState = {
  userInfo: null,
  token: ''
}

// 简易全局状态（小型项目够用）
let globalState = { ...initialState }
let listeners: Array<() => void> = []

export function useAppStore() {
  const [, setState] = useState(0)

  const rerender = useCallback(() => {
    listeners.forEach(fn => fn())
  }, [])

  const setUserInfo = (info: any) => {
    globalState = { ...globalState, userInfo: info }
    rerender()
  }

  const setToken = (token: string) => {
    globalState = { ...globalState, token }
    rerender()
  }

  const logout = () => {
    globalState = { ...initialState }
    rerender()
  }

  return {
    ...globalState,
    setUserInfo,
    setToken,
    logout
  }
}
```

## 网络请求封装

```typescript
// services/request.ts
import Taro from '@tarojs/taro'

const BASE_URL = 'https://api.example.com'

interface RequestOptions {
  url: string
  method?: 'GET' | 'POST' | 'PUT' | 'DELETE'
  data?: any
  header?: Record<string, string>
}

const request = async <T>(options: RequestOptions): Promise<T> => {
  const token = Taro.getStorageSync('token')

  const res = await Taro.request({
    url: BASE_URL + options.url,
    method: options.method || 'GET',
    data: options.data || {},
    header: {
      'Authorization': token || '',
      'Content-Type': 'application/json',
      ...options.header
    }
  })

  if (res.statusCode === 200) {
    return res.data as T
  } else if (res.statusCode === 401) {
    Taro.navigateTo({ url: '/pages/login/login' })
    throw new Error('未登录')
  } else {
    throw new Error(res.data?.message || '请求失败')
  }
}

export default request
```

## 运行命令

```bash
# 开发 - 微信小程序
pnpm dev:weapp

# 开发 - 支付宝小程序
pnpm dev:alipay

# 开发 - 抖音小程序
pnpm dev:tt

# 开发 - 百度小程序
pnpm dev:swan

# 开发 - H5
pnpm dev:h5

# 构建 - 微信小程序
pnpm build:weapp
```

编译产物在 `dist/` 目录下，用对应开发者工具导入即可预览。
