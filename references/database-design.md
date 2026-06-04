# 数据库设计指导

> **⚠️ 免责声明**：本文档提供通用的数据库设计指导，具体表结构和字段设计应根据实际业务需求调整。数据库选型和最佳实践请参考各数据库官方文档。

## 数据库选型

| 数据库 | 适用场景 | 说明 |
|--------|---------|------|
| **MySQL** | 关系型数据，如用户、订单、商品 | 最常用，生态成熟，推荐中小项目首选 |
| **PostgreSQL** | 复杂查询、JSON 数据、地理数据 | 功能更强大，适合复杂业务 |
| **MongoDB** | 文档型数据，灵活 schema | 适合内容管理、日志等非结构化数据 |
| **SQLite** | 本地开发、小型项目 | 无需安装服务器，适合原型阶段 |
| **云数据库** | 云开发场景 | 各平台云开发自带数据库，如微信云开发的云数据库 |

## 通用表设计原则

1. **每个表必须有主键**：推荐使用自增 ID 或 UUID
2. **时间字段**：每张表建议有 `created_at` 和 `updated_at` 字段
3. **软删除**：重要数据使用 `is_deleted` 或 `deleted_at` 字段，不直接删除
4. **索引**：常用查询字段加索引，但不要过度索引
5. **外键**：明确表之间的关联关系

## 小程序常见表结构

### 用户表（users）

```sql
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  openid VARCHAR(64) UNIQUE COMMENT '微信/支付宝等平台的 openid',
  unionid VARCHAR(64) COMMENT '微信开放平台 unionid',
  phone VARCHAR(20) UNIQUE COMMENT '手机号',
  nickname VARCHAR(50) COMMENT '昵称',
  avatar VARCHAR(255) COMMENT '头像 URL',
  password_hash VARCHAR(255) COMMENT '密码哈希（账密登录时使用）',
  status TINYINT DEFAULT 1 COMMENT '状态：0-禁用，1-正常',
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  INDEX idx_openid (openid),
  INDEX idx_phone (phone)
);
```

### 地址表（addresses）

