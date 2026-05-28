# MIS 205 项目 Part 2：校园活动报名系统 (Campus Event Registration System)

## 项目概述
一个帮助校园学生组织和参加活动的 Web 应用程序。目标用户是高校学生和社团组织者。

## 团队成员
- 学生 1：XXX 学号：XXXXXXXXXX
- 学生 2：XXX 学号：XXXXXXXXXX（如适用）

## 实体设计

### 1. `school_personnel`（学校人员表）- 预定义的学校人员信息
- `id` (SERIAL, PK) - 记录 ID
- `full_name` (VARCHAR, NOT NULL) - 人员姓名
- `student_employee_id` (VARCHAR, NOT NULL, UNIQUE) - 学号/工号（12开头的8位数字）
- `email` (VARCHAR, NOT NULL, UNIQUE) - 南科大邮箱 (@sustech.edu.cn 或 @mail.sustech.edu.cn)
- `department` (VARCHAR) - 所属院系
- `college_system` (VARCHAR) - 所属书院（如适用）
- `role` (VARCHAR) - 身份（学生/教职工）
- `created_at` (TIMESTAMP) - 记录创建时间

### 2. `users`（用户表）- 系统注册用户
- `id` (UUID, PK) - Supabase Auth 生成的用户 ID
- `email` (VARCHAR) - 用户邮箱
- `full_name` (VARCHAR) - 用户全名
- `personnel_id` (INTEGER, FK → school_personnel.id) - 关联的学校人员记录 ID
- `created_at` (TIMESTAMP) - 账户创建时间

### 3. `events`（活动表）
- `id` (SERIAL, PK) - 活动 ID
- `title` (VARCHAR, NOT NULL) - 活动标题
- `description` (TEXT) - 活动描述
- `location` (VARCHAR) - 活动地点
- `event_time` (TIMESTAMP, NOT NULL) - 活动时间
- `max_attendees` (INTEGER) - 最大参与人数
- `organizer_id` (UUID, FK → users.id) - 组织者 ID
- `created_at` (TIMESTAMP) - 创建时间

### 3. `registrations`（报名表）
- `id` (SERIAL, PK) - 报名 ID
- `event_id` (INTEGER, FK → events.id) - 活动 ID
- `user_id` (UUID, FK → users.id) - 报名用户 ID
- `registered_at` (TIMESTAMP) - 报名时间
- UNIQUE(event_id, user_id) - 防止重复报名

## 实体关系
- **school_personnel 1:1 users** - 一个学校人员可以注册一个系统账户
- **users 1:N events** - 一个用户可以组织多个活动
- **users 1:N registrations** - 一个用户可以报名多个活动
- **events 1:N registrations** - 一个活动可以有多个报名记录

## 用户流程
1. **用户注册验证** → 系统验证用户的姓名、学号/工号、邮箱是否在学校人员数据库中
2. **用户注册/登录** → 通过验证的用户使用邮箱注册或登录系统
2. **浏览活动列表** → 用户查看所有即将举行的活动
3. **创建活动** → 已登录用户创建新活动（作为组织者）
4. **报名参加活动** → 用户报名参加感兴趣的活动
5. **管理活动** → 组织者可以编辑或删除自己组织的活动
6. **查看已报名活动** → 用户查看自己已报名的活动列表

## 数据库架构 SQL

