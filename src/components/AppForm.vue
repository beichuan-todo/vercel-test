<template>
  <div class="form-container">
    <!-- 顶部导航栏 -->
    <header class="header">
      <div class="header-left">
        <div class="logo">
          <div class="logo-icon">
            <el-icon><Cpu /></el-icon>
          </div>
          <span class="logo-text">智小说</span>
        </div>
        <div class="nav-tabs">
          <span class="nav-tab" @click="$emit('back')">智能体列表</span>
          <span class="nav-tab active">应用配置</span>
        </div>
      </div>
      <div class="header-right">
        <el-button type="primary" size="small" class="login-btn">
          <el-icon><User /></el-icon>
          请先登录账号
        </el-button>
        <div class="user-info">
          <el-avatar size="small" src="https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png" />
          <span>刘万千</span>
          <el-icon class="dropdown-icon"><ArrowDown /></el-icon>
        </div>
      </div>
    </header>

    <div class="main-container">
      <!-- 左侧导航 -->
      <aside class="sidebar">
        <div class="sidebar-section">
          <div class="section-title">应用配置</div>
          <div class="menu-item active">
            <el-icon><Setting /></el-icon>
            <span>基础设置</span>
          </div>
          <div class="menu-item">
            <el-icon><Key /></el-icon>
            <span>API配置</span>
          </div>
          <div class="menu-item">
            <el-icon><Shield /></el-icon>
            <span>权限管理</span>
          </div>
          <div class="menu-item">
            <el-icon><Monitor /></el-icon>
            <span>监控统计</span>
          </div>
        </div>
        
        <div class="sidebar-section">
          <div class="section-title">高级功能</div>
          <div class="menu-item">
            <el-icon><Connection /></el-icon>
            <span>数据连接</span>
          </div>
          <div class="menu-item">
            <el-icon><Notification /></el-icon>
            <span>消息推送</span>
          </div>
          <div class="menu-item">
            <el-icon><Files /></el-icon>
            <span>文件管理</span>
          </div>
        </div>
      </aside>

      <!-- 主内容区域 -->
      <main class="content">
        <!-- 面包屑导航 -->
        <div class="breadcrumb">
          <el-breadcrumb separator="/">
            <el-breadcrumb-item @click="$emit('back')" class="clickable">智能体列表</el-breadcrumb-item>
            <el-breadcrumb-item>应用配置</el-breadcrumb-item>
            <el-breadcrumb-item>基础设置</el-breadcrumb-item>
          </el-breadcrumb>
        </div>

        <!-- 表单区域 -->
        <div class="form-wrapper">
          <div class="form-header">
            <div class="form-title">
              <h2>应用基础配置</h2>
              <p>配置您的应用基本信息和运行参数</p>
            </div>
            <div class="form-actions">
              <el-button @click="$emit('back')">取消</el-button>
              <el-button type="primary" @click="handleSave" :loading="saving">
                <el-icon><Check /></el-icon>
                保存配置
              </el-button>
            </div>
          </div>

          <el-form :model="formData" :rules="formRules" ref="formRef" label-width="120px" class="app-form">
            <!-- 基础信息 -->
            <div class="form-section">
              <div class="section-header">
                <el-icon><InfoFilled /></el-icon>
                <span>基础信息</span>
              </div>
              
              <el-form-item label="应用名称" prop="appName">
                <el-input v-model="formData.appName" placeholder="请输入应用名称" />
              </el-form-item>
              
              <el-form-item label="应用描述" prop="description">
                <el-input
                  v-model="formData.description"
                  type="textarea"
                  :rows="4"
                  placeholder="请详细描述应用的功能和用途"
                />
              </el-form-item>
              
              <el-form-item label="应用类型" prop="appType">
                <el-select v-model="formData.appType" placeholder="请选择应用类型">
                  <el-option label="Web应用" value="web" />
                  <el-option label="移动应用" value="mobile" />
                  <el-option label="桌面应用" value="desktop" />
                  <el-option label="API服务" value="api" />
                </el-select>
              </el-form-item>
              
              <el-form-item label="应用版本" prop="version">
                <el-input v-model="formData.version" placeholder="如：1.0.0" />
              </el-form-item>
            </div>

            <!-- 运行配置 -->
            <div class="form-section">
              <div class="section-header">
                <el-icon><Setting /></el-icon>
                <span>运行配置</span>
              </div>
              
              <el-form-item label="运行环境" prop="environment">
                <el-radio-group v-model="formData.environment">
                  <el-radio label="development">开发环境</el-radio>
                  <el-radio label="testing">测试环境</el-radio>
                  <el-radio label="production">生产环境</el-radio>
                </el-radio-group>
              </el-form-item>
              
              <el-form-item label="端口号" prop="port">
                <el-input-number v-model="formData.port" :min="1000" :max="65535" />
              </el-form-item>
              
              <el-form-item label="最大并发数" prop="maxConnections">
                <el-input-number v-model="formData.maxConnections" :min="1" :max="10000" />
              </el-form-item>
              
              <el-form-item label="超时时间(秒)" prop="timeout">
                <el-input-number v-model="formData.timeout" :min="1" :max="300" />
              </el-form-item>
            </div>

            <!-- 数据库配置 -->
            <div class="form-section">
              <div class="section-header">
                <el-icon><Coin /></el-icon>
                <span>数据库配置</span>
              </div>
              
              <el-form-item label="数据库类型" prop="dbType">
                <el-select v-model="formData.dbType" placeholder="请选择数据库类型">
                  <el-option label="MySQL" value="mysql" />
                  <el-option label="PostgreSQL" value="postgresql" />
                  <el-option label="MongoDB" value="mongodb" />
                  <el-option label="Redis" value="redis" />
                </el-select>
              </el-form-item>
              
              <el-form-item label="数据库地址" prop="dbHost">
                <el-input v-model="formData.dbHost" placeholder="如：localhost" />
              </el-form-item>
              
              <el-form-item label="数据库端口" prop="dbPort">
                <el-input-number v-model="formData.dbPort" :min="1" :max="65535" />
              </el-form-item>
              
              <el-form-item label="数据库名称" prop="dbName">
                <el-input v-model="formData.dbName" placeholder="请输入数据库名称" />
              </el-form-item>
              
              <el-form-item label="用户名" prop="dbUsername">
                <el-input v-model="formData.dbUsername" placeholder="请输入数据库用户名" />
              </el-form-item>
              
              <el-form-item label="密码" prop="dbPassword">
                <el-input v-model="formData.dbPassword" type="password" placeholder="请输入数据库密码" show-password />
              </el-form-item>
            </div>

            <!-- 安全配置 -->
            <div class="form-section">
              <div class="section-header">
                <el-icon><Lock /></el-icon>
                <span>安全配置</span>
              </div>
              
              <el-form-item label="启用HTTPS" prop="enableHttps">
                <el-switch v-model="formData.enableHttps" />
              </el-form-item>
              
              <el-form-item label="API密钥" prop="apiKey">
                <el-input v-model="formData.apiKey" placeholder="请输入API密钥">
                  <template #append>
                    <el-button @click="generateApiKey">生成</el-button>
                  </template>
                </el-input>
              </el-form-item>
              
              <el-form-item label="允许的域名" prop="allowedDomains">
                <el-input
                  v-model="formData.allowedDomains"
                  type="textarea"
                  :rows="3"
                  placeholder="请输入允许访问的域名，每行一个"
                />
              </el-form-item>
              
              <el-form-item label="启用日志记录" prop="enableLogging">
                <el-switch v-model="formData.enableLogging" />
              </el-form-item>
            </div>
          </el-form>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { ElMessage } from 'element-plus'

// 定义事件
const emit = defineEmits(['back', 'save'])

// 表单数据
const formData = reactive({
  appName: '',
  description: '',
  appType: '',
  version: '1.0.0',
  environment: 'development',
  port: 3000,
  maxConnections: 1000,
  timeout: 30,
  dbType: '',
  dbHost: 'localhost',
  dbPort: 3306,
  dbName: '',
  dbUsername: '',
  dbPassword: '',
  enableHttps: false,
  apiKey: '',
  allowedDomains: '',
  enableLogging: true
})

// 表单验证规则
const formRules = {
  appName: [
    { required: true, message: '请输入应用名称', trigger: 'blur' },
    { min: 2, max: 50, message: '应用名称长度在 2 到 50 个字符', trigger: 'blur' }
  ],
  description: [
    { required: true, message: '请输入应用描述', trigger: 'blur' },
    { min: 10, max: 500, message: '应用描述长度在 10 到 500 个字符', trigger: 'blur' }
  ],
  appType: [
    { required: true, message: '请选择应用类型', trigger: 'change' }
  ],
  version: [
    { required: true, message: '请输入应用版本', trigger: 'blur' },
    { pattern: /^\d+\.\d+\.\d+$/, message: '版本号格式不正确，如：1.0.0', trigger: 'blur' }
  ],
  dbType: [
    { required: true, message: '请选择数据库类型', trigger: 'change' }
  ],
  dbHost: [
    { required: true, message: '请输入数据库地址', trigger: 'blur' }
  ],
  dbName: [
    { required: true, message: '请输入数据库名称', trigger: 'blur' }
  ],
  dbUsername: [
    { required: true, message: '请输入数据库用户名', trigger: 'blur' }
  ],
  dbPassword: [
    { required: true, message: '请输入数据库密码', trigger: 'blur' }
  ]
}

const formRef = ref()
const saving = ref(false)

// 生成API密钥
const generateApiKey = () => {
  const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'
  let result = ''
  for (let i = 0; i < 32; i++) {
    result += chars.charAt(Math.floor(Math.random() * chars.length))
  }
  formData.apiKey = result
  ElMessage.success('API密钥已生成')
}

// 保存配置
const handleSave = async () => {
  if (!formRef.value) return
  
  try {
    await formRef.value.validate()
    saving.value = true
    
    // 模拟保存过程
    await new Promise(resolve => setTimeout(resolve, 2000))
    
    ElMessage.success('配置保存成功！')
    emit('save', formData)
  } catch (error) {
    ElMessage.error('请检查表单填写是否正确')
  } finally {
    saving.value = false
  }
}
</script>

<style scoped>
.form-container {
  height: 100vh;
  display: flex;
  flex-direction: column;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  background-attachment: fixed;
}

.header {
  height: 70px;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-bottom: 1px solid rgba(228, 231, 237, 0.3);
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 30px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  position: relative;
  z-index: 100;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 50px;
}

.logo {
  display: flex;
  align-items: center;
  gap: 12px;
  font-size: 20px;
  font-weight: 700;
  color: #667eea;
}

.logo-icon {
  width: 40px;
  height: 40px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 20px;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

.nav-tabs {
  display: flex;
  gap: 8px;
}

.nav-tab {
  padding: 10px 20px;
  cursor: pointer;
  border-radius: 25px;
  transition: all 0.3s ease;
  font-weight: 500;
  position: relative;
  overflow: hidden;
}

.nav-tab.active {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

.nav-tab:not(.active):hover {
  background: rgba(102, 126, 234, 0.1);
  color: #667eea;
}

.header-right {
  display: flex;
  align-items: center;
  gap: 20px;
}

.login-btn {
  border-radius: 20px;
  padding: 8px 20px;
  font-weight: 500;
  box-shadow: 0 4px 15px rgba(64, 158, 255, 0.3);
  transition: all 0.3s ease;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  padding: 8px 16px;
  border-radius: 20px;
  transition: all 0.3s ease;
}

.user-info:hover {
  background: rgba(102, 126, 234, 0.1);
}

.main-container {
  flex: 1;
  display: flex;
  overflow: hidden;
  margin: 20px;
  border-radius: 20px;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}

.sidebar {
  width: 260px;
  background: rgba(255, 255, 255, 0.8);
  border-right: 1px solid rgba(228, 231, 237, 0.3);
  padding: 30px 0;
  overflow-y: auto;
  border-radius: 20px 0 0 20px;
}

.sidebar-section {
  margin-bottom: 35px;
}

.section-title {
  font-size: 13px;
  color: #8b92a5;
  margin: 0 25px 15px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.menu-item {
  display: flex;
  align-items: center;
  gap: 15px;
  padding: 15px 25px;
  cursor: pointer;
  transition: all 0.3s ease;
  color: #606266;
  margin: 0 15px;
  border-radius: 12px;
  font-weight: 500;
  position: relative;
}

.menu-item:hover {
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.1) 0%, rgba(118, 75, 162, 0.1) 100%);
  color: #667eea;
  transform: translateX(5px);
}

.menu-item.active {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

.content {
  flex: 1;
  padding: 30px;
  overflow-y: auto;
  background: rgba(255, 255, 255, 0.5);
  border-radius: 0 20px 20px 0;
}

.breadcrumb {
  margin-bottom: 30px;
}

.breadcrumb .clickable {
  cursor: pointer;
  color: #667eea;
}

.breadcrumb .clickable:hover {
  text-decoration: underline;
}

.form-wrapper {
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  overflow: hidden;
}

.form-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30px 40px;
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.05) 0%, rgba(118, 75, 162, 0.05) 100%);
  border-bottom: 1px solid rgba(228, 231, 237, 0.3);
}

.form-title h2 {
  margin: 0 0 8px 0;
  color: #2c3e50;
  font-size: 24px;
  font-weight: 600;
}

.form-title p {
  margin: 0;
  color: #7f8c8d;
  font-size: 14px;
}

.form-actions {
  display: flex;
  gap: 12px;
}

.app-form {
  padding: 40px;
}

.form-section {
  margin-bottom: 40px;
  padding: 30px;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 16px;
  border: 1px solid rgba(228, 231, 237, 0.3);
}

.section-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 25px;
  padding-bottom: 15px;
  border-bottom: 2px solid rgba(102, 126, 234, 0.1);
  font-size: 16px;
  font-weight: 600;
  color: #667eea;
}

.app-form :deep(.el-form-item) {
  margin-bottom: 25px;
}

.app-form :deep(.el-form-item__label) {
  font-weight: 500;
  color: #2c3e50;
}

.app-form :deep(.el-input),
.app-form :deep(.el-select),
.app-form :deep(.el-textarea) {
  border-radius: 8px;
}

.app-form :deep(.el-input__wrapper) {
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.app-form :deep(.el-input__wrapper:hover) {
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.15);
}

.app-form :deep(.el-input__wrapper.is-focus) {
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.25);
}

.app-form :deep(.el-button--primary) {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
  border-radius: 8px;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
  transition: all 0.3s ease;
}

.app-form :deep(.el-button--primary:hover) {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(102, 126, 234, 0.4);
}

.app-form :deep(.el-switch.is-checked .el-switch__core) {
  background-color: #667eea;
}

.app-form :deep(.el-radio.is-checked .el-radio__inner) {
  background-color: #667eea;
  border-color: #667eea;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .main-container {
    flex-direction: column;
    margin: 10px;
  }
  
  .sidebar {
    width: 100%;
    height: auto;
    border-right: none;
    border-bottom: 1px solid rgba(228, 231, 237, 0.3);
    border-radius: 15px 15px 0 0;
  }
  
  .content {
    border-radius: 0 0 15px 15px;
    padding: 20px;
  }
  
  .form-header {
    flex-direction: column;
    gap: 20px;
    align-items: flex-start;
    padding: 20px;
  }
  
  .app-form {
    padding: 20px;
  }
  
  .form-section {
    padding: 20px;
  }
}
</style>