```sql
CREATE TABLE addresses (
  id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT NOT NULL COMMENT '用户 ID',
  name VARCHAR(50) NOT NULL COMMENT '收货人姓名',
  phone VARCHAR(20) NOT NULL COMMENT '联系电话',
  province VARCHAR(20) COMMENT '省',
  city VARCHAR(20) COMMENT '市',
  district VARCHAR(20) COMMENT '区',
  address VARCHAR(255) NOT NULL COMMENT '详细地址',
  latitude DECIMAL(10, 6) COMMENT '纬度',
  longitude DECIMAL(10, 6) COMMENT '经度',
  is_default TINYINT DEFAULT 0 COMMENT '是否默认地址',
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  INDEX idx_user_id (user_id),
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### 订单表（orders）

```sql
CREATE TABLE orders (
  id INT PRIMARY KEY AUTO_INCREMENT,
  order_no VARCHAR(64) UNIQUE NOT NULL COMMENT '订单编号',
  user_id INT NOT NULL COMMENT '用户 ID',
  status TINYINT DEFAULT 0 COMMENT '状态：0-待支付，1-已支付，2-已完成，3-已取消，4-已退款',
  total_amount DECIMAL(10, 2) NOT NULL COMMENT '总金额',
  pay_amount DECIMAL(10, 2) COMMENT '实付金额',
  pay_method VARCHAR(20) COMMENT '支付方式：wechat/alipay',
  pay_time DATETIME COMMENT '支付时间',
  address_id INT COMMENT '收货地址 ID',
  remark VARCHAR(255) COMMENT '备注',
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  INDEX idx_user_id (user_id),
  INDEX idx_order_no (order_no),
  INDEX idx_status (status),
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### 订单明细表（order_items）

```sql
CREATE TABLE order_items (
  id INT PRIMARY KEY AUTO_INCREMENT,
  order_id INT NOT NULL COMMENT '订单 ID',
  product_id INT NOT NULL COMMENT '商品 ID',
  product_name VARCHAR(100) NOT NULL COMMENT '商品名称（冗余，防止商品修改后订单显示异常）',
  product_image VARCHAR(255) COMMENT '商品图片',
  price DECIMAL(10, 2) NOT NULL COMMENT '单价',
  quantity INT NOT NULL DEFAULT 1 COMMENT '数量',
  subtotal DECIMAL(10, 2) NOT NULL COMMENT '小计',
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  INDEX idx_order_id (order_id),
  FOREIGN KEY (order_id) REFERENCES orders(id)
);
```

### 商品表（products）

```sql
CREATE TABLE products (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100) NOT NULL COMMENT '商品名称',
  description TEXT COMMENT '商品描述',
  price DECIMAL(10, 2) NOT NULL COMMENT '价格',
  original_price DECIMAL(10, 2) COMMENT '原价（用于显示划线价）',
  image VARCHAR(255) COMMENT '主图',
  images TEXT COMMENT '图片列表（JSON 数组）',
  category_id INT COMMENT '分类 ID',
  stock INT DEFAULT 0 COMMENT '库存',
  sales INT DEFAULT 0 COMMENT '销量',
  status TINYINT DEFAULT 1 COMMENT '状态：0-下架，1-上架',
  sort_order INT DEFAULT 0 COMMENT '排序',
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  INDEX idx_category (category_id),
  INDEX idx_status (status)
);
```

### 分类表（categories）

```sql
CREATE TABLE categories (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(50) NOT NULL COMMENT '分类名称',
  icon VARCHAR(255) COMMENT '图标',
  parent_id INT DEFAULT 0 COMMENT '父分类 ID，0 为顶级',
  sort_order INT DEFAULT 0 COMMENT '排序',
  status TINYINT DEFAULT 1 COMMENT '状态：0-禁用，1-正常',
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  INDEX idx_parent (parent_id)
);
```

### 购物车表（cart_items）

```sql
CREATE TABLE cart_items (
  id INT PRIMARY KEY AUTO_INCREMENT,
  user_id INT NOT NULL COMMENT '用户 ID',
  product_id INT NOT NULL COMMENT '商品 ID',
  quantity INT NOT NULL DEFAULT 1 COMMENT '数量',
  selected TINYINT DEFAULT 1 COMMENT '是否选中',
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  UNIQUE KEY uk_user_product (user_id, product_id),
  FOREIGN KEY (user_id) REFERENCES users(id),
  FOREIGN KEY (product_id) REFERENCES products(id)
);
```

## 按业务类型的表设计建议

### 外卖/电商类
- 必须：users、products、categories、orders、order_items、cart_items、addresses
- 可选：coupons（优惠券）、reviews（评价）、shops（店铺）

### 电影购票类
- 必须：users、movies、cinemas、showtimes、orders、order_items、seats
- 可选：reviews（评价）、favorites（收藏）

### 社交/内容类
- 必须：users、posts、comments、likes
- 可选：follows（关注）、messages（私信）、notifications（通知）

### 工具类
- 必须：users
- 可选：根据具体功能设计，如 records（使用记录）、settings（用户设置）

## 字段设计规范

### 金额字段
- 使用 `DECIMAL(10, 2)` 类型，不要用 `FLOAT`（精度问题）
- 存储单位为"元"，如 99.99

### 状态字段
- 使用 `TINYINT` 类型
- 在代码中定义常量或枚举，不要硬编码数字

### 时间字段
- 使用 `DATETIME` 类型
- 默认值设为 `CURRENT_TIMESTAMP`
- 更新时间设为 `ON UPDATE CURRENT_TIMESTAMP`

### 文本字段
- 短文本用 `VARCHAR(N)`，N 为最大长度
- 长文本用 `TEXT`
- 不要用 `CHAR`（会补空格）

## 数据库初始化脚本示例

```sql
-- 创建数据库
CREATE DATABASE IF NOT EXISTS miniprogram_db DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

USE miniprogram_db;

-- 执行上述建表语句
-- ...

-- 插入初始分类数据
INSERT INTO categories (name, sort_order) VALUES
('推荐', 1),
('热销', 2),
('新品', 3);
```

## 常见问题

**Q：要不要用外键？**
A：小型项目可以用外键保证数据一致性；中大型项目通常在应用层做关联查询，不依赖数据库外键，以提高灵活性。

**Q：数据量大了怎么办？**
A：初期不用过度设计。当单表数据超过百万时，再考虑分表、读写分离、缓存等优化。

**Q：要不要用 ORM？**
A：推荐使用。Node.js 可用 Sequelize、TypeORM、Prisma；Python 可用 SQLAlchemy、Django ORM。ORM 能减少 SQL 注入风险，提高开发效率。
