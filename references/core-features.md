# 核心功能代码对照

> **⚠️ 免责声明**：本文档中的代码示例基于编写时的各平台 API 整理，API 签名和调用方式可能随平台更新而变化。实际开发时请以各平台最新官方文档为准。如发现过时信息，请及时更新本文档或查阅官方文档确认。

## 登录功能

### 微信小程序登录

```javascript
// 微信登录流程：wx.login() 获取 code → 发送到后端 → 后端换取 openid/session_key

// 1. 获取登录 code
wx.login({
  success: (res) => {
    if (res.code) {
      // 2. 将 code 发送到后端
      wx.request({
        url: 'https://api.example.com/login',
        method: 'POST',
        data: { code: res.code },
        success: (response) => {
          // 3. 保存 token
          wx.setStorageSync('token', response.data.token)
        }
      })
    }
  }
})

// 获取用户头像昵称（2023年后新版接口）
// 方式一：头像昵称填写能力（用户主动填写）
<button open-type="chooseAvatar" bindchooseavatar="onChooseAvatar">
  选择头像
</button>
<input type="nickname" bindinput="onInputNickname" placeholder="请输入昵称" />

// 方式二：已废弃 getUserProfile，现在推荐用头像昵称填写

// 获取手机号（需要用户授权）
// 模板：
<button open-type="getPhoneNumber" bindgetphonenumber="onGetPhone">
  手机号快捷登录
</button>

// JS：
Page({
  onGetPhone(e) {
    if (e.detail.code) {
      // 将 code 发送到后端换取手机号
      wx.request({
        url: 'https://api.example.com/getPhone',
        method: 'POST',
        data: { code: e.detail.code },
        success: (res) => {
          console.log('手机号', res.data.phoneNumber)
        }
      })
    }
  }
})
```

### 支付宝小程序登录

```javascript
// 支付宝登录流程：my.getAuthCode() 获取 authCode → 发送到后端 → 后端换取 user_id

// 1. 获取授权码
my.getAuthCode({
  scopes: 'auth_user',
  success: (res) => {
    if (res.authCode) {
      // 2. 将 authCode 发送到后端
      my.request({
        url: 'https://api.example.com/login',
        method: 'POST',
        data: { authCode: res.authCode },
        success: (response) => {
          my.setStorageSync({ key: 'token', data: response.data.token })
        }
      })
    }
  }
})

// 获取用户信息（已授权情况下）
my.getOpenUserInfo({
  success: (res) => {
    const userInfo = JSON.parse(res.response).response
    console.log(userInfo.avatar, userInfo.nickName)
  }
})

// 获取手机号（需要签约手机号快速验证组件）
// 模板：
<button open-type="getPhoneNumber" onGetPhoneNumber="onGetPhone">
  手机号快捷登录
</button>

// JS：
Page({
  onGetPhone(e) {
    if (e.detail.response) {
      // 将加密数据发送到后端解密获取手机号
    }
  }
})
```

### 抖音小程序登录

```javascript
// 抖音登录流程：tt.login() 获取 code → 发送到后端 → 后端换取 openid/session_key

tt.login({
  success: (res) => {
    if (res.code) {
      tt.request({
        url: 'https://api.example.com/login',
        method: 'POST',
        data: { code: res.code },
        success: (response) => {
          tt.setStorageSync('token', response.data.token)
        }
      })
    }
  }
})

// 获取用户信息
tt.getUserProfile({
  success: (res) => {
    console.log(res.userInfo)
  }
})
```

### 百度小程序登录

```javascript
// 百度登录流程：swan.login() 获取 code → 发送到后端 → 后端换取 session_key

swan.login({
  success: (res) => {
    if (res.code) {
      swan.request({
        url: 'https://api.example.com/login',
        method: 'POST',
        data: { code: res.code },
        success: (response) => {
          swan.setStorageSync('token', response.data.token)
        }
      })
    }
  }
})

// 获取用户信息
swan.getUserInfo({
  success: (res) => {
    console.log(res.userInfo)
  }
})
```

