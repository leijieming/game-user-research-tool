# 游戏设计用户研究工具 v3.0 - 三栏看板布局设计规格

**版本**: 3.0  
**日期**: 2025-05-08  
**作者**: 雷杰茗

---

## 一、设计目标

将工具从单栏 Tab 布局升级为三栏看板式布局，提供更高效的信息组织和编辑体验。

### 1.1 设计原则

- **效率优先**: 减少切换，快速访问常用功能
- **灵活布局**: 面板可自由拖拽、折叠、调整大小
- **视觉一致**: 保持 v2.1 的现代科技风格

---

## 二、整体布局

### 2.1 三栏布局结构

```
┌─────────────────────────────────────────────────────────────────┐
│  顶部标题栏（64px）                                             │
│  项目名称 + 工具栏（导入/导出/设置）                              │
├─────────────────────────────────────────────────────────────────┤
│  Tab 导航栏（48px）                                              │
│  竞品分析 | 用户画像 | 用户访谈 | 问卷设计 | 数据分析 | 报告生成   │
├────────────────┬────────────────────────────────────────────────┤
│                │                                                │
│  左侧边栏      │              右侧主内容区                         │
│  (220px)      │              (flex: 1)                        │
│                │                                                │
│  📂 模板库     │  看板式面板布局                                 │
│  ├─ 默认模板   │  可拖拽、可折叠、可增删、可调整大小               │
│  ├─ 游戏类型   │                                                │
│  └─ 自定义     │  ┌─────────────┬─────────────┬─────────────┐   │
│                │  │   面板 A    │   面板 B    │   面板 C    │   │
│  ➕ 添加面板    │  └─────────────┴─────────────┴─────────────┘   │
│                │                                                │
└────────────────┴────────────────────────────────────────────────┘
```

### 2.2 布局尺寸规范

| 区域 | 尺寸 | 最小宽度 |
|------|------|----------|
| 顶部标题栏 | 64px | 100% |
| Tab 导航栏 | 48px | 100% |
| 左侧边栏 | 220px | 180px |
| 右侧主内容区 | flex: 1 | 600px |

---

## 三、顶部标题栏

### 3.1 组件结构

```
┌──────────────────────────────────────────────────────────────────┐
│ 🔮 游戏设计用户研究工具 v3.0    [导入] [导出] [⚙ 设置]            │
└──────────────────────────────────────────────────────────────────┘
```

### 3.2 样式

```css
.header {
    height: 64px;
    background: rgba(0, 0, 0, 0.2);
    backdrop-filter: blur(10px);
    border-bottom: 1px solid var(--glass-border);
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 24px;
}

.header-title {
    font-size: 18px;
    font-weight: 700;
    background: var(--primary-gradient);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}

.header-actions {
    display: flex;
    gap: 12px;
}
```

---

## 四、Tab 导航栏

### 4.1 组件结构

```
┌──────────────────────────────────────────────────────────────────┐
│ [竞品分析] [用户画像] [用户访谈] [问卷设计] [数据分析] [报告生成]   │
└──────────────────────────────────────────────────────────────────┘
```

### 4.2 样式

```css
.tab-nav {
    height: 48px;
    background: rgba(0, 0, 0, 0.15);
    border-bottom: 1px solid var(--glass-border);
    display: flex;
    align-items: center;
    padding: 0 16px;
    gap: 4px;
}

.tab-btn {
    padding: 12px 20px;
    border: none;
    background: transparent;
    color: var(--text-muted);
    cursor: pointer;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 500;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 8px;
}

.tab-btn:hover {
    background: var(--glass-bg);
    color: var(--text-secondary);
}

.tab-btn.active {
    background: var(--primary-gradient);
    color: white;
}
```

---

## 五、左侧边栏

### 5.1 组件结构

```
┌─────────────────────┐
│ 📂 模板库            │
├─────────────────────┤
│ ▼ 默认模板           │
│   ├─ 竞品分析模板    │
│   └─ 用户画像模板    │
│ ▼ 游戏类型           │
│   ├─ 二次元/卡牌    │
│   ├─ MOBA/竞技     │
│   └─ ...           │
│ ▼ 自定义模板         │
│   ├─ 我的模板1      │
│   └─ ...           │
├─────────────────────┤
│ ➕ 添加面板          │
└─────────────────────┘
```

### 5.2 样式

```css
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

.sidebar-item {
    padding: 10px 16px 10px 24px;
    color: var(--text-secondary);
    font-size: 14px;
    cursor: pointer;
    transition: all 0.2s;
    display: flex;
    align-items: center;
    gap: 8px;
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
    padding: 12px 16px;
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

---

## 六、右侧看板面板

### 6.1 组件结构

```
┌──────────────────────────────────────────────────────────────────┐
│  面板工具栏：[折叠] [重置大小] [删除]                    [⋮⋮] │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│                        面板内容                                   │
│                                                                  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### 6.2 面板样式

