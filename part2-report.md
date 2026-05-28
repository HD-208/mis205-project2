# MIS 205 项目 Part 2 报告

## 团队信息
- 学生 1：XXX 学号：XXXXXXXXXX
- 学生 2：XXX 学号：XXXXXXXXXX（如适用）
- 班级：XXXXXXXXXX

## 项目概述
校园活动报名系统 (Campus Event Registration System)，一个基于 Supabase 的 Web 应用，用于学生组织和报名校园活动。

## 数据库设计

### ER 图
```
┌─────────────┐         ┌─────────────┐         ┌─────────────────┐
│    users    │         │   events    │         │  registrations  │
├─────────────┤         ├─────────────┤         ├─────────────────┤
│ id (PK)     │◀────────│ id (PK)     │────────▶│ id (PK)         │
│ email       │  1:N    │ title       │  1:N   │ event_id (FK)   │
│ full_name   │         │ description │         │ user_id (FK)    │
│ created_at  │         │ location    │         │ registered_at   │
└─────────────┘         │ event_time  │         └─────────────────┘
                        │ max_attendees│
                        │ organizer_id│
                        └─────────────┘
```

### 表结构
- **users**: 用户信息（关联 Supabase Auth）
- **events**: 活动信息（关联组织者）
- **registrations**: 报名记录（关联用户和活动）

## 功能测试截图

### 1. 登录/注册页面
![登录页面]（此处应放截图）
用户可以注册新账号或登录。

### 2. 活动列表（首页）
![活动列表]（此处应放截图）
显示所有即将举行的活动。

### 3. 创建活动
![创建活动]（此处应放截图）
用户可以创建新活动。

### 4. 我的活动
![我的活动]（此处应放截图）
查看我组织的活动和已报名的活动。

### 5. 编辑活动
![编辑活动]（此处应放截图）
组织者可以编辑或删除活动。

## 功能验证
- ✅ Supabase Auth 用户认证
- ✅ 完整的 CRUD 操作（创建、读取、更新、删除活动）
- ✅ 报名功能
- ✅ 多表关联
- ✅ 响应式界面设计

## Supabase 设置
- 项目区域：Singapore
- 认证方式：Email
- RLS 策略：已启用，确保数据安全

## 技术栈
- 后端：Supabase (PostgreSQL + Auth)
- 前端：HTML + CSS + JavaScript (原生)

## 项目文件
- `spec.md`: 项目规格说明
- `index.html`: 主页面
- `css/style.css`: 样式文件
- `js/app.js`: 应用逻辑

## GitHub 协作（可选，用于加分）
如果使用 GitHub，这里可放仓库链接和协作记录。