### uni-app 跨平台登录

```javascript
// uni-app 统一 API，自动适配各平台

// 登录
uni.login({
  provider: 'weixin', // 支付宝可不传
  success: (res) => {
    console.log('登录 code:', res.code)
    // 发送到后端
  }
})

// 获取用户信息（各平台适配）
// #ifdef MP-WEIXIN
// 微信使用头像昵称填写组件
// #endif

// #ifdef MP-ALIPAY
my.getOpenUserInfo({
  success: (res) => {
    console.log(JSON.parse(res.response).response)
  }
})
// #endif

// 获取手机号
// 模板中：
// <button open-type="getPhoneNumber" @getphonenumber="onGetPhone">手机号登录</button>
```

## 支付功能

### 微信支付

```javascript
// 微信支付流程：
// 1. 前端创建订单 → 后端生成预支付订单 → 返回支付参数
// 2. 前端调用 wx.requestPayment 发起支付

Page({
  async pay() {
    // 1. 调用后端创建订单，获取支付参数
    const res = await wx.request({
      url: 'https://api.example.com/createOrder',
      method: 'POST',
      data: { goodsId: 1, count: 2 }
    })

    const payParams = res.data

    // 2. 调起微信支付
    wx.requestPayment({
      timeStamp: payParams.timeStamp,
      nonceStr: payParams.nonceStr,
      package: payParams.package,
      signType: payParams.signType || 'MD5',
      paySign: payParams.paySign,
      success: (res) => {
        wx.showToast({ title: '支付成功' })
        // 跳转到订单详情
      },
      fail: (err) => {
        if (err.errMsg.includes('cancel')) {
          wx.showToast({ title: '已取消支付', icon: 'none' })
        } else {
          wx.showToast({ title: '支付失败', icon: 'none' })
        }
      }
    })
  }
})
```

### 支付宝支付

```javascript
// 支付宝支付流程：
// 1. 后端生成预支付订单字符串
// 2. 前端调用 my.tradePay 发起支付

Page({
  async pay() {
    // 1. 调用后端创建订单，获取支付字符串
    const res = await my.request({
      url: 'https://api.example.com/createOrder',
      method: 'POST',
      data: { goodsId: 1, count: 2 }
    })

    // 2. 调起支付宝支付
    my.tradePay({
      tradeNO: res.data.tradeNO,
      success: (res) => {
        if (res.resultCode === '9000') {
          my.showToast({ title: '支付成功' })
        } else {
          my.showToast({ title: '支付失败', icon: 'none' })
        }
      },
      fail: () => {
        my.showToast({ title: '支付取消', icon: 'none' })
      }
    })
  }
})
```

### 抖音支付

```javascript
// 抖音支付流程类似，需要签约抖音支付

Page({
  async pay() {
    const res = await tt.request({
      url: 'https://api.example.com/createOrder',
      method: 'POST',
      data: { goodsId: 1 }
    })

    tt.pay({
      orderInfo: res.data.orderInfo,
      service: 1, // 1: 抖音支付
      success: (res) => {
        if (res.code === 0) {
          tt.showToast({ title: '支付成功' })
        }
      },
      fail: (err) => {
        tt.showToast({ title: '支付失败', icon: 'none' })
      }
    })
  }
})
```

### uni-app 跨平台支付

```javascript
// uni-app 统一支付 API
async pay() {
  // 1. 调用后端获取支付参数
  const payParams = await api.createOrder({ goodsId: 1 })

  // 2. 调起支付
  // #ifdef MP-WEIXIN
  uni.requestPayment({
    provider: 'wxpay',
    timeStamp: payParams.timeStamp,
    nonceStr: payParams.nonceStr,
    package: payParams.package,
    signType: payParams.signType,
    paySign: payParams.paySign,
    success: () => uni.showToast({ title: '支付成功' }),
    fail: (err) => console.log('支付失败', err)
  })
  // #endif

  // #ifdef MP-ALIPAY
  my.tradePay({
    tradeNO: payParams.tradeNO,
    success: (res) => {
      if (res.resultCode === '9000') {
        uni.showToast({ title: '支付成功' })
      }
    }
  })
  // #endif
}
```

