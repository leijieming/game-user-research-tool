# 游戏设计用户研究工具 v3.0 - 三栏看板布局实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将工具从单栏 Tab 布局升级为三栏看板式布局，实现侧边栏模板库 + 可拖拽/折叠/增删/调整大小的面板系统

**Architecture:** 重构 index.html 的 HTML 结构，添加侧边栏组件和看板面板系统，使用 CSS Grid 实现响应式布局，HTML5 Drag API 实现拖拽功能

**Tech Stack:** Vue 3 (Composition API) + CSS3 + HTML5 Drag API

---

## 文件结构

```
/workspace/
├── index.html              # 主文件（重构 HTML + CSS + JS）
├── docs/superpowers/
│   ├── specs/
│   │   └── 2025-05-08-kanban-layout-design.md
│   └── plans/
│       └── 2025-05-08-kanban-layout-impl-plan.md
```

---

## 实现任务

### Task 1: 重构 HTML 结构 - 三栏布局框架

**文件:**
- 修改: `/sessions/69fcc95fa3baa9b124417137/workspace/index.html`

- [ ] **Step 1: 替换 HTML 结构**

找到 `<!-- 页面主容器 -->` 区域，替换为：

```html
<!-- 页面主容器 - 三栏布局 -->
<div class="app-layout">
    <!-- 顶部标题栏 -->
    <header class="app-header">
        <h1 class="app-title">🎮 游戏设计用户研究工具</h1>
        <div class="header-actions">
            <button class="btn btn-secondary" @click="importAllData">📥 导入</button>
            <button class="btn btn-secondary" @click="exportAllData">📤 导出</button>
            <button class="btn btn-secondary" @click="showSettings">⚙️ 设置</button>
        </div>
    </header>

    <!-- Tab 导航栏 -->
    <nav class="tab-nav">
        <button 
            v-for="tab in tabs" 
            :key="tab.id"
            :class="['tab-btn', { active: activeTab === tab.id }]"
            @click="switchTab(tab.id)"
        >
            {{ tab.icon }} {{ tab.name }}
        </button>
    </nav>

    <!-- 内容区域 -->
    <div class="app-content">
        <!-- 左侧边栏 -->
        <aside class="sidebar">
            <div class="sidebar-header">
                📂 模板库
            </div>
            <div class="sidebar-content">
                <template v-for="section in getSidebarSections(activeTab)" :key="section.id">
                    <div class="sidebar-section">
                        <div class="sidebar-section-title" @click="toggleSection(section.id)">
                            {{ section.name }}
                            <span>{{ section.collapsed ? '▶' : '▼' }}</span>
                        </div>
                        <div v-show="!section.collapsed">
                            <div 
                                v-for="item in section.items" 
                                :key="item.id"
                                class="sidebar-item"
                                @click="loadTemplate(item)"
                            >
                                {{ item.name }}
                            </div>
                        </div>
                    </div>
                </template>
                <div class="sidebar-add-btn" @click="addNewPanel">
                    ➕ 添加面板
                </div>
            </div>
        </aside>

        <!-- 右侧主内容区 - 看板 -->
        <main class="main-content">
            <div class="kanban-container">
                <!-- 面板将通过 v-for 循环渲染 -->
                <div 
                    v-for="panel in getCurrentPanels()"
                    :key="panel.id"
                    :class="['panel', { collapsed: panel.collapsed, dragging: isDragging === panel.id }]"
                    draggable="true"
                    @dragstart="onDragStart($event, panel)"
                    @dragover="onDragOver($event, panel)"
                    @drop="onDrop($event, panel)"
                    @dragend="onDragEnd"
                >
                    <div class="panel-header">
                        <div class="panel-title">
                            <span class="panel-drag-handle">⋮⋮</span>
                            {{ panel.title }}
                        </div>
                        <div class="panel-actions">
                            <button class="panel-action-btn" @click="togglePanelCollapse(panel.id)" :title="panel.collapsed ? '展开' : '折叠'">
                                {{ panel.collapsed ? '⬆' : '⬇' }}
                            </button>
                            <button class="panel-action-btn" @click="resetPanelSize(panel.id)" title="重置大小">⛶</button>
                            <button class="panel-action-btn" @click="deletePanel(panel.id)" title="删除">✕</button>
                        </div>
                    </div>
                    <div class="panel-content" v-show="!panel.collapsed">
                        <!-- 面板内容将由各模块填充 -->
                    </div>
                </div>
            </div>
        </main>
    </div>
</div>
```

---

### Task 2: 更新 CSS 样式 - 三栏布局

**文件:**
- 修改: `/sessions/69fcc95fa3baa9b124417137/workspace/index.html`

- [ ] **Step 1: 添加布局容器样式**

在 CSS 中添加：

```css
/* 三栏布局容器 */
.app-layout {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
    background: var(--bg-gradient);
}

/* 顶部标题栏 */
.app-header {
    height: 64px;
    background: rgba(0, 0, 0, 0.2);
    backdrop-filter: blur(10px);
    border-bottom: 1px solid var(--glass-border);
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 24px;
    position: sticky;
    top: 0;
    z-index: 50;
}

.app-title {
    font-size: 20px;
    font-weight: 700;
    background: var(--primary-gradient);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    margin: 0;
}

.header-actions {
    display: flex;
    gap: 12px;
}

/* Tab 导航栏 */
.tab-nav {
    height: 48px;
    background: rgba(0, 0, 0, 0.15);
    border-bottom: 1px solid var(--glass-border);
    display: flex;
    align-items: center;
    padding: 0 16px;
    gap: 4px;
    overflow-x: auto;
}

/* 内容区域 */
.app-content {
    display: flex;
    flex: 1;
    min-height: 0;
}
```

- [ ] **Step 2: 添加侧边栏样式**

```css
/* 侧边栏 */
.sidebar {
    width: 220px;
    min-width: 180px;
    background: rgba(0, 0, 0, 0.15);
    border-right: 1px solid var(--glass-border);
    display: flex;
    flex-direction: column;
    overflow-y: auto;
}

.sidebar-header {
    padding: 16px;
    font-weight: 600;
    color: var(--text);
    border-bottom: 1px solid var(--glass-border);
    display: flex;
    align-items: center;
    gap: 8px;
}

.sidebar-content {
    flex: 1;
    overflow-y: auto;
}

.sidebar-section {
    border-bottom: 1px solid var(--glass-border);
}

.sidebar-section-title {
    padding: 12px 16px;
    font-size: 12px;
    font-weight: 600;
    color: var(--text-muted);
    text-transform: uppercase;
    letter-spacing: 0.5px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.sidebar-section-title:hover {
    background: var(--glass-bg);
}

.sidebar-item {
    padding: 10px 16px 10px 24px;
    color: var(--text-secondary);
    font-size: 14px;
    cursor: pointer;
    transition: all 0.2s;
}

.sidebar-item:hover {
    background: var(--glass-bg);
    color: var(--text);
}

.sidebar-item.active {
    background: var(--primary);
    color: white;
}

.sidebar-add-btn {
    padding: 16px;
    color: var(--primary);
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 8px;
    font-weight: 500;
}

.sidebar-add-btn:hover {
    background: var(--glass-bg);
}
```

- [ ] **Step 3: 添加看板和面板样式**

```css
/* 主内容区 */
.main-content {
    flex: 1;
    padding: 20px;
    overflow-y: auto;
}

/* 看板容器 */
.kanban-container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
    gap: 16px;
    align-content: start;
}

/* 面板 */
.panel {
    background: var(--glass-bg);
    backdrop-filter: blur(10px);
    border: 1px solid var(--glass-border);
    border-radius: 16px;
    overflow: hidden;
    transition: all 0.3s ease;
    min-width: 300px;
    min-height: 200px;
}

.panel:hover {
    border-color: var(--primary);
    box-shadow: var(--glass-shadow), var(--glow-spread);
}

.panel.collapsed {
    min-height: auto;
}

.panel.dragging {
    opacity: 0.5;
    transform: scale(1.02);
}

.panel-header {
    background: rgba(0, 0, 0, 0.2);
    padding: 12px 16px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    cursor: grab;
    border-bottom: 1px solid var(--glass-border);
}

.panel-title {
    font-weight: 600;
    color: var(--text);
    display: flex;
    align-items: center;
    gap: 8px;
}

.panel-drag-handle {
    cursor: grab;
    color: var(--text-muted);
    margin-right: 8px;
}

.panel-actions {
    display: flex;
    gap: 4px;
}

.panel-action-btn {
    width: 28px;
    height: 28px;
    border: none;
    background: transparent;
    color: var(--text-muted);
    cursor: pointer;
    border-radius: 6px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 14px;
}

.panel-action-btn:hover {
    background: var(--glass-bg);
    color: var(--text);
}

.panel-content {
    padding: 16px;
    min-height: 150px;
}
```

- [ ] **Step 4: 添加响应式样式**

```css
@media (max-width: 1024px) {
    .sidebar {
        width: 180px;
        min-width: 160px;
    }
}

@media (max-width: 768px) {
    .app-content {
        flex-direction: column;
    }
    
    .sidebar {
        width: 100%;
        max-height: 200px;
        border-right: none;
        border-bottom: 1px solid var(--glass-border);
    }
    
    .kanban-container {
        grid-template-columns: 1fr;
    }
}
```

---

### Task 3: 实现 JavaScript 逻辑 - 布局管理

**文件:**
- 修改: `/sessions/69fcc95fa3baa9b124417137/workspace/index.html`

- [ ] **Step 1: 添加布局数据结构**

在 setup() 中添加：

```javascript
// 布局状态
const panelLayout = ref({
    competitor: {
        panels: [
            { id: 'case-library', title: '📚 案例库', collapsed: false, width: '100%' },
            { id: 'add-game', title: '➕ 添加游戏', collapsed: false, width: '50%' },
            { id: 'comparison', title: '📊 对比分析', collapsed: false, width: '100%' },
            { id: 'radar-chart', title: '🎯 雷达图', collapsed: false, width: '100%' }
        ]
    },
    persona: {
        panels: [
            { id: 'template-select', title: '📋 模板选择', collapsed: false, width: '50%' },
            { id: 'basic-info', title: '👤 基本信息', collapsed: false, width: '50%' },
            { id: 'game-habit', title: '🎮 游戏习惯', collapsed: false, width: '100%' }
        ]
    },
    interview: {
        panels: [
            { id: 'guide-templates', title: '📝 访谈提纲', collapsed: false, width: '50%' },
            { id: 'interview-records', title: '💬 访谈记录', collapsed: false, width: '50%' },
            { id: 'insights', title: '💡 洞察提炼', collapsed: false, width: '100%' }
        ]
    },
    questionnaire: {
        panels: [
            { id: 'q-basic', title: '📄 基本信息', collapsed: false, width: '50%' },
            { id: 'q-templates', title: '📚 模板库', collapsed: false, width: '50%' },
            { id: 'q-questions', title: '❓ 题目列表', collapsed: false, width: '100%' }
        ]
    },
    analysis: {
        panels: [
            { id: 'data-upload', title: '📤 数据上传', collapsed: false, width: '50%' },
            { id: 'statistics', title: '📈 数据统计', collapsed: false, width: '100%' }
        ]
    },
    visualization: {
        panels: [
            { id: 'radar', title: '🎯 竞品雷达图', collapsed: false, width: '100%' },
            { id: 'funnel', title: '🔮 AARRR漏斗', collapsed: false, width: '50%' },
            { id: 'wordcloud', title: '☁️ 词云', collapsed: false, width: '50%' }
        ]
    },
    report: {
        panels: [
            { id: 'report-config', title: '⚙️ 报告配置', collapsed: false, width: '100%' },
            { id: 'report-preview', title: '👁 报告预览', collapsed: false, width: '100%' }
        ]
    }
});

// 拖拽状态
const isDragging = ref(null);
const dragOverPanel = ref(null);
```

- [ ] **Step 2: 添加布局操作方法**

```javascript
// 获取当前 Tab 的面板
const getCurrentPanels = () => {
    const tab = activeTab.value;
    return panelLayout.value[tab]?.panels || [];
};

// 切换 Tab
const switchTab = (tabId) => {
    activeTab.value = tabId;
    saveLayout();
};

// 切换面板折叠
const togglePanelCollapse = (panelId) => {
    const panels = getCurrentPanels();
    const panel = panels.find(p => p.id === panelId);
    if (panel) {
        panel.collapsed = !panel.collapsed;
        saveLayout();
    }
};

// 重置面板大小
const resetPanelSize = (panelId) => {
    const panels = getCurrentPanels();
    const panel = panels.find(p => p.id === panelId);
    if (panel) {
        panel.width = '100%';
        saveLayout();
    }
};

// 删除面板
const deletePanel = (panelId) => {
    if (confirm('确定要删除这个面板吗？')) {
        const panels = getCurrentPanels();
        const index = panels.findIndex(p => p.id === panelId);
        if (index > -1) {
            panels.splice(index, 1);
            saveLayout();
        }
    }
};

// 添加新面板
const addNewPanel = () => {
    const panels = getCurrentPanels();
    const newId = 'panel-' + Date.now();
    panels.push({
        id: newId,
        title: '📋 新面板',
        collapsed: false,
        width: '50%'
    });
    saveLayout();
    showToast('已添加新面板');
};
```

- [ ] **Step 3: 实现拖拽功能**

```javascript
// 开始拖拽
const onDragStart = (e, panel) => {
    isDragging.value = panel.id;
    e.dataTransfer.effectAllowed = 'move';
    e.dataTransfer.setData('text/plain', panel.id);
};

// 拖拽经过
const onDragOver = (e, targetPanel) => {
    e.preventDefault();
    if (isDragging.value !== targetPanel.id) {
        dragOverPanel.value = targetPanel.id;
    }
};

// 放置面板
const onDrop = (e, targetPanel) => {
    e.preventDefault();
    const panelId = e.dataTransfer.getData('text/plain');
    if (panelId && panelId !== targetPanel.id) {
        const panels = getCurrentPanels();
        const dragIndex = panels.findIndex(p => p.id === panelId);
        const targetIndex = panels.findIndex(p => p.id === targetPanel.id);
        if (dragIndex > -1 && targetIndex > -1) {
            const [draggedPanel] = panels.splice(dragIndex, 1);
            panels.splice(targetIndex, 0, draggedPanel);
            saveLayout();
        }
    }
    isDragging.value = null;
    dragOverPanel.value = null;
};

// 拖拽结束
const onDragEnd = () => {
    isDragging.value = null;
    dragOverPanel.value = null;
};
```

- [ ] **Step 4: 实现布局保存/加载**

```javascript
// 保存布局
const saveLayout = () => {
    try {
        localStorage.setItem('panelLayout', JSON.stringify(panelLayout.value));
    } catch (e) {
        console.error('保存布局失败:', e);
    }
};

// 加载布局
const loadLayout = () => {
    try {
        const saved = localStorage.getItem('panelLayout');
        if (saved) {
            const parsed = JSON.parse(saved);
            // 合并保存的布局，保留默认面板结构
            Object.keys(parsed).forEach(tab => {
                if (panelLayout.value[tab]) {
                    // 更新已有面板的折叠状态
                    parsed[tab].panels.forEach(savedPanel => {
                        const defaultPanel = panelLayout.value[tab].panels.find(p => p.id === savedPanel.id);
                        if (defaultPanel) {
                            defaultPanel.collapsed = savedPanel.collapsed;
                        }
                    });
                }
            });
        }
    } catch (e) {
        console.error('加载布局失败:', e);
    }
};

// 初始化时加载布局
onMounted(() => {
    loadLayout();
});

// 监听布局变化自动保存
watch(panelLayout, () => {
    saveLayout();
}, { deep: true });
```

- [ ] **Step 5: 添加侧边栏数据**

```javascript
// 获取侧边栏内容
const getSidebarSections = (tabId) => {
    const sectionsMap = {
        competitor: [
            { id: 'default', name: '📦 默认模板', collapsed: false, items: [
                { id: 'case-template', name: '案例库模板' }
            ]},
            { id: 'game-types', name: '🎮 游戏类型', collapsed: false, items: [
                { id: 'gacha', name: '二次元/卡牌' },
                { id: 'moba', name: 'MOBA/竞技' },
                { id: 'casual', name: '休闲/派对' },
                { id: 'slg', name: 'SLG/策略' },
                { id: 'rpg', name: 'RPG/MMO' },
                { id: 'shooter', name: '射击/动作' }
            ]},
            { id: 'custom', name: '✨ 自定义', collapsed: false, items: [] }
        ],
        persona: [
            { id: 'persona-templates', name: '📋 画像模板', collapsed: false, items: [
                { id: 'casual-player', name: '休闲玩家' },
                { id: 'hardcore-player', name: '硬核玩家' },
                { id: 'social-player', name: '社交型玩家' },
                { id: 'competitive-player', name: '竞技型玩家' }
            ]},
            { id: 'player-type', name: '🎯 玩家类型', collapsed: false, items: [
                { id: 'achievement', name: '成就型' },
                { id: 'explorer', name: '探索型' },
                { id: 'social', name: '社交型' },
                { id: 'killer', name: '杀手型' }
            ]}
        ],
        interview: [
            { id: 'guide-templates', name: '📝 提纲模板', collapsed: false, items: [
                { id: 'game-habit', name: '游戏习惯访谈' },
                { id: 'payment', name: '付费行为访谈' },
                { id: 'experience', name: '玩家体验访谈' }
            ]}
        ],
        questionnaire: [
            { id: 'q-templates', name: '📚 问卷模板', collapsed: false, items: [
                { id: 'player-type-test', name: '玩家类型测试' },
                { id: 'experience-satisfaction', name: '体验满意度' },
                { id: 'cognitive-stage', name: '认知阶段' },
                { id: 'motivation', name: '动机分析' },
                { id: 'pain-point', name: '痛点分析' },
                { id: 'comprehensive', name: '综合调研' }
            ]}
        ],
        analysis: [
            { id: 'upload-templates', name: '📤 数据模板', collapsed: false, items: [
                { id: 'demographic', name: '人口统计数据' },
                { id: 'game-behavior', name: '游戏行为数据' },
                { id: 'spending', name: '消费行为数据' }
            ]}
        ],
        visualization: [
            { id: 'chart-types', name: '📊 图表类型', collapsed: false, items: [
                { id: 'radar', name: '雷达图' },
                { id: 'funnel', name: '漏斗图' },
                { id: 'pie', name: '饼图' },
                { id: 'bar', name: '柱状图' },
                { id: 'line', name: '折线图' },
                { id: 'wordcloud', name: '词云图' }
            ]}
        ],
        report: [
            { id: 'report-templates', name: '📄 报告模板', collapsed: false, items: [
                { id: 'competitor-report', name: '竞品分析报告' },
                { id: 'persona-report', name: '用户画像报告' },
                { id: 'comprehensive-report', name: '综合研究报告' }
            ]}
        ]
    };
    return sectionsMap[tabId] || [];
};

// 切换侧边栏分组展开/收起
const toggleSection = (sectionId) => {
    // 实现展开/收起逻辑
};

// 加载模板
const loadTemplate = (item) => {
    showToast('加载模板: ' + item.name);
    // 实现模板加载逻辑
};
```

- [ ] **Step 6: 更新 return 语句**

在 setup() 的 return 中添加：

```javascript
return {
    // ... existing
    panelLayout,
    isDragging,
    getCurrentPanels,
    switchTab,
    togglePanelCollapse,
    resetPanelSize,
    deletePanel,
    addNewPanel,
    onDragStart,
    onDragOver,
    onDrop,
    onDragEnd,
    getSidebarSections,
    toggleSection,
    loadTemplate
};
```

---

### Task 4: 清理旧代码

**文件:**
- 修改: `/sessions/69fcc95fa3baa9b124417137/workspace/index.html`

- [ ] **Step 1: 移除旧的模块容器占位符**

删除以下旧的占位符结构（如果存在）：
- `<!-- 竞品分析模块 -->`
- `<!-- 用户画像模块 -->`
- 等等

这些内容将整合到新的面板系统中。

- [ ] **Step 2: 更新 Tab 按钮绑定**

确保 `tabs` 数组包含正确的 icon：

```javascript
const tabs = [
    { id: 'competitor', name: '竞品分析', icon: '📊' },
    { id: 'persona', name: '用户画像', icon: '👤' },
    { id: 'interview', name: '用户访谈', icon: '💬' },
    { id: 'questionnaire', name: '问卷设计', icon: '📝' },
    { id: 'analysis', name: '数据分析', icon: '📈' },
    { id: 'visualization', name: '可视化中心', icon: '🎨' },
    { id: 'report', name: '报告生成', icon: '📄' }
];
```

---

### Task 5: 测试与调试

- [ ] **Step 1: 检查布局是否正确显示**
- [ ] **Step 2: 测试 Tab 切换**
- [ ] **Step 3: 测试侧边栏展开/收起**
- [ ] **Step 4: 测试面板拖拽排序**
- [ ] **Step 5: 测试面板折叠/展开**
- [ ] **Step 6: 测试面板删除/添加**
- [ ] **Step 7: 测试布局保存/加载**
- [ ] **Step 8: 测试响应式布局**

---

### Task 6: 提交并发布新版本

- [ ] **Step 1: 提交代码**

```bash
git add index.html
git commit -m "feat: 重构为三栏看板布局 v3.0

作者: 雷杰茗

布局重构:
- 添加顶部标题栏 + 工具栏
- 添加 Tab 导航栏
- 添加侧边栏模板库
- 实现看板式面板系统
- 支持拖拽/折叠/增删/调整大小
- 布局自动保存到 localStorage

BREAKING CHANGE: 布局结构重大变更"
```

- [ ] **Step 2: 推送到 GitHub**

```bash
git push origin master
```

- [ ] **Step 3: 创建 Release**

```bash
# 创建 v3.0.0 tag
git tag -a v3.0.0 -m "feat: 三栏看板布局

- 重构为三栏布局
- 添加侧边栏模板库
- 实现看板式面板系统
- 支持拖拽/折叠/增删

作者: 雷杰茗"
git push origin v3.0.0
```

---

## 验收标准

- [ ] 三栏布局正常显示
- [ ] Tab 切换正常工作
- [ ] 侧边栏模板列表可点击
- [ ] 面板可拖拽调整位置
- [ ] 面板可折叠/展开
- [ ] 面板可删除/添加
- [ ] 布局保存到 localStorage
- [ ] 刷新页面布局保持
- [ ] 移动端响应式正常

---

## 实现顺序

1. Task 1: HTML 结构重构
2. Task 2: CSS 样式
3. Task 3: JavaScript 逻辑
4. Task 4: 清理旧代码
5. Task 5: 测试调试
6. Task 6: 提交发布

**Plan Created**: 2025-05-08  
**Total Tasks**: 6 阶段，约 15 步