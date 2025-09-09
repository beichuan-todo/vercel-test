<template>
  <div class="agent-detail-container">
    <!-- 顶部导航栏 -->
    <header class="header">
      <div class="header-left">
        <div class="back-button" @click="$emit('back')">
          <el-icon><ArrowLeft /></el-icon>
          <span>{{ agent?.name || '智能体详情' }}</span>
        </div>
      </div>
      <div class="header-right">
        <el-button type="primary" size="small" class="publish-btn">
          <el-icon><Upload /></el-icon>
          发布智能体
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
        <div class="agent-info-card">
          <div class="agent-avatar">
            <div class="avatar-bg" :style="{ background: agent?.avatarBg || 'linear-gradient(135deg, #667eea 0%, #764ba2 100%)' }">
              <el-icon class="avatar-icon"><Robot /></el-icon>
            </div>
          </div>
          <h3 class="agent-name">{{ agent?.name || '智能体名称' }}</h3>
          <p class="agent-desc">{{ agent?.description || '智能体描述' }}</p>
        </div>

        <div class="sidebar-section">
          <div class="menu-item active">
            <el-icon><ChatDotRound /></el-icon>
            <span>对话调试</span>
          </div>
          <div class="menu-item">
            <el-icon><Setting /></el-icon>
            <span>基础配置</span>
          </div>
          <div class="menu-item">
            <el-icon><Document /></el-icon>
            <span>知识库</span>
          </div>
          <div class="menu-item">
            <el-icon><Connection /></el-icon>
            <span>工具集成</span>
          </div>
          <div class="menu-item">
            <el-icon><DataAnalysis /></el-icon>
            <span>数据分析</span>
          </div>
          <div class="menu-item">
            <el-icon><Monitor /></el-icon>
            <span>监控统计</span>
          </div>
        </div>
      </aside>

      <!-- 主内容区域 -->
      <main class="content">
        <div class="content-wrapper">
          <!-- 左侧对话区域 -->
          <div class="chat-section">
            <div class="chat-tabs">
              <div class="tab active">对话调试</div>
              <div class="tab">高级调试</div>
            </div>

            <!-- 基础信息卡片 -->
            <div class="info-card">
              <div class="card-header">
                <el-icon><InfoFilled /></el-icon>
                <span>基础信息</span>
              </div>
              <div class="info-content">
                <div class="info-item">
                  <span class="label">智能体名称:</span>
                  <span class="value">{{ agent?.name || '处方单药名提取' }}</span>
                </div>
                <div class="info-item">
                  <span class="label">创建时间:</span>
                  <span class="value">{{ agent?.updateTime || '2024-06-18 17:47' }}</span>
                </div>
                <div class="info-item">
                  <span class="label">作者:</span>
                  <span class="value">{{ agent?.author || '智能体开发者' }}</span>
                </div>
              </div>
            </div>

            <!-- 对话测试区域 -->
            <div class="chat-test-area">
              <div class="test-header">
                <el-icon><ChatDotRound /></el-icon>
                <span>对话测试</span>
                <el-button size="small" text @click="clearChat">清空对话</el-button>
              </div>
              
              <div class="chat-messages" ref="chatMessages">
                <div v-for="(message, index) in chatHistory" :key="index" class="message" :class="message.type">
                  <div class="message-avatar">
                    <el-avatar v-if="message.type === 'user'" size="small" src="https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png" />
                    <div v-else class="ai-avatar">
                      <el-icon><Robot /></el-icon>
                    </div>
                  </div>
                  <div class="message-content">
                    <div class="message-text">{{ message.content }}</div>
                    <div class="message-time">{{ message.time }}</div>
                  </div>
                </div>
              </div>

              <div class="chat-input-area">
                <el-input
                  v-model="inputMessage"
                  type="textarea"
                  :rows="3"
                  placeholder="请输入您的问题..."
                  @keydown.ctrl.enter="sendMessage"
                />
                <div class="input-actions">
                  <div class="input-tips">
                    <span>Ctrl + Enter 发送</span>
                  </div>
                  <el-button type="primary" @click="sendMessage" :loading="sending">
                    发送
                  </el-button>
                </div>
              </div>
            </div>
          </div>

          <!-- 右侧信息面板 -->
          <div class="info-panel">
            <div class="panel-header">
              <h3>调试信息</h3>
            </div>
            
            <div class="debug-info">
              <div class="debug-section">
                <h4>模型参数</h4>
                <div class="param-item">
                  <span>温度值:</span>
                  <span>0.7</span>
                </div>
                <div class="param-item">
                  <span>最大长度:</span>
                  <span>2048</span>
                </div>
                <div class="param-item">
                  <span>Top-p:</span>
                  <span>0.9</span>
                </div>
              </div>

              <div class="debug-section">
                <h4>系统提示词</h4>
                <div class="prompt-content">
                  你是一个专业的医疗助手，专门用于从处方单中提取药名。请准确识别各种药物名称，包括通用名和商品名，并提供相关的用药指导。
                </div>
              </div>

              <div class="debug-section">
                <h4>使用统计</h4>
                <div class="stats-grid">
                  <div class="stat-item">
                    <div class="stat-value">{{ agent?.views || '1.2k' }}</div>
                    <div class="stat-label">调用次数</div>
                  </div>
                  <div class="stat-item">
                    <div class="stat-value">98.5%</div>
                    <div class="stat-label">准确率</div>
                  </div>
                  <div class="stat-item">
                    <div class="stat-value">1.2s</div>
                    <div class="stat-label">平均响应</div>
                  </div>
                  <div class="stat-item">
                    <div class="stat-value">{{ agent?.likes || '256' }}</div>
                    <div class="stat-label">用户评分</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, nextTick } from 'vue'
import { ElMessage } from 'element-plus'

// 定义props和事件
const props = defineProps({
  agent: {
    type: Object,
    default: () => ({})
  }
})

const emit = defineEmits(['back'])

// 聊天相关状态
const inputMessage = ref('')
const sending = ref(false)
const chatMessages = ref()

const chatHistory = reactive([
  {
    type: 'ai',
    content: '您好！我是专业的处方单药名提取助手。请提供处方单内容，我将帮您准确识别其中的药物名称。',
    time: new Date().toLocaleTimeString()
  }
])

// 发送消息
const sendMessage = async () => {
  if (!inputMessage.value.trim()) return
  
  const userMessage = {
    type: 'user',
    content: inputMessage.value,
    time: new Date().toLocaleTimeString()
  }
  
  chatHistory.push(userMessage)
  
  const messageContent = inputMessage.value
  inputMessage.value = ''
  sending.value = true
  
  // 滚动到底部
  await nextTick()
  scrollToBottom()
  
  // 模拟AI回复
  setTimeout(() => {
    const aiResponse = generateAIResponse(messageContent)
    chatHistory.push({
      type: 'ai',
      content: aiResponse,
      time: new Date().toLocaleTimeString()
    })
    sending.value = false
    
    nextTick(() => {
      scrollToBottom()
    })
  }, 1500)
}

// 生成AI回复
const generateAIResponse = (userInput) => {
  const responses = [
    '根据您提供的处方单，我识别出以下药物：\n1. 阿莫西林胶囊 - 抗生素类药物\n2. 布洛芬片 - 解热镇痛药\n3. 维生素C片 - 维生素补充剂\n\n请注意按医嘱服用，如有疑问请咨询医生。',
    '我已经分析了您的输入。如果您能提供更清晰的处方单图片或文字，我可以为您提供更准确的药名识别结果。',
    '处方单药名提取完成：\n• 主要药物：已识别3种处方药物\n• 用药建议：请严格按照医生指导用药\n• 注意事项：如有过敏史请告知医生',
    '感谢您的咨询。基于我的专业知识，我已为您整理了药物清单。如需了解具体用法用量，建议咨询您的主治医生。'
  ]
  
  return responses[Math.floor(Math.random() * responses.length)]
}

// 清空对话
const clearChat = () => {
  chatHistory.splice(1) // 保留第一条欢迎消息
  ElMessage.success('对话已清空')
}

// 滚动到底部
const scrollToBottom = () => {
  if (chatMessages.value) {
    chatMessages.value.scrollTop = chatMessages.value.scrollHeight
  }
}
</script>

<style scoped>
.agent-detail-container {
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
}

.back-button {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  padding: 8px 16px;
  border-radius: 8px;
  transition: all 0.3s ease;
  color: #667eea;
  font-weight: 500;
}

.back-button:hover {
  background: rgba(102, 126, 234, 0.1);
}

.header-right {
  display: flex;
  align-items: center;
  gap: 20px;
}

.publish-btn {
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
  width: 280px;
  background: rgba(255, 255, 255, 0.8);
  border-right: 1px solid rgba(228, 231, 237, 0.3);
  padding: 30px 0;
  overflow-y: auto;
  border-radius: 20px 0 0 20px;
}

.agent-info-card {
  padding: 0 25px 30px;
  text-align: center;
  border-bottom: 1px solid rgba(228, 231, 237, 0.3);
  margin-bottom: 30px;
}

.agent-avatar {
  width: 80px;
  height: 80px;
  margin: 0 auto 15px;
  border-radius: 20px;
}

.avatar-bg {
  width: 100%;
  height: 100%;
  border-radius: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 32px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

.agent-name {
  font-size: 18px;
  font-weight: 600;
  color: #2c3e50;
  margin: 0 0 8px 0;
}

.agent-desc {
  font-size: 13px;
  color: #7f8c8d;
  line-height: 1.5;
  margin: 0;
}

.sidebar-section {
  padding: 0 15px;
}

.menu-item {
  display: flex;
  align-items: center;
  gap: 15px;
  padding: 15px 25px;
  cursor: pointer;
  transition: all 0.3s ease;
  color: #606266;
  margin-bottom: 8px;
  border-radius: 12px;
  font-weight: 500;
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
  overflow: hidden;
  background: rgba(255, 255, 255, 0.5);
  border-radius: 0 20px 20px 0;
}

.content-wrapper {
  height: 100%;
  display: flex;
}

.chat-section {
  flex: 1;
  display: flex;
  flex-direction: column;
  padding: 30px;
  border-right: 1px solid rgba(228, 231, 237, 0.3);
}

.chat-tabs {
  display: flex;
  gap: 8px;
  margin-bottom: 25px;
}

.tab {
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-weight: 500;
  color: #606266;
}

.tab.active {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
}

.info-card {
  background: rgba(255, 255, 255, 0.9);
  border-radius: 12px;
  padding: 20px;
  margin-bottom: 25px;
  border: 1px solid rgba(228, 231, 237, 0.3);
}

.card-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 15px;
  color: #667eea;
  font-weight: 600;
}

.info-content {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.info-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.label {
  color: #7f8c8d;
  font-size: 14px;
}

.value {
  color: #2c3e50;
  font-weight: 500;
}

.chat-test-area {
  flex: 1;
  display: flex;
  flex-direction: column;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 12px;
  border: 1px solid rgba(228, 231, 237, 0.3);
  overflow: hidden;
}

.test-header {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 20px;
  border-bottom: 1px solid rgba(228, 231, 237, 0.3);
  background: rgba(102, 126, 234, 0.05);
  color: #667eea;
  font-weight: 600;
}

.chat-messages {
  flex: 1;
  overflow-y: auto;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.message {
  display: flex;
  gap: 12px;
  align-items: flex-start;
}

.message.user {
  flex-direction: row-reverse;
}

.message-avatar {
  flex-shrink: 0;
}

.ai-avatar {
  width: 32px;
  height: 32px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 16px;
}

.message-content {
  max-width: 70%;
}

.message.user .message-content {
  text-align: right;
}

.message-text {
  background: #f8f9fa;
  padding: 12px 16px;
  border-radius: 12px;
  line-height: 1.5;
  white-space: pre-wrap;
}

.message.user .message-text {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

.message-time {
  font-size: 12px;
  color: #7f8c8d;
  margin-top: 5px;
}

.chat-input-area {
  padding: 20px;
  border-top: 1px solid rgba(228, 231, 237, 0.3);
  background: rgba(248, 249, 250, 0.5);
}

.input-actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 12px;
}

.input-tips {
  font-size: 12px;
  color: #7f8c8d;
}

.info-panel {
  width: 320px;
  padding: 30px;
  background: rgba(255, 255, 255, 0.8);
  overflow-y: auto;
}

.panel-header h3 {
  margin: 0 0 25px 0;
  color: #2c3e50;
  font-size: 18px;
  font-weight: 600;
}

.debug-info {
  display: flex;
  flex-direction: column;
  gap: 25px;
}

.debug-section h4 {
  margin: 0 0 15px 0;
  color: #667eea;
  font-size: 14px;
  font-weight: 600;
}

.param-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 0;
  border-bottom: 1px solid rgba(228, 231, 237, 0.3);
  font-size: 14px;
}

.param-item:last-child {
  border-bottom: none;
}

.prompt-content {
  background: rgba(248, 249, 250, 0.8);
  padding: 15px;
  border-radius: 8px;
  font-size: 13px;
  line-height: 1.5;
  color: #5a6c7d;
}

.stats-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
}

.stat-item {
  text-align: center;
  padding: 15px;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 8px;
  border: 1px solid rgba(228, 231, 237, 0.3);
}

.stat-value {
  font-size: 18px;
  font-weight: 600;
  color: #667eea;
  margin-bottom: 5px;
}

.stat-label {
  font-size: 12px;
  color: #7f8c8d;
}

/* 响应式设计 */
@media (max-width: 1200px) {
  .info-panel {
    width: 280px;
  }
}

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
  
  .content-wrapper {
    flex-direction: column;
  }
  
  .info-panel {
    width: 100%;
    border-top: 1px solid rgba(228, 231, 237, 0.3);
  }
}
</style>