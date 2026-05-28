# MIS 205 项目 Part 2 - 校园活动报名系统

## 项目简介
这是一个基于 Supabase 后端的校园活动报名系统，支持用户注册、登录、创建活动、报名参加活动等功能。

## 团队成员
（请在这里填写团队成员姓名和学号）

## 文件结构
```
part2/
├── spec.md           # 项目规格说明
├── README.md         # 本文档
├── index.html        # 主页面
├── css/
│   └── style.css     # 样式文件
└── js/
    └── app.js        # 应用逻辑
```

## 设置步骤

### 1. 创建 Supabase 项目
1. 访问 [supabase.com](https://supabase.com) 并注册账号
2. 创建新项目，选择 **Singapore** 区域
3. 进入项目后，获取以下信息：
   - **Project URL**: 在 Settings → API → Project URL
   - **anon public key**: 在 Settings → API → Project API keys

### 2. 配置数据库
1. 在 Supabase 控制台进入 **SQL Editor**
2. 新建查询，复制并运行 `spec.md` 中的 SQL 代码
3. 执行查询，创建表和 RLS 策略

### 3. 配置应用
1. 打开 `js/app.js`
2. 找到以下代码并替换为你的 Supabase 信息：
   ```javascript
   const SUPABASE_URL = 'YOUR_SUPABASE_URL'; // 替换为你的 Project URL
   const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY'; // 替换为你的 anon key
   ```

### 4. 启用邮箱认证
1. 在 Supabase 控制台进入 **Authentication → Providers**
2. 启用 **Email** 认证

### 5. 运行应用
直接用浏览器打开 `index.html` 即可运行应用！

## 功能说明
- **用户认证**: 注册、登录、登出
- **活动管理**: 创建、编辑、删除活动（完整 CRUD）
- **活动浏览**: 查看所有活动，显示当前报名人数
- **报名功能**: 报名和取消报名活动，已报名会显示取消按钮
- **我的活动**: 查看我组织的活动和已报名的活动
- **时间验证**: 活动时间必须在未来
- **错误处理**: 完善的错误提示和处理
- **级联删除**: 删除活动时会同时删除相关报名记录

## 演示模式
如果你暂时不想设置 Supabase，应用也包含演示模式，使用内置的示例数据就可以直接测试所有功能！

## 演示数据
- 用户：任意邮箱（演示模式）
- 包含 3 个示例活动
