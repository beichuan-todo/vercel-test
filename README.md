# 小说平台 - Vue3 + Element Plus

这是一个使用Vue3和Element Plus复刻的小说阅读平台界面。

## 功能特性

- 🎨 完全复刻原图片界面设计
- 📱 响应式布局，支持移动端
- 🎯 Vue3 Composition API
- 🎪 Element Plus UI组件库
- 🔍 搜索和筛选功能
- 📚 内容卡片网格布局
- 📄 分页功能
- 🎭 悬浮客服按钮

## 项目结构

```
├── src/
│   ├── App.vue          # 主应用组件
│   └── main.js          # 应用入口
├── index.html           # HTML模板
├── package.json         # 项目配置
├── vite.config.js       # Vite配置
└── README.md           # 项目说明
```

## 安装和运行

1. 安装依赖：
```bash
npm install
```

2. 启动开发服务器：
```bash
npm run dev
```

3. 构建生产版本：
```bash
npm run build
```

## 界面说明

### 顶部导航栏
- Logo和网站名称
- 导航菜单（首页、书库）
- 用户登录状态和头像

### 左侧边栏
- 首页作品
- 我的收藏、最近阅读等个人功能
- 创作中心

### 主内容区
- 搜索和分类筛选栏
- 内容卡片网格展示
- 分页导航

### 特色功能
- 卡片悬浮效果
- 响应式布局
- 右下角悬浮客服按钮

## 技术栈

- Vue 3.3+
- Element Plus 2.4+
- Vite 4.4+
- 现代ES6+语法

## 浏览器支持

- Chrome >= 87
- Firefox >= 78
- Safari >= 14
- Edge >= 88