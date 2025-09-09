<template>
  <!-- 主列表页面 -->
  <div v-if="currentPageView === 'list'" class="app-container">
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
          <span class="nav-tab active">智能体列表</span>
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
          <div class="section-title">创作中心</div>
          <div class="menu-item active">
            <el-icon><Robot /></el-icon>
            <span>智能体</span>
          </div>
        </div>
        
        <div class="sidebar-section">
          <div class="section-title">我的</div>
          <div class="menu-item">
            <el-icon><Star /></el-icon>
            <span>我的收藏</span>
          </div>
          <div class="menu-item">
            <el-icon><Document /></el-icon>
            <span>我的作品</span>
          </div>
          <div class="menu-item">
            <el-icon><User /></el-icon>
            <span>我的账户</span>
          </div>
        </div>

        <div class="sidebar-section">
          <div class="section-title">创作</div>
          <div class="menu-item">
            <el-icon><Plus /></el-icon>
            <span>从0开始</span>
          </div>
          <div class="menu-item">
            <el-icon><Upload /></el-icon>
            <span>导入(TXT)</span>
          </div>
          <div class="menu-item">
            <el-icon><Edit /></el-icon>
            <span>续写作品</span>
          </div>
          <div class="menu-item">
            <el-icon><Magic /></el-icon>
            <span>智能续写</span>
          </div>
          <div class="menu-item">
            <el-icon><Brush /></el-icon>
            <span>智能改写</span>
          </div>
        </div>
      </aside>

      <!-- 主内容区域 -->
      <main class="content">
        <!-- 搜索和筛选栏 -->
        <div class="search-bar">
          <div class="search-left">
            <el-button type="primary" size="small">全部</el-button>
            <el-button size="small">办公</el-button>
            <el-button size="small">生活</el-button>
            <el-button size="small">图片</el-button>
            <el-button size="small">写作更多</el-button>
          </div>
          <div class="search-right">
            <el-input
              v-model="searchText"
              placeholder="搜索智能体或者功能"
              size="small"
              style="width: 300px"
            >
              <template #suffix>
                <el-icon><Search /></el-icon>
              </template>
            </el-input>
          </div>
        </div>

        <!-- 智能体卡片列表 -->
        <div class="agent-grid">
          <!-- 创建应用卡片 -->
          <div class="create-card" @click="showCreateDialog = true">
            <div class="create-icon">
              <el-icon><Plus /></el-icon>
            </div>
            <h3 class="create-title">创建应用</h3>
            <p class="create-description">点击创建新的智能体应用</p>
          </div>
          
          <div v-for="agent in agentList" :key="agent.id" class="agent-card" :class="{ 'app-card-clickable': agent.type === 'app', 'agent-card-clickable': agent.type === 'agent' }" @click="handleCardClick(agent)">
            <div class="card-header">
              <div class="agent-avatar">
                <div class="avatar-bg" :style="{ background: agent.avatarBg }">
                  <el-icon class="avatar-icon"><Robot /></el-icon>
                </div>
              </div>
              <div class="agent-info">
                <div class="agent-title-row">
                  <h3 class="agent-name">{{ agent.name }}</h3>
                  <span class="type-tag" :class="agent.type || 'agent'">
                    {{ (agent.type || 'agent') === 'agent' ? '智能体' : '应用' }}
                  </span>
                </div>
                <p class="agent-author">
                  <el-icon><User /></el-icon>
                  {{ agent.author }} · {{ agent.updateTime }}
                </p>
              </div>
            </div>
            <div class="card-content">
              <p class="agent-description">{{ agent.description }}</p>
            </div>
            <div class="card-footer">
              <div class="card-actions-container">
                <div class="card-actions-left">
                  <el-button size="small" text class="action-btn" @click.stop="openEditDialog(agent)">
                    <el-icon><Setting /></el-icon>
                    设置
                  </el-button>
                </div>
                <div class="card-actions-right">
                  <el-dropdown trigger="click" @command="handleCommand">
                    <el-button size="small" text class="action-btn">
                      <el-icon><MoreFilled /></el-icon>
                      更多
                    </el-button>
                    <template #dropdown>
                      <el-dropdown-menu>
                        <el-dropdown-item :command="`stats-${agent.id}`">
                          <el-icon><View /></el-icon>
                          统计 ({{ agent.views }})
                        </el-dropdown-item>
                        <el-dropdown-item :command="`delete-${agent.id}`" divided>
                          <el-icon><Delete /></el-icon>
                          删除
                        </el-dropdown-item>
                      </el-dropdown-menu>
                    </template>
                  </el-dropdown>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- 分页 -->
        <div class="pagination-container">
          <el-pagination
            v-model:current-page="currentPage"
            :page-size="pageSize"
            :total="total"
            layout="prev, pager, next, jumper"
            @current-change="handlePageChange"
          />
        </div>
      </main>
    </div>

    <!-- 创建应用弹窗 -->
    <el-dialog
      v-model="showCreateDialog"
      title="创建应用"
      width="600px"
      :before-close="handleCloseDialog"
    >
      <el-form :model="createForm" :rules="createRules" ref="createFormRef" label-width="100px">
        <div class="type-cards">
          <div 
            class="type-card agent-card" 
            :class="{ active: createForm.type === 'agent' }"
            @click="createForm.type = 'agent'"
          >
            <div class="type-icon">
              <svg viewBox="0 0 24 24" class="custom-icon">
                <path d="M12 2C13.1 2 14 2.9 14 4C14 5.1 13.1 6 12 6C10.9 6 10 5.1 10 4C10 2.9 10.9 2 12 2ZM21 9V7L15 1H5C3.89 1 3 1.89 3 3V21C3 22.11 3.89 23 5 23H11V21H5V3H13V9H21ZM14 10V12H16V10H14ZM16 13H14V15H16V13ZM20.5 18.08L19.42 19.16L18.5 18.24L19.58 17.16L20.5 18.08ZM22 20.5L21.5 21L20.5 20L21 19.5L22 20.5ZM18.5 21.92L17.42 20.84L18.34 19.92L19.42 21L18.5 21.92Z" fill="currentColor"/>
              </svg>
            </div>
            <div class="type-content">
              <div class="type-title">智能体</div>
              <div class="type-desc">AI驱动的智能助手</div>
            </div>
          </div>
          <div 
            class="type-card app-card" 
            :class="{ active: createForm.type === 'app' }"
            @click="createForm.type = 'app'"
          >
            <div class="type-icon">
              <svg viewBox="0 0 24 24" class="custom-icon">
                <path d="M4 6H20V16H4M20 18C21.11 18 22 17.11 22 16V6C22 4.89 21.11 4 20 4H4C2.89 4 2 4.89 2 6V16C2 17.11 2.89 18 4 18H0V20H24V18H20Z" fill="currentColor"/>
                <circle cx="7" cy="11" r="1" fill="currentColor"/>
                <circle cx="12" cy="11" r="1" fill="currentColor"/>
                <circle cx="17" cy="11" r="1" fill="currentColor"/>
              </svg>
            </div>
            <div class="type-content">
              <div class="type-title">应用</div>
              <div class="type-desc">传统业务应用</div>
            </div>
          </div>
        </div>
        
        <el-form-item label="应用名称" prop="name">
          <el-input v-model="createForm.name" placeholder="请输入应用名称" />
        </el-form-item>
        
        <el-form-item label="应用描述" prop="description">
          <el-input
            v-model="createForm.description"
            type="textarea"
            :rows="3"
            placeholder="请输入应用描述"
          />
        </el-form-item>
        
        <el-form-item label="所属公司" prop="company">
          <el-select v-model="createForm.company" placeholder="请选择公司" style="width: 100%">
            <el-option label="阿里巴巴集团" value="alibaba" />
            <el-option label="腾讯科技" value="tencent" />
            <el-option label="百度公司" value="baidu" />
            <el-option label="字节跳动" value="bytedance" />
            <el-option label="华为技术" value="huawei" />
            <el-option label="其他公司" value="other" />
          </el-select>
        </el-form-item>
        
        <el-form-item label="所属部门" prop="department">
          <el-select v-model="createForm.department" placeholder="请选择部门" style="width: 100%">
            <el-option label="技术研发部" value="rd" />
            <el-option label="产品设计部" value="product" />
            <el-option label="市场营销部" value="marketing" />
            <el-option label="人力资源部" value="hr" />
            <el-option label="财务管理部" value="finance" />
            <el-option label="运营管理部" value="operations" />
            <el-option label="客户服务部" value="service" />
            <el-option label="其他部门" value="other" />
          </el-select>
        </el-form-item>
        
        <el-form-item label="权限设置" prop="permissions">
          <el-select v-model="createForm.permissions" placeholder="请选择权限范围" style="width: 100%">
            <el-option label="本部门" value="department" />
            <el-option label="本公司" value="company" />
            <el-option label="跨公司本部门" value="cross-company-department" />
            <el-option label="指定角色" value="specific-role" />
          </el-select>
        </el-form-item>
        
        <el-form-item v-if="createForm.type === 'app'" label="跳转地址" prop="redirectUrl">
          <el-input v-model="createForm.redirectUrl" placeholder="请输入跳转地址（可选）" />
        </el-form-item>
        
        <el-form-item label="展示形式" prop="displayType">
          <el-radio-group v-model="createForm.displayType">
            <el-radio label="mobile">手机端</el-radio>
            <el-radio label="desktop">电脑端</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
      
      <template #footer>
        <div class="dialog-footer">
          <el-button @click="showCreateDialog = false">取消</el-button>
          <el-button type="primary" @click="handleCreateApp">创建</el-button>
        </div>
      </template>
    </el-dialog>

    <!-- 编辑应用弹窗 -->
    <el-dialog
      v-model="showEditDialog"
      title="编辑应用"
      width="600px"
      :before-close="handleCloseDialog"
    >
      <el-form :model="editForm" :rules="createRules" ref="editFormRef" label-width="100px">
        <div class="type-cards readonly">
          <div 
            class="type-card agent-card" 
            :class="{ active: editForm.type === 'agent' }"
          >
            <div class="type-icon">
              <svg viewBox="0 0 24 24" class="custom-icon">
                <path d="M12 2C13.1 2 14 2.9 14 4C14 5.1 13.1 6 12 6C10.9 6 10 5.1 10 4C10 2.9 10.9 2 12 2ZM21 9V7L15 1H5C3.89 1 3 1.89 3 3V21C3 22.11 3.89 23 5 23H11V21H5V3H13V9H21ZM14 10V12H16V10H14ZM16 13H14V15H16V13ZM20.5 18.08L19.42 19.16L18.5 18.24L19.58 17.16L20.5 18.08ZM22 20.5L21.5 21L20.5 20L21 19.5L22 20.5ZM18.5 21.92L17.42 20.84L18.34 19.92L19.42 21L18.5 21.92Z" fill="currentColor"/>
              </svg>
            </div>
            <div class="type-content">
              <div class="type-title">智能体</div>
              <div class="type-desc">AI驱动的智能助手</div>
            </div>
          </div>
          <div 
            class="type-card app-card" 
            :class="{ active: editForm.type === 'app' }"
          >
            <div class="type-icon">
              <svg viewBox="0 0 24 24" class="custom-icon">
                <path d="M4 6H20V16H4M20 18C21.11 18 22 17.11 22 16V6C22 4.89 21.11 4 20 4H4C2.89 4 2 4.89 2 6V16C2 17.11 2.89 18 4 18H0V20H24V20H20Z" fill="currentColor"/>
                <circle cx="7" cy="11" r="1" fill="currentColor"/>
                <circle cx="12" cy="11" r="1" fill="currentColor"/>
                <circle cx="17" cy="11" r="1" fill="currentColor"/>
              </svg>
            </div>
            <div class="type-content">
              <div class="type-title">应用</div>
              <div class="type-desc">传统业务应用</div>
            </div>
          </div>
        </div>
        
        <el-form-item label="应用名称" prop="name">
          <el-input v-model="editForm.name" placeholder="请输入应用名称" />
        </el-form-item>
        
        <el-form-item label="应用描述" prop="description">
          <el-input
            v-model="editForm.description"
            type="textarea"
            :rows="3"
            placeholder="请输入应用描述"
          />
        </el-form-item>
        
        <el-form-item label="所属公司" prop="company">
          <el-select v-model="editForm.company" placeholder="请选择公司" style="width: 100%">
            <el-option label="阿里巴巴集团" value="alibaba" />
            <el-option label="腾讯科技" value="tencent" />
            <el-option label="百度公司" value="baidu" />
            <el-option label="字节跳动" value="bytedance" />
            <el-option label="华为技术" value="huawei" />
            <el-option label="其他公司" value="other" />
          </el-select>
        </el-form-item>
        
        <el-form-item label="所属部门" prop="department">
          <el-select v-model="editForm.department" placeholder="请选择部门" style="width: 100%">
            <el-option label="技术研发部" value="rd" />
            <el-option label="产品设计部" value="product" />
            <el-option label="市场营销部" value="marketing" />
            <el-option label="人力资源部" value="hr" />
            <el-option label="财务管理部" value="finance" />
            <el-option label="运营管理部" value="operations" />
            <el-option label="客户服务部" value="service" />
            <el-option label="其他部门" value="other" />
          </el-select>
        </el-form-item>
        
        <el-form-item label="权限设置" prop="permissions">
          <el-select v-model="editForm.permissions" placeholder="请选择权限范围" style="width: 100%">
            <el-option label="本部门" value="department" />
            <el-option label="本公司" value="company" />
            <el-option label="跨公司本部门" value="cross-company-department" />
            <el-option label="指定角色" value="specific-role" />
          </el-select>
        </el-form-item>
        
        <el-form-item v-if="editForm.type === 'app'" label="跳转地址" prop="redirectUrl">
          <el-input v-model="editForm.redirectUrl" placeholder="请输入跳转地址（可选）" />
        </el-form-item>
        
        <el-form-item label="展示形式" prop="displayType">
          <el-radio-group v-model="editForm.displayType">
            <el-radio label="mobile">手机端</el-radio>
            <el-radio label="desktop">电脑端</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
      
      <template #footer>
        <div class="dialog-footer">
          <el-button @click="showEditDialog = false">取消</el-button>
          <el-button type="primary" @click="handleEditApp">保存</el-button>
        </div>
      </template>
    </el-dialog>
  </div>

  <!-- 表单页面 -->
  <AppForm v-else-if="currentPageView === 'form'" @back="goBackToList" @save="handleFormSave" />
  
  <!-- 智能体详情页面 -->
  <AgentDetail v-else-if="currentPageView === 'agent'" :agent="selectedAgent" @back="goBackToList" />
