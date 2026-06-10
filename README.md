
# 资料获取  点击  [**《基于springboot+vue校园部门资料管理系统》资料**](https://nwqbsc0rm1n.feishu.cn/docx/QnFZdiPRloKSzwxY7hdc6MLUnlb)
---


## 一、项目概述

### 1\.1 项目背景

随着高校学生组织、社团数量增多，传统线下或零散表格的管理方式，存在信息分散、数据易丢失、权限不清晰、统计效率低等问题。本系统旨在通过数字化手段，为高校各学生部门/组织提供一站式的资料与事务管理平台，实现组织信息、成员、活动、财务、文档的规范化管理。

### 1\.2 技术栈

- **后端**：Spring Boot 2\.7\.x \+ MyBatis Plus 3\.5\.x \+ MySQL 8\.0

- **前端**：Vue 3 \+ Element Plus \+ Axios \+ Vue Router \+ Pinia

- **工具**：Maven、IDEA、VS Code、Postman

- **部署**：前后端分离部署，Nginx 反向代理

### 1\.3 系统功能架构

根据需求，系统分为管理员和组织管理人两种角色，核心模块如下：

```Plaintext
校园部门资料管理系统
├── 登录/注册模块
├── 个人信息管理模块
├── 组织管理人管理模块（管理员专属）
├── 学生组织管理模块
├── 组织成员管理模块
├── 活动信息管理模块
├── 财务管理模块
└── 文档管理模块
```

---
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a492ecb80d71440681584b47b08734c9.png#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/cb51153f42f34dc7847ec3be780feb6d.png#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e15fec9e45174896967047b93de7e889.png#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d84ad877b4fb4e25a9252a3f819d1d88.png#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2b55ba99fd874315b04a9badc6124b10.png#pic_center)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/88f424ffd41e4c6e9ac59520855d510c.png#pic_center)

## 二、数据库设计

### 2\.1 数据库表结构

#### 1\. 用户表（user）

存储系统所有用户信息，区分管理员和组织管理人角色。