## 分享功能

### 微信小程序分享

```javascript
// 方式一：页面级分享配置
Page({
  onShareAppMessage() {
    return {
      title: '分享标题',
      path: '/pages/index/index?id=123',
      imageUrl: '/images/share.png'
    }
  },

  // 分享到朋友圈（微信 8.0+）
  onShareTimeline() {
    return {
      title: '分享标题',
      query: 'id=123',
      imageUrl: '/images/share.png'
    }
  }
})

// 方式二：按钮触发分享
// 模板：
<button open-type="share">分享给好友</button>
```

### 支付宝小程序分享

```javascript
// 支付宝分享需要主动调用
Page({
  onShareAppMessage() {
    return {
      title: '分享标题',
      path: '/pages/index/index?id=123'
    }
  },

  // 主动调起分享面板
  showShare() {
    my.showSharePanel()
  }
})
```

### 抖音小程序分享

```javascript
// 抖音分享
Page({
  onShareAppMessage() {
    return {
      title: '分享标题',
      path: '/pages/index/index?id=123',
      imageUrl: '/images/share.png'
    }
  }
})

// 模板：
<button open-type="share">分享</button>
```

### uni-app 跨平台分享

```javascript
// 模板：
// <button open-type="share">分享给好友</button>

// JS：
export default {
  onShareAppMessage() {
    return {
      title: '分享标题',
      path: '/pages/index/index?id=123',
      imageUrl: '/images/share.png'
    }
  }
}
```

## 地图与定位

### 微信小程序

```javascript
// 获取位置
wx.getLocation({
  type: 'gcj02',
  success: (res) => {
    console.log(res.latitude, res.longitude)
  }
})

// 打开地图导航
wx.openLocation({
  latitude: 39.9042,
  longitude: 116.4074,
  name: '目的地',
  address: '详细地址'
})

// 地图组件
// <map latitude="{{lat}}" longitude="{{lng}}" markers="{{markers}}" />
```

### 支付宝小程序

```javascript
// 获取位置
my.getLocation({
  success: (res) => {
    console.log(res.latitude, res.longitude)
  }
})

// 打开地图
my.openLocation({
  latitude: 39.9042,
  longitude: 116.4074,
  name: '目的地',
  address: '详细地址'
})
```

### uni-app 跨平台

```javascript
// 获取位置
uni.getLocation({
  type: 'gcj02',
  success: (res) => {
    console.log(res.latitude, res.longitude)
  }
})

// 打开地图
uni.openLocation({
  latitude: 39.9042,
  longitude: 116.4074,
  name: '目的地',
  address: '详细地址'
})

// 模板：
// <map :latitude="lat" :longitude="lng" :markers="markers" />
```

## 文件上传

### 各平台对照

```javascript
// 微信/QQ/百度/快手
wx.chooseImage({
  count: 1,
  success: (res) => {
    wx.uploadFile({
      url: 'https://api.example.com/upload',
      filePath: res.tempFilePaths[0],
      name: 'file',  // 注意：这里是 name
      success: (uploadRes) => {
        console.log(uploadRes.data)
      }
    })
  }
})

// 支付宝（注意 fileName 而非 name）
my.chooseImage({
  count: 1,
  success: (res) => {
    my.uploadFile({
      url: 'https://api.example.com/upload',
      filePath: res.tempFilePaths[0],
      fileName: 'file',  // 注意：支付宝用 fileName
      success: (uploadRes) => {
        console.log(uploadRes.data)
      }
    })
  }
})

// uni-app（统一接口）
uni.chooseImage({
  count: 1,
  success: (res) => {
    uni.uploadFile({
      url: 'https://api.example.com/upload',
      filePath: res.tempFilePaths[0],
      name: 'file',
      success: (uploadRes) => {
        console.log(uploadRes.data)
      }
    })
  }
})
```