```sql
-- 学校人员表 - 预定义的学校人员信息
CREATE TABLE school_personnel (
    id SERIAL PRIMARY KEY,
    full_name VARCHAR(255) NOT NULL,
    student_employee_id VARCHAR(20) NOT NULL UNIQUE,
    email VARCHAR(255) NOT NULL UNIQUE,
    department VARCHAR(255),
    college_system VARCHAR(255),
    role VARCHAR(50) DEFAULT '学生',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 插入示例学校人员数据
INSERT INTO school_personnel (full_name, student_employee_id, email, department, college_system, role) VALUES
('田野', '12411802', '12411802@mail.sustech.edu.cn', '计算机科学与工程系', '致仁书院', '学生'),
('蓝浩轩', '12411721', '12411721@mail.sustech.edu.cn', '计算机科学与工程系', '树德书院', '学生'),
('黄鼎', '12411818', '12411818@mail.sustech.edu.cn', '计算机科学与工程系', '树仁书院', '学生'),
('张三', '12400001', '12400001@mail.sustech.edu.cn', '电子与电气工程系', '致诚书院', '学生'),
('李四', '12400002', '12400002@mail.sustech.edu.cn', '理学院', '树礼书院', '学生'),
('王五', '12400003', '12400003@mail.sustech.edu.cn', '商学院', '致新书院', '学生'),
('刘教授', '12000001', '12000001@mail.sustech.edu.cn', '计算机科学与工程系', NULL, '教职工'),
('周老师', '12000002', '12000002@mail.sustech.edu.cn', '学生工作部', NULL, '教职工');

-- 用户表（对应 Supabase Auth）
CREATE TABLE users (
    id UUID REFERENCES auth.users(id) PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    full_name VARCHAR(255),
    personnel_id INTEGER REFERENCES school_personnel(id),
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 活动表
CREATE TABLE events (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    location VARCHAR(255),
    event_time TIMESTAMPTZ NOT NULL,
    max_attendees INTEGER,
    organizer_id UUID REFERENCES users(id) NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 报名表
CREATE TABLE registrations (
    id SERIAL PRIMARY KEY,
    event_id INTEGER REFERENCES events(id) NOT NULL,
    user_id UUID REFERENCES users(id) NOT NULL,
    registered_at TIMESTAMPTZ DEFAULT NOW(),
    UNIQUE(event_id, user_id)
);

-- 启用行级安全 (RLS)
ALTER TABLE school_personnel ENABLE ROW LEVEL SECURITY;
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE events ENABLE ROW LEVEL SECURITY;
ALTER TABLE registrations ENABLE ROW LEVEL SECURITY;

-- RLS 策略
CREATE POLICY "School personnel are viewable by everyone" ON school_personnel FOR SELECT USING (true);
CREATE POLICY "Only admins can modify school personnel" ON school_personnel FOR ALL USING (false);

CREATE POLICY "Users can view all profiles" ON users FOR SELECT USING (true);
CREATE POLICY "Users can update their own profile" ON users FOR UPDATE USING (auth.uid() = id);
CREATE POLICY "Users can insert their own profile" ON users FOR INSERT WITH CHECK (auth.uid() = id);

CREATE POLICY "Events are viewable by everyone" ON events FOR SELECT USING (true);
CREATE POLICY "Organizers can create events" ON events FOR INSERT WITH CHECK (auth.uid() = organizer_id);
CREATE POLICY "Organizers can update their own events" ON events FOR UPDATE USING (auth.uid() = organizer_id);
CREATE POLICY "Organizers can delete their own events" ON events FOR DELETE USING (auth.uid() = organizer_id);

CREATE POLICY "Registrations are viewable by everyone" ON registrations FOR SELECT USING (true);
CREATE POLICY "Users can register themselves" ON registrations FOR INSERT WITH CHECK (auth.uid() = user_id);
CREATE POLICY "Users can cancel their own registrations" ON registrations FOR DELETE USING (auth.uid() = user_id);
```

## 功能需求
- ✅ 用户注册/登录（使用 Supabase Auth）
- ✅ 活动列表浏览（Read）
- ✅ 创建新活动（Create）
- ✅ 编辑活动（Update）
- ✅ 删除活动（Delete）
- ✅ 报名参加活动（Create registration）
- ✅ 取消报名（Delete registration）

## 技术栈
- 后端：Supabase（PostgreSQL + Auth）
- 前端：HTML + CSS + JavaScript（原生，简单易用）