```SQL
CREATE TABLE `user` (
  `id` int NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `username` varchar(50) NOT NULL COMMENT '用户名/账号',
  `password` varchar(100) NOT NULL COMMENT '密码（加密存储）',
  `real_name` varchar(50) DEFAULT NULL COMMENT '真实姓名',
  `gender` varchar(10) DEFAULT NULL COMMENT '性别',
  `phone` varchar(20) DEFAULT NULL COMMENT '手机号',
  `avatar` varchar(255) DEFAULT NULL COMMENT '头像地址',
  `role` varchar(20) NOT NULL COMMENT '角色：admin-管理员，org_admin-组织管理人',
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_username` (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='用户表';
```

#### 2\. 学生组织表（student\_org）

存储各学生组织/部门的基础信息。

```SQL
CREATE TABLE `student_org` (
  `id` int NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `org_name` varchar(100) NOT NULL COMMENT '组织名称',
  `activity_name` varchar(100) DEFAULT NULL COMMENT '关联活动名称',
  `org_image` varchar(255) DEFAULT NULL COMMENT '组织图片',
  `activity_time` date DEFAULT NULL COMMENT '活动时间',
  `activity_place` varchar(100) DEFAULT NULL COMMENT '活动地点',
  `participants` text COMMENT '参与人员',
  `manage_user_id` int NOT NULL COMMENT '管理人ID',
  `manage_user_name` varchar(50) NOT NULL COMMENT '管理人姓名',
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_manage_user_id` (`manage_user_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='学生组织表';
```

#### 3\. 组织成员表 \(org\_member\)

存储各学生组织的成员信息。

```SQL
CREATE TABLE `org_member` (
  `id` int NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `org_id` int NOT NULL COMMENT '所属组织ID',
  `org_name` varchar(100) NOT NULL COMMENT '组织名称',
  `member_name` varchar(50) NOT NULL COMMENT '成员姓名',
  `member_student_id` varchar(50) NOT NULL COMMENT '成员学号',
  `member_phone` varchar(20) DEFAULT NULL COMMENT '成员手机号',
  `member_role` varchar(50) DEFAULT NULL COMMENT '成员在组织中的角色',
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_org_id` (`org_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='组织成员表';
```

#### 4\. 活动信息表 \(activity\_info\)

存储各学生组织发起的活动信息。

```SQL
CREATE TABLE `activity_info` (
  `id` int NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `org_id` int NOT NULL COMMENT '所属组织ID',
  `org_name` varchar(100) NOT NULL COMMENT '组织名称',
  `activity_name` varchar(100) NOT NULL COMMENT '活动名称',
  `activity_image` varchar(255) DEFAULT NULL COMMENT '活动图片',
  `activity_time` date NOT NULL COMMENT '活动时间',
  `activity_place` varchar(100) NOT NULL COMMENT '活动地点',
  `activity_content` text COMMENT '活动内容/详情',
  `participants` text COMMENT '参与人员',
  `manage_user_id` int NOT NULL COMMENT '管理人ID',
  `manage_user_name` varchar(50) NOT NULL COMMENT '管理人姓名',
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_org_id` (`org_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='活动信息表';
```

#### 5\. 财务信息表 \(finance\_info\)

存储各学生组织的收支财务记录。

```SQL
CREATE TABLE `finance_info` (
  `id` int NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `org_id` int NOT NULL COMMENT '所属组织ID',
  `org_name` varchar(100) NOT NULL COMMENT '组织名称',
  `finance_type` varchar(50) NOT NULL COMMENT '财务类型：income-收入，expense-支出',
  `amount` decimal(10,2) NOT NULL COMMENT '金额',
  `finance_desc` varchar(255) DEFAULT NULL COMMENT '财务说明',
  `finance_date` date NOT NULL COMMENT '财务日期',
  `manage_user_id` int NOT NULL COMMENT '管理人ID',
  `manage_user_name` varchar(50) NOT NULL COMMENT '管理人姓名',
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_org_id` (`org_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='财务信息表';
```

#### 6\. 文档信息表 \(document\_info\)

存储各学生组织上传的各类文档。

```SQL
CREATE TABLE `document_info` (
  `id` int NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `org_id` int NOT NULL COMMENT '所属组织ID',
  `org_name` varchar(100) NOT NULL COMMENT '组织名称',
  `doc_name` varchar(100) NOT NULL COMMENT '文档名称',
  `doc_type` varchar(50) DEFAULT NULL COMMENT '文档类型：如会议记录、活动策划等',
  `doc_url` varchar(255) NOT NULL COMMENT '文档存储地址',
  `publish_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '发布时间',
  `manage_user_id` int NOT NULL COMMENT '管理人ID',
  `manage_user_name` varchar(50) NOT NULL COMMENT '管理人姓名',
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_org_id` (`org_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='文档信息表';
```

---

## 三、后端实现（Spring Boot）

### 3\.1 项目结构

```Plaintext
campus-management
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── campus
│   │   │           ├── CampusManagementApplication.java
│   │   │           ├── config          # 配置类（跨域、MyBatis Plus、文件上传等）
│   │   │           ├── controller      # 控制器层
│   │   │           ├── service         # 服务层
│   │   │           │   └── impl       # 服务实现类
│   │   │           ├── mapper         # DAO层
│   │   │           ├── entity         # 实体类
│   │   │           ├── dto            # 数据传输对象
│   │   │           ├── utils          # 工具类（JWT、MD5、结果封装等）
│   │   │           └── exception      # 全局异常处理
│   │   └── resources
│   │       ├── application.yml
│   │       └── mapper
│   └── test
└── pom.xml
```

### 3\.2 核心配置（application\.yml）

```YAML
server:
  port: 8080

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/campus_management?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT%2B8&useSSL=false
    username: root
    password: 123456
  servlet:
    multipart:
      max-file-size: 10MB
      max-request-size: 10MB

mybatis-plus:
  configuration:
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  mapper-locations: classpath:mapper/*.xml

# 文件上传路径
file:
  upload-path: /usr/local/campus/upload/
```

### 3\.3 核心实体类示例（User\.java）

```Java
package com.campus.entity;

import com.baomidou.mybatisplus.annotation.*;
import lombok.Data;
import java.time.LocalDateTime;

@Data
@TableName("user")
public class User {
    @TableId(type = IdType.AUTO)
    private Integer id;
    private String username;
    private String password;
    private String realName;
    private String gender;
    private String phone;
    private String avatar;
    private String role; // admin / org_admin

    @TableField(fill = FieldFill.INSERT)
    private LocalDateTime createTime;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private LocalDateTime updateTime;
}
```

### 3\.4 控制器层示例（UserController\.java）

```Java
package com.campus.controller;

import com.campus.dto.LoginDTO;
import com.campus.entity.User;
import com.campus.service.UserService;
import com.campus.utils.Result;
import org.springframework.web.bind.annotation.*;
import javax.annotation.Resource;

@RestController
@RequestMapping("/user")
public class UserController {

    @Resource
    private UserService userService;

    // 登录接口
    @PostMapping("/login")
    public Result login(@RequestBody LoginDTO loginDTO) {
        return userService.login(loginDTO);
    }

    // 注册接口（组织管理人）
    @PostMapping("/register")
    public Result register(@RequestBody User user) {
        return userService.register(user);
    }

    // 修改个人信息
    @PutMapping("/update")
    public Result updateUser(@RequestBody User user) {
        return userService.updateUser(user);
    }
}
```

### 3\.5 服务层示例（UserService\.java）

```Java
package com.campus.service;

import com.baomidou.mybatisplus.extension.service.IService;
import com.campus.dto.LoginDTO;
import com.campus.entity.User;
import com.campus.utils.Result;

public interface UserService extends IService<User> {
    Result login(LoginDTO loginDTO);
    Result register(User user);
    Result updateUser(User user);
}
```

### 3\.6 工具类示例（Result\.java）

```Java
package com.campus.utils;

import lombok.Data;

@Data
public class Result {
    private Integer code; // 响应码：200成功，500失败
    private String message;
    private Object data;

    public static Result success(Object data) {
        Result result = new Result();
        result.setCode(200);
        result.setMessage("操作成功");
        result.setData(data);
        return result;
    }

    public static Result success() {
        return success(null);
    }

    public static Result error(String message) {
        Result result = new Result();
        result.setCode(500);
        result.setMessage(message);
        return result;
    }
}
```

---

## 四、前端实现（Vue 3 \+ Element Plus）

### 4\.1 项目结构

```Plaintext
campus-front
├── public
├── src
│   ├── assets         # 静态资源
│   ├── components    # 公共组件
│   ├── views         # 页面组件
│   │   ├── Login.vue
│   │   ├── Register.vue
│   │   ├── Home.vue
│   │   ├── studentOrg
│   │   ├── orgMember
│   │   ├── activity
│   │   ├── finance
│   │   └── document
│   ├── router        # 路由配置
│   ├── store         # Pinia状态管理
│   ├── utils         # 工具类（axios请求封装）
│   ├── App.vue
│   └── main.js
├── package.json
└── vite.config.js
```

### 4\.2 路由配置（router/index\.js）

```JavaScript
import { createRouter, createWebHistory } from 'vue-router'
import Login from '../views/Login.vue'
import Home from '../views/Home.vue'
import StudentOrg from '../views/studentOrg/StudentOrg.vue'
import Activity from '../views/activity/Activity.vue'
import Finance from '../views/finance/Finance.vue'
import Document from '../views/document/Document.vue'
import AdminOrg from '../views/admin/AdminOrg.vue'
import AdminActivity from '../views/admin/AdminActivity.vue'

const routes = [
  { path: '/', redirect: '/login' },
  { path: '/login', component: Login },
  { 
    path: '/home', 
    component: Home,
    children: [
      { path: 'studentOrg', component: StudentOrg },
      { path: 'activity', component: Activity },
      { path: 'finance', component: Finance },
      { path: 'document', component: Document },
      { path: 'adminOrg', component: AdminOrg },
      { path: 'adminActivity', component: AdminActivity }
    ]
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```

### 4\.3 Axios 请求封装（utils/request\.js）

```JavaScript
import axios from 'axios'
import { ElMessage } from 'element-plus'
import router from '../router'

const request = axios.create({
  baseURL: 'http://localhost:8080',
  timeout: 5000
})

// 请求拦截器
request.interceptors.request.use(
  config => {
    const token = localStorage.getItem('token')
    if (token) {
      config.headers.Authorization = token
    }
    return config
  },
  error => {
    return Promise.reject(error)
  }
)

// 响应拦截器
request.interceptors.response.use(
  response => {
    const res = response.data
    if (res.code !== 200) {
      ElMessage.error(res.message || '请求失败')
      if (res.code === 401) {
        router.push('/login')
      }
      return Promise.reject(res)
    }
    return res
  },
  error => {
    ElMessage.error('网络异常')
    return Promise.reject(error)
  }
)

export default request
```

### 4\.4 登录页面示例（Login\.vue）

```Plaintext
<template>
  <div class="login-container">
    <el-card class="login-card">
      <h2>校园部门资料管理系统登录</h2>
      <el-form :model="loginForm" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="loginForm.username" placeholder="请输入用户名"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input v-model="loginForm.password" type="password" placeholder="请输入密码"></el-input>
        </el-form-item>
        <el-form-item label="角色">
          <el-radio-group v-model="loginForm.role">
            <el-radio label="admin">管理员</el-radio>
            <el-radio label="org_admin">组织管理人</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleLogin">登录</el-button>
          <el-button @click="goRegister">注册组织管理人</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import request from '../utils/request'
import { ElMessage } from 'element-plus'

const router = useRouter()
const loginForm = ref({
  username: '',
  password: '',
  role: 'org_admin'
})

const handleLogin = async () => {
  const res = await request.post('/user/login', loginForm.value)
  if (res.code === 200) {
    localStorage.setItem('token', res.data.token)
    localStorage.setItem('user', JSON.stringify(res.data.user))
    ElMessage.success('登录成功')
    router.push('/home')
  }
}

const goRegister = () => {
  router.push('/register')
}
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f0f2f5;
}
.login-card {
  width: 400px;
  padding: 20px;
}
h2 {
  text-align: center;
  margin-bottom: 20px;
}
</style>
```

### 4\.5 文档管理页面示例（views/document/Document\.vue）

```Plaintext
<template>
  <div class="document-container">
    <el-form :inline="true" :model="queryForm" class="query-form">
      <el-form-item label="文档名称">
        <el-input v-model="queryForm.docName" placeholder="请输入文档名称"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="getDocumentList">查询</el-button>
        <el-button type="success" @click="openAddDialog">添加</el-button>
      </el-form-item>
    </el-form>

    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="id" label="序号"></el-table-column>
      <el-table-column prop="docName" label="文档名称"></el-table-column>
      <el-table-column prop="docType" label="文档类型"></el-table-column>
      <el-table-column prop="publishTime" label="发布时间"></el-table-column>
      <el-table-column label="操作">
        <template #default="scope">
          <el-button type="primary" @click="viewDocument(scope.row)">查看</el-button>
          <el-button type="danger" @click="deleteDocument(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-dialog v-model="dialogVisible" title="添加文档" width="500px">
      <el-form :model="documentForm" label-width="80px">
        <el-form-item label="文档名称">
          <el-input v-model="documentForm.docName"></el-input>
        </el-form-item>
        <el-form-item label="文档类型">
          <el-select v-model="documentForm.docType">
            <el-option label="会议记录" value="会议记录"></el-option>
            <el-option label="活动策划" value="活动策划"></el-option>
            <el-option label="财务报表" value="财务报表"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="上传文档">
          <el-upload action="#" :auto-upload="false" :on-change="handleFileChange">
            <el-button type="primary">选择文件</el-button>
          </el-upload>
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="submitDocument">提交</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import request from '../../utils/request'
import { ElMessage } from 'element-plus'

const queryForm = ref({ docName: '' })
const tableData = ref([])
const dialogVisible = ref(false)
const documentForm = ref({
  docName: '',
  docType: '',
  docUrl: ''
})
let selectedFile = null

onMounted(() => {
  getDocumentList()
})

const getDocumentList = async () => {
  const res = await request.get('/document/list', { params: queryForm.value })
  if (res.code === 200) {
    tableData.value = res.data
  }
}

const openAddDialog = () => {
  dialogVisible.value = true
  documentForm.value = { docName: '', docType: '', docUrl: '' }
}

const handleFileChange = (file) => {
  selectedFile = file.raw
}

const submitDocument = async () => {
  const formData = new FormData()
  formData.append('file', selectedFile)
  formData.append('docName', documentForm.value.docName)
  formData.append('docType', documentForm.value.docType)

  const res = await request.post('/document/add', formData, {
    headers: { 'Content-Type': 'multipart/form-data' }
  })
  if (res.code === 200) {
    ElMessage.success('添加成功')
    dialogVisible.value = false
    getDocumentList()
  }
}

const viewDocument = (row) => {
  window.open(row.docUrl)
}

const deleteDocument = async (row) => {
  await request.delete(`/document/delete/${row.id}`)
  ElMessage.success('删除成功')
  getDocumentList()
}
</script>

<style scoped>
.query-form {
  margin-bottom: 20px;
}
</style>
```

---

## 五、系统功能实现说明

### 5\.1 角色权限控制

- **管理员角色**：可登录系统，管理所有组织管理人账号，查看、修改、删除所有学生组织、活动、财务、文档信息。

- **组织管理人角色**：注册并登录后，仅能管理自己所属组织的信息，包括成员、活动、财务、文档，无法查看其他组织的数据。

### 5\.2 核心业务流程

1. **注册登录流程**：组织管理人通过注册页面填写信息完成注册，管理员后台审核（本系统简化为直接注册），登录时通过角色区分权限。

2. **学生组织管理流程**：组织管理人添加组织信息，关联活动、图片、参与人员，支持修改、删除操作。

3. **活动信息管理流程**：组织管理人创建活动，填写活动时间、地点、内容，管理员可统一查看所有活动并进行管理。

4. **财务管理流程**：记录组织的收入和支出，支持按组织、时间查询，生成基础统计数据。

5. **文档管理流程**：上传各类文档，支持按名称、类型查询，可在线查看或下载。

### 5\.3 文件上传实现

后端配置文件上传路径，接收前端上传的图片和文档，存储到服务器本地，并将文件路径存入数据库，前端通过路径访问文件。

---

## 六、部署说明

### 6\.1 后端部署

1. 打包Spring Boot项目：`mvn clean package`，生成`campus-management.jar`。

2. 将jar包上传到服务器，运行：`java -jar campus-management.jar`。

3. 确保MySQL服务正常运行，创建数据库`campus_management`并执行SQL脚本。

### 6\.2 前端部署

1. 打包Vue项目：`npm run build`，生成`dist`文件夹。

2. 将`dist`文件夹上传到服务器，配置Nginx反向代理，将请求转发到后端服务。

3. Nginx配置示例：

```Nginx
server {
    listen 80;
    server_name localhost;

    location / {
        root /usr/local/campus/front/dist;
        index index.html;
        try_files $uri $uri/ /index.html;
    }

    location /api {
        proxy_pass http://localhost:8080;
    }
}
```

---

## 七、总结与扩展

### 7\.1 系统特点

- 角色权限清晰，数据隔离安全

- 模块化设计，各功能独立且关联

- 前后端分离架构，便于维护和扩展

- 界面简洁易用，适配高校场景需求

### 7\.2 可扩展功能

- 增加消息通知功能，活动、财务变更时推送通知

- 增加数据统计报表，生成组织活动、财务收支图表

- 增加权限更细化，支持部门管理员、普通成员等角色

- 增加文档在线预览功能，支持PDF、Word等格式预览

---