```css
.kanban-container {
    flex: 1;
    padding: 20px;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
    gap: 16px;
    align-content: start;
    overflow-y: auto;
}

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

.panel.collapsed {
    min-height: auto;
}

.panel.collapsed .panel-content {
    display: none;
}

.panel.dragging {
    opacity: 0.5;
    transform: scale(1.02);
    box-shadow: var(--glow-spread);
}

.panel.resizing {
    resize: both;
    overflow: auto;
}

.panel-header {
    background: rgba(0, 0, 0, 0.2);
    padding: 12px 16px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    cursor: move;
    border-bottom: 1px solid var(--glass-border);
}

.panel-title {
    font-weight: 600;
    color: var(--text);
    display: flex;
    align-items: center;
    gap: 8px;
}

.panel-actions {
    display: flex;
    gap: 8px;
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
    transition: all 0.2s;
}

.panel-action-btn:hover {
    background: var(--glass-bg);
    color: var(--text);
}

.panel-drag-handle {
    cursor: grab;
    color: var(--text-muted);
}

.panel-content {
    padding: 16px;
    min-height: 150px;
}
```

### 6.3 拖拽功能

```javascript
// 拖拽状态
const draggingPanel = ref(null);
const dragOverPanel = ref(null);

// 开始拖拽
const onDragStart = (e, panel) => {
    draggingPanel.value = panel;
    e.dataTransfer.effectAllowed = 'move';
};

// 拖拽经过
const onDragOver = (e, targetPanel) => {
    e.preventDefault();
    dragOverPanel.value = targetPanel;
};

// 放置面板
const onDrop = (e, targetPanel) => {
    e.preventDefault();
    if (draggingPanel.value && draggingPanel.value.id !== targetPanel.id) {
        const panels = [...panelLayout.value.panels];
        const dragIndex = panels.findIndex(p => p.id === draggingPanel.value.id);
        const targetIndex = panels.findIndex(p => p.id === targetPanel.id);
        panels.splice(dragIndex, 1);
        panels.splice(targetIndex, 0, draggingPanel.value);
        panelLayout.value.panels = panels;
        saveLayout();
    }
    draggingPanel.value = null;
    dragOverPanel.value = null;
};

// 拖拽结束
const onDragEnd = () => {
    draggingPanel.value = null;
    dragOverPanel.value = null;
};
```

### 6.4 折叠功能

```javascript
const togglePanelCollapse = (panelId) => {
    const panel = panelLayout.value.panels.find(p => p.id === panelId);
    if (panel) {
        panel.collapsed = !panel.collapsed;
        saveLayout();
    }
};
```

### 6.5 删除功能

```javascript
const deletePanel = (panelId) => {
    if (confirm('确定要删除这个面板吗？')) {
        panelLayout.value.panels = panelLayout.value.panels.filter(p => p.id !== panelId);
        saveLayout();
    }
};
```

---

## 七、数据结构

### 7.1 布局数据结构

```javascript
const panelLayout = ref({
    tabs: {
        competitor: {
            panels: [
                { id: 'case-library', title: '案例库', collapsed: false, width: '100%' },
                { id: 'game-form', title: '添加游戏', collapsed: false, width: '50%' },
                { id: 'comparison-table', title: '对比分析', collapsed: false, width: '100%' }
            ]
        },
        persona: { ... },
        interview: { ... },
        questionnaire: { ... },
        analysis: { ... },
        report: { ... }
    },
    activeTab: 'competitor'
});
```

### 7.2 本地存储

```javascript
// 保存布局
const saveLayout = () => {
    localStorage.setItem('panelLayout', JSON.stringify(panelLayout.value));
};

// 加载布局
const loadLayout = () => {
    const saved = localStorage.getItem('panelLayout');
    if (saved) {
        panelLayout.value = JSON.parse(saved);
    }
};

// 监听变化自动保存
watch(panelLayout, (newVal) => {
    saveLayout();
}, { deep: true });
```

---

## 八、响应式设计

### 8.1 断点

| 断点 | 布局变化 |
|------|----------|
| < 768px | 侧边栏收起为抽屉模式 |
| 768px - 1024px | 侧边栏缩小为 180px |
| > 1024px | 完整三栏布局 |

### 8.2 移动端适配

```css
@media (max-width: 768px) {
    .sidebar {
        position: fixed;
        left: -220px;
        top: 112px;
        height: calc(100vh - 112px);
        z-index: 100;
        transition: left 0.3s ease;
    }
    
    .sidebar.open {
        left: 0;
    }
    
    .kanban-container {
        grid-template-columns: 1fr;
    }
}
```

---

## 九、动画效果

### 9.1 过渡动画

| 元素 | 动画 | 时长 |
|------|------|------|
| 面板折叠 | height + opacity | 0.3s |
| 面板拖拽 | transform + opacity | 0.2s |
| 侧边栏展开 | left | 0.3s |
| 面板悬停 | box-shadow + transform | 0.3s |

### 9.2 CSS 动画

```css
.panel {
    transition: all 0.3s ease;
}

.panel:hover {
    box-shadow: var(--glass-shadow), var(--glow-spread);
    transform: translateY(-2px);
}

.panel.collapsed .panel-header {
    border-bottom: none;
}
```

---

## 十、验收标准

- [ ] 三栏布局正常显示
- [ ] Tab 切换正常工作
- [ ] 侧边栏模板列表可点击
- [ ] 面板可拖拽调整位置
- [ ] 面板可折叠/展开
- [ ] 面板可删除
- [ ] 布局保存到 localStorage
- [ ] 刷新页面布局保持
- [ ] 移动端侧边栏收起为抽屉

---

**规格确认人**：雷杰茗  
**确认日期**：2025-05-08