</template>

<script setup>
import { ref, onMounted } from 'vue'
import AppForm from './components/AppForm.vue'
import AgentDetail from './components/AgentDetail.vue'

const searchText = ref('')
const currentPage = ref(1)
const pageSize = ref(9)
const total = ref(100)

// 页面状态管理
const currentPageView = ref('list') // 'list' | 'form' | 'agent'
const selectedAgent = ref(null)

// 弹窗控制
const showCreateDialog = ref(false)
const showEditDialog = ref(false)
const createFormRef = ref()
const editFormRef = ref()

// 创建表单数据
const createForm = ref({
  type: '',
  name: '',
  description: '',
  company: '',
  department: '',
  permissions: '',
  redirectUrl: '',
  displayType: 'desktop'
})

// 编辑表单数据
const editForm = ref({
  id: null,
  type: '',
  name: '',
  description: '',
  company: '',
  department: '',
  permissions: '',
  redirectUrl: '',
  displayType: 'desktop'
})

// 表单验证规则
const createRules = {
  type: [{ required: true, message: '请选择应用类型', trigger: 'change' }],
  name: [{ required: true, message: '请输入应用名称', trigger: 'blur' }],
  description: [{ required: true, message: '请输入应用描述', trigger: 'blur' }],
  company: [{ required: true, message: '请输入公司名称', trigger: 'blur' }],
  department: [{ required: true, message: '请输入部门名称', trigger: 'blur' }],
  permissions: [{ required: true, message: '请选择权限设置', trigger: 'change' }],
  displayType: [{ required: true, message: '请选择展示形式', trigger: 'change' }]
}

const agentList = ref([
  {
    id: 1,
    name: '处方单药名提取',
    author: '智能体开发者',
    updateTime: '2024-06-18 17:47',
    type: 'agent',
    description: '这是一个专门用于从处方单中提取药名的智能体，能够准确识别各种药物名称，提高医疗工作效率。',
    avatarBg: 'linear-gradient(135deg, #667eea 0%, #764ba2 100%)',
    views: '1.2k',
    likes: '256'
  },
  {
    id: 2,
    name: '科技论文助手',
    author: '学术研究团队',
    updateTime: '2024-06-18 16:32',
    type: 'agent',
    description: '专业的科技论文写作助手，帮助研究人员撰写高质量的学术论文，提供格式规范和内容优化建议。',
    avatarBg: 'linear-gradient(135deg, #f093fb 0%, #f5576c 100%)',
    views: '2.8k',
    likes: '432'
  },
  {
    id: 3,
    name: '材料标准查询系统',
    author: '工程技术专家',
    updateTime: '2024-06-18 15:28',
    type: 'app',
    description: '提供各种工程材料的标准规范查询和技术指导服务，确保工程质量符合行业标准。',
    avatarBg: 'linear-gradient(135deg, #4facfe 0%, #00f2fe 100%)',
    views: '856',
    likes: '189'
  },
  {
    id: 4,
    name: '员工培训助手',
    author: '人力资源专家',
    updateTime: '2024-06-18 14:20',
    type: 'agent',
    description: '专业的员工培训课程设计和培训效果评估工具，帮助企业提升员工技能和工作效率。',
    avatarBg: 'linear-gradient(135deg, #fa709a 0%, #fee140 100%)',
    views: '1.5k',
    likes: '298'
  },
  {
    id: 5,
    name: '内容管理平台',
    author: '创意团队',
    updateTime: '2024-06-18 13:45',
    type: 'app',
    description: '高效的内容创作工具，支持多种文体和风格的创作需求，让创意变成现实。',
    avatarBg: 'linear-gradient(135deg, #a8edea 0%, #fed6e3 100%)',
    views: '3.2k',
    likes: '567'
  },
  {
    id: 6,
    name: '数据分析师',
    author: '数据科学团队',
    updateTime: '2024-06-18 12:30',
    type: 'agent',
    description: '智能数据分析工具，提供数据清洗、统计分析和可视化功能，让数据洞察更清晰。',
    avatarBg: 'linear-gradient(135deg, #ffecd2 0%, #fcb69f 100%)',
    views: '2.1k',
    likes: '378'
  },
  {
    id: 7,
    name: '代码审查平台',
    author: '开发团队',
    updateTime: '2024-06-18 11:15',
    type: 'app',
    description: '专业的代码审查工具，帮助开发者发现潜在问题，提升代码质量和安全性。',
    avatarBg: 'linear-gradient(135deg, #ff9a9e 0%, #fecfef 100%)',
    views: '1.8k',
    likes: '324'
  },
  {
    id: 8,
    name: '翻译专家',
    author: '语言服务团队',
    updateTime: '2024-06-18 10:00',
    type: 'agent',
    description: '多语言翻译专家，支持50+种语言互译，保持语境准确性和文化适应性。',
    avatarBg: 'linear-gradient(135deg, #a18cd1 0%, #fbc2eb 100%)',
    views: '4.5k',
    likes: '789'
  },
  {
    id: 9,
    name: '客服系统',
    author: '客户服务团队',
    updateTime: '2024-06-18 09:30',
    type: 'app',
    description: '智能客服机器人，24小时在线服务，快速响应客户问题，提升服务体验。',
    avatarBg: 'linear-gradient(135deg, #fad0c4 0%, #ffd1ff 100%)',
    views: '3.7k',
    likes: '612'
  }
])

const handlePageChange = (page) => {
  currentPage.value = page
  // 这里可以添加数据加载逻辑
}

const handleCommand = (command) => {
  const [action, id] = command.split('-')
  console.log(`执行操作: ${action}, 智能体ID: ${id}`)
  
  switch (action) {
    case 'stats':
      // 查看统计
      console.log(`查看智能体 ${id} 的统计信息`)
      break
    case 'delete':
      // 删除智能体
      if (confirm('确定要删除这个智能体吗？')) {
        agentList.value = agentList.value.filter(agent => agent.id !== parseInt(id))
      }
      break
  }
}

// 重置创建表单
const resetCreateForm = () => {
  createForm.value = {
    type: '',
    name: '',
    description: '',
    company: '',
    department: '',
    permissions: '',
    redirectUrl: '',
    displayType: 'desktop'
  }
}

// 重置编辑表单
const resetEditForm = () => {
  editForm.value = {
    id: null,
    type: '',
    name: '',
    description: '',
    company: '',
    department: '',
    permissions: '',
    redirectUrl: '',
    displayType: 'desktop'
  }
}

// 关闭弹窗处理
const handleCloseDialog = (done) => {
  resetCreateForm()
  resetEditForm()
  done()
}

// 创建应用
const handleCreateApp = async () => {
  if (!createFormRef.value) return
  
  await createFormRef.value.validate((valid) => {
    if (valid) {
      // 生成新的ID
      const newId = Math.max(...agentList.value.map(item => item.id)) + 1
      
      // 创建新应用
      const newApp = {
        id: newId,
        name: createForm.value.name,
        author: '当前用户',
        updateTime: new Date().toLocaleString('zh-CN'),
        description: createForm.value.description,
        avatarBg: `linear-gradient(135deg, hsl(${Math.random() * 360}, 70%, 60%) 0%, hsl(${Math.random() * 360}, 70%, 70%) 100%)`,
        views: '0',
        likes: '0',
        // 扩展字段
        type: createForm.value.type,
        company: createForm.value.company,
        department: createForm.value.department,
        permissions: createForm.value.permissions,
        redirectUrl: createForm.value.redirectUrl,
        displayType: createForm.value.displayType
      }
      
      agentList.value.unshift(newApp)
      showCreateDialog.value = false
      resetCreateForm()
      
      console.log('应用创建成功！')
    }
  })
}

// 打开编辑弹窗
const openEditDialog = (agent) => {
  editForm.value = {
    id: agent.id,
    type: agent.type || 'agent',
    name: agent.name,
    description: agent.description,
    company: agent.company || '',
    department: agent.department || '',
    permissions: agent.permissions || 'department',
    redirectUrl: agent.redirectUrl || '',
    displayType: agent.displayType || 'desktop'
  }
  showEditDialog.value = true
}

// 编辑应用
const handleEditApp = async () => {
  if (!editFormRef.value) return
  
  await editFormRef.value.validate((valid) => {
    if (valid) {
      const index = agentList.value.findIndex(item => item.id === editForm.value.id)
      if (index !== -1) {
        // 更新应用信息
        agentList.value[index] = {
          ...agentList.value[index],
          name: editForm.value.name,
          description: editForm.value.description,
          type: editForm.value.type,
          company: editForm.value.company,
          department: editForm.value.department,
          permissions: editForm.value.permissions,
          redirectUrl: editForm.value.redirectUrl,
          displayType: editForm.value.displayType,
          updateTime: new Date().toLocaleString('zh-CN')
        }
        
        showEditDialog.value = false
        resetEditForm()
        
        console.log('应用更新成功！')
      }
    }
  })
}

// 处理卡片点击
const handleCardClick = (agent) => {
  console.log('点击卡片:', agent.name, '类型:', agent.type)
  
  selectedAgent.value = agent
  
  // 根据类型跳转到不同页面
  if (agent.type === 'app') {
    currentPageView.value = 'form'
  } else {
    // 智能体类型，跳转到智能体详情页面
    currentPageView.value = 'agent'
  }
}

// 返回列表页
const goBackToList = () => {
  currentPageView.value = 'list'
  selectedAgent.value = null
}

// 处理表单保存
const handleFormSave = (formData) => {
  console.log('保存表单数据:', formData)
  // 这里可以添加保存逻辑
  setTimeout(() => {
    goBackToList()
  }, 1000)
}

onMounted(() => {
  // 组件挂载后的初始化逻辑
})
</script>
<style scoped>
.app-container {
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

.nav-tab.active::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
  transition: left 0.5s;
}

.nav-tab.active:hover::before {
  left: 100%;
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

.login-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(64, 158, 255, 0.4);
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

.dropdown-icon {
  transition: transform 0.3s ease;
}

.user-info:hover .dropdown-icon {
  transform: rotate(180deg);
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

.menu-item.active::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 4px;
  height: 20px;
  background: white;
  border-radius: 2px;
}

.content {
  flex: 1;
  padding: 30px;
  overflow-y: auto;
  background: rgba(255, 255, 255, 0.5);
  border-radius: 0 20px 20px 0;
}

.search-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(10px);
  padding: 20px 25px;
  border-radius: 16px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.search-left {
  display: flex;
  gap: 12px;
}

.search-left .el-button {
  border-radius: 20px;
  font-weight: 500;
  transition: all 0.3s ease;
}

.search-left .el-button--primary {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

.search-left .el-button:not(.el-button--primary):hover {
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.1) 0%, rgba(118, 75, 162, 0.1) 100%);
  color: #667eea;
  border-color: #667eea;
}

.agent-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 25px;
  margin-bottom: 40px;
}

.create-card {
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  padding: 40px 25px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  cursor: pointer;
  border: 2px dashed rgba(102, 126, 234, 0.3);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  min-height: 200px;
  position: relative;
  overflow: hidden;
}

.create-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(102, 126, 234, 0.1), transparent);
  transition: left 0.6s;
}

.create-card:hover::before {
  left: 100%;
}

.create-card:hover {
  transform: translateY(-8px) scale(1.02);
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.15);
  border-color: rgba(102, 126, 234, 0.6);
}

.create-icon {
  width: 80px;
  height: 80px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
  box-shadow: 0 8px 25px rgba(102, 126, 234, 0.3);
  transition: all 0.3s ease;
}

.create-card:hover .create-icon {
  transform: scale(1.1);
  box-shadow: 0 12px 35px rgba(102, 126, 234, 0.4);
}

.create-icon .el-icon {
  font-size: 32px;
  color: white;
}

.create-title {
  font-size: 20px;
  font-weight: 700;
  color: #2c3e50;
  margin: 0 0 12px 0;
}

.create-description {
  font-size: 14px;
  color: #7f8c8d;
  margin: 0;
  line-height: 1.5;
}

.agent-card {
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  padding: 25px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  cursor: pointer;
  border: 1px solid rgba(255, 255, 255, 0.2);
  position: relative;
  overflow: hidden;
}

.agent-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.2), transparent);
  transition: left 0.6s;
}

.agent-card:hover::before {
  left: 100%;
}

.agent-card:hover {
  transform: translateY(-8px) scale(1.02);
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.15);
}

/* 应用类型卡片特殊样式 */
.app-card-clickable {
  border-color: rgba(103, 194, 58, 0.3);
}

.app-card-clickable:hover {
  border-color: rgba(103, 194, 58, 0.6);
  box-shadow: 0 20px 40px rgba(103, 194, 58, 0.2);
}

.app-card-clickable::after {
  content: '点击配置';
  position: absolute;
  top: 15px;
  right: 15px;
  background: linear-gradient(135deg, rgba(103, 194, 58, 0.9) 0%, rgba(103, 194, 58, 0.7) 100%);
  color: white;
  padding: 4px 8px;
  border-radius: 8px;
  font-size: 11px;
  font-weight: 500;
  opacity: 0;
  transform: translateY(-5px);
  transition: all 0.3s ease;
  pointer-events: none;
}

.app-card-clickable:hover::after {
  opacity: 1;
  transform: translateY(0);
}

/* 智能体类型卡片特殊样式 */
.agent-card-clickable {
  border-color: rgba(64, 158, 255, 0.3);
}

.agent-card-clickable:hover {
  border-color: rgba(64, 158, 255, 0.6);
  box-shadow: 0 20px 40px rgba(64, 158, 255, 0.2);
}

.agent-card-clickable::after {
  content: '点击调试';
  position: absolute;
  top: 15px;
  right: 15px;
  background: linear-gradient(135deg, rgba(64, 158, 255, 0.9) 0%, rgba(64, 158, 255, 0.7) 100%);
  color: white;
  padding: 4px 8px;
  border-radius: 8px;
  font-size: 11px;
  font-weight: 500;
  opacity: 0;
  transform: translateY(-5px);
  transition: all 0.3s ease;
  pointer-events: none;
}

.agent-card-clickable:hover::after {
  opacity: 1;
  transform: translateY(0);
}

.card-header {
  display: flex;
  gap: 15px;
  margin-bottom: 18px;
  align-items: flex-start;
}

.agent-avatar {
  width: 56px;
  height: 56px;
  border-radius: 16px;
  flex-shrink: 0;
  position: relative;
}

.avatar-bg {
  width: 100%;
  height: 100%;
  border-radius: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}

.avatar-icon {
  font-size: 24px;
  color: white;
}

.agent-info {
  flex: 1;
  min-width: 0;
}

.agent-title-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.agent-name {
  font-size: 18px;
  font-weight: 700;
  color: #2c3e50;
  margin: 0;
  line-height: 1.3;
}

.agent-meta-row {
  margin-bottom: 8px;
}

.agent-author {
  font-size: 13px;
  color: #7f8c8d;
  margin: 0;
  display: flex;
  align-items: center;
  gap: 6px;
}

.card-footer {
  padding-top: 15px;
  border-top: 1px solid rgba(0, 0, 0, 0.05);
}

.card-actions-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 20px;
}

.card-actions-left,
.card-actions-right {
  display: flex;
  gap: 12px;
  flex-shrink: 0;
}

.action-btn {
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  color: #7f8c8d;
  transition: all 0.3s ease;
  padding: 8px 12px;
  font-size: 13px;
}

.action-btn:hover {
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.1) 0%, rgba(118, 75, 162, 0.1) 100%);
  color: #667eea;
}

/* 下拉菜单样式 */
:deep(.el-dropdown-menu) {
  border-radius: 12px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.15);
  border: 1px solid rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(10px);
  background: rgba(255, 255, 255, 0.95);
}

:deep(.el-dropdown-menu__item) {
  padding: 12px 16px;
  border-radius: 8px;
  margin: 4px 8px;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 8px;
}

:deep(.el-dropdown-menu__item:hover) {
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.1) 0%, rgba(118, 75, 162, 0.1) 100%);
  color: #667eea;
}

:deep(.el-dropdown-menu__item.is-divided) {
  border-top: 1px solid rgba(0, 0, 0, 0.05);
  margin-top: 8px;
}

:deep(.el-dropdown-menu__item.is-divided:hover) {
  background: linear-gradient(135deg, rgba(255, 99, 99, 0.1) 0%, rgba(255, 71, 87, 0.1) 100%);
  color: #ff6b6b;
}

/* 弹窗样式 */
:deep(.el-dialog) {
  border-radius: 20px;
  overflow: hidden;
  backdrop-filter: blur(10px);
  background: rgba(255, 255, 255, 0.95);
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.2);
}

:deep(.el-dialog__header) {
  padding: 20px 30px;
  margin: 0;
  border-bottom: 1px solid #e4e7ed;
}

:deep(.el-dialog__title) {
  color: #2c3e50;
  font-weight: 600;
  font-size: 18px;
}

:deep(.el-dialog__headerbtn) {
  top: 20px;
  right: 20px;
}

:deep(.el-dialog__headerbtn .el-dialog__close) {
  color: #909399;
  font-size: 18px;
}

:deep(.el-dialog__body) {
  padding: 30px;
}

:deep(.el-dialog__footer) {
  padding: 20px 30px 30px;
  text-align: right;
}

:deep(.el-form-item__label) {
  font-weight: 500;
  color: #2c3e50;
}

:deep(.el-input__wrapper) {
  border-radius: 8px;
  transition: all 0.3s ease;
}

:deep(.el-input__wrapper:hover) {
  box-shadow: 0 2px 8px rgba(102, 126, 234, 0.2);
}

:deep(.el-input__wrapper.is-focus) {
  box-shadow: 0 2px 8px rgba(102, 126, 234, 0.3);
}

:deep(.el-select .el-input__wrapper) {
  border-radius: 8px;
}

:deep(.el-textarea__inner) {
  border-radius: 8px;
  transition: all 0.3s ease;
}

:deep(.el-textarea__inner:hover) {
  box-shadow: 0 2px 8px rgba(102, 126, 234, 0.2);
}

:deep(.el-textarea__inner:focus) {
  box-shadow: 0 2px 8px rgba(102, 126, 234, 0.3);
}

:deep(.el-checkbox) {
  margin-right: 20px;
  margin-bottom: 8px;
}

:deep(.el-radio) {
  margin-right: 20px;
  margin-bottom: 8px;
}

.dialog-footer {
  display: flex;
  justify-content: flex-end;
  gap: 12px;
}

.dialog-footer .el-button {
  border-radius: 8px;
  padding: 10px 20px;
  font-weight: 500;
}

.dialog-footer .el-button--primary {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: none;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

/* 应用类型卡片样式 */
.type-cards {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 16px;
  margin-bottom: 24px;
}

.type-card {
  border: 2px solid #e4e7ed;
  border-radius: 16px;
  padding: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
  background: rgba(255, 255, 255, 0.9);
  position: relative;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  gap: 16px;
}

.type-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
  transition: left 0.6s;
}

.type-card:hover::before {
  left: 100%;
}

/* 智能体卡片 - 蓝色主题 */
.agent-card {
  border-color: rgba(64, 158, 255, 0.3);
}

.agent-card:hover {
  border-color: rgba(64, 158, 255, 0.6);
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(64, 158, 255, 0.2);
}

.agent-card.active {
  border-color: #409eff;
  background: linear-gradient(135deg, rgba(64, 158, 255, 0.1) 0%, rgba(64, 158, 255, 0.05) 100%);
  box-shadow: 0 4px 15px rgba(64, 158, 255, 0.3);
}

.agent-card .type-icon {
  background: linear-gradient(135deg, #409eff 0%, #66b3ff 100%);
}

.agent-card.active .type-icon {
  box-shadow: 0 6px 20px rgba(64, 158, 255, 0.4);
}

/* 应用卡片 - 绿色主题 */
.app-card {
  border-color: rgba(103, 194, 58, 0.3);
}

.app-card:hover {
  border-color: rgba(103, 194, 58, 0.6);
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(103, 194, 58, 0.2);
}

.app-card.active {
  border-color: #67c23a;
  background: linear-gradient(135deg, rgba(103, 194, 58, 0.1) 0%, rgba(103, 194, 58, 0.05) 100%);
  box-shadow: 0 4px 15px rgba(103, 194, 58, 0.3);
}

.app-card .type-icon {
  background: linear-gradient(135deg, #67c23a 0%, #85ce61 100%);
}

.app-card.active .type-icon {
  box-shadow: 0 6px 20px rgba(103, 194, 58, 0.4);
}

.type-icon {
  width: 56px;
  height: 56px;
  border-radius: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  flex-shrink: 0;
}

.type-card.active .type-icon {
  transform: scale(1.05);
}

.custom-icon {
  width: 28px;
  height: 28px;
  color: white;
}

.type-content {
  flex: 1;
  text-align: center;
}

.type-title {
  font-size: 16px;
  font-weight: 600;
  color: #2c3e50;
  margin-bottom: 6px;
}

.type-desc {
  font-size: 13px;
  color: #7f8c8d;
  line-height: 1.4;
}

/* 只读状态样式 */
.type-cards.readonly .type-card {
  cursor: not-allowed;
  opacity: 0.8;
}

.type-cards.readonly .type-card:hover {
  transform: none;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

.type-cards.readonly .agent-card:hover {
  border-color: rgba(64, 158, 255, 0.3);
  box-shadow: 0 4px 15px rgba(64, 158, 255, 0.1);
}

.type-cards.readonly .app-card:hover {
  border-color: rgba(103, 194, 58, 0.3);
  box-shadow: 0 4px 15px rgba(103, 194, 58, 0.1);
}

.type-cards.readonly .type-card::before {
  display: none;
}

/* 类型标签样式 */
.type-tag {
  padding: 4px 12px;
  border-radius: 12px;
  font-size: 12px;
  font-weight: 500;
  white-space: nowrap;
  flex-shrink: 0;
  transition: all 0.3s ease;
}

.type-tag.agent {
  background: linear-gradient(135deg, rgba(64, 158, 255, 0.1) 0%, rgba(64, 158, 255, 0.05) 100%);
  color: #409eff;
  border: 1px solid rgba(64, 158, 255, 0.2);
}

.type-tag.app {
  background: linear-gradient(135deg, rgba(103, 194, 58, 0.1) 0%, rgba(103, 194, 58, 0.05) 100%);
  color: #67c23a;
  border: 1px solid rgba(103, 194, 58, 0.2);
}

.agent-card:hover .type-tag.agent {
  background: linear-gradient(135deg, rgba(64, 158, 255, 0.15) 0%, rgba(64, 158, 255, 0.08) 100%);
  border-color: rgba(64, 158, 255, 0.3);
}

.agent-card:hover .type-tag.app {
  background: linear-gradient(135deg, rgba(103, 194, 58, 0.15) 0%, rgba(103, 194, 58, 0.08) 100%);
  border-color: rgba(103, 194, 58, 0.3);
}



.card-content {
  margin-bottom: 20px;
  flex: 1;
}

.agent-description {
  font-size: 14px;
  color: #5a6c7d;
  line-height: 1.6;
  margin: 0;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}



.pagination-container {
  display: flex;
  justify-content: center;
  padding: 30px 0;
}

.pagination-container :deep(.el-pagination) {
  --el-pagination-bg-color: rgba(255, 255, 255, 0.9);
  --el-pagination-text-color: #667eea;
  --el-pagination-border-radius: 12px;
}

.pagination-container :deep(.el-pager li) {
  border-radius: 8px;
  margin: 0 4px;
  transition: all 0.3s ease;
}

.pagination-container :deep(.el-pager li.is-active) {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

/* 响应式设计 */
@media (max-width: 1400px) {
  .agent-grid {
    grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  }
}

@media (max-width: 1200px) {
  .agent-grid {
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  }
  
  .main-container {
    margin: 15px;
  }
}

@media (max-width: 768px) {
  .app-container {
    background: #f8f9fa;
  }
  
  .main-container {
    flex-direction: column;
    margin: 10px;
    border-radius: 15px;
  }
  
  .sidebar {
    width: 100%;
    height: auto;
    border-right: none;
    border-bottom: 1px solid rgba(228, 231, 237, 0.3);
    border-radius: 15px 15px 0 0;
    padding: 20px 0;
  }
  
  .content {
    border-radius: 0 0 15px 15px;
    padding: 20px;
  }
  
  .search-bar {
    flex-direction: column;
    gap: 16px;
    align-items: stretch;
    padding: 15px 20px;
  }
  
  .agent-grid {
    grid-template-columns: 1fr;
    gap: 20px;
  }
  
  .header {
    padding: 0 15px;
    height: 60px;
  }
  
  .header-left {
    gap: 20px;
  }
  
  .logo {
    font-size: 16px;
  }
  
  .logo-icon {
    width: 32px;
    height: 32px;
    font-size: 16px;
  }
}
</style>