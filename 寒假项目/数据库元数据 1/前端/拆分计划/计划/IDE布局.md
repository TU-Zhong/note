从零开始组装是一个极好的学习过程，这能让你对**组件间的数据通信**有最直观的理解。既然“大脑”（Pinia Store）已经写好了，我们现在的任务是给它穿上“衣服”，并让各部位联动起来。

根据你的毕业设计规划，我们现在的目标是构建一个能根据 **Pinia 状态**动态变化的 IDE 布局。

---

### 📂 当前项目结构建议

在你的 `frontend/src` 目录下，请先手动建立以下空文件，我们接下来逐一填充：

- `src/layout/MainLayout.vue`（大框架）
    
- `src/components/database/DbSidebar.vue`（左侧导航）
    
- `src/components/WorkArea.vue`（右侧多标签工作区）
    

---

### 第一步：编写大框架 `src/layout/MainLayout.vue`

这个组件的作用是划定“地盘”，它不处理具体业务，只负责把侧边栏和工作区放好。

Code snippet

```
<template>
  <el-container class="layout-container">
    <el-header height="40px" class="layout-header">
      <div class="logo">🚀 Metadata Studio</div>
    </el-header>

    <el-container class="inner-container">
      <el-aside width="300px">
        <DbSidebar />
      </el-aside>

      <el-main>
        <WorkArea />
      </el-main>
    </el-container>
  </el-container>
</template>

<script setup>
import DbSidebar from '../components/database/DbSidebar.vue'
import WorkArea from '../components/WorkArea.vue'
</script>

<style scoped>
.layout-container { height: 100vh; background-color: #1e1e1e; }
.layout-header { background-color: #3c3f41; display: flex; align-items: center; border-bottom: 1px solid #2b2b2b; }
.logo { color: #a9b7c6; font-size: 14px; font-weight: bold; }
.inner-container { overflow: hidden; }
</style>
```

---

### 第二步：修改 `src/App.vue` 作为唯一入口

现在的 `App.vue` 应该非常干净，只负责挂载我们刚刚写的 `MainLayout`。

Code snippet

```
<template>
  <MainLayout />
</template>

<script setup>
import MainLayout from './layout/MainLayout.vue'
</script>

<style>
/* 全局样式重置 */
body { margin: 0; padding: 0; }
</style>
```

---

### 第三步：联动测试——编写 `src/components/database/DbSidebar.vue`

这里是“动作发起者”。我们要在这里调用 Pinia 的 `addTab` 方法。

Code snippet

```
<template>
  <div class="sidebar-content">
    <div class="toolbar">
      <el-icon @click="handleNewConsole"><VideoPlay /></el-icon>
    </div>
    
    <div class="tree-mock" @dblclick="handleOpenTable">
      📁 Student (双击我测试)
    </div>
  </div>
</template>

<script setup>
import { VideoPlay } from '@element-plus/icons-vue'
import { useWorkspaceStore } from '../../stores/useWorkspaceStore'

const store = useWorkspaceStore()

const handleNewConsole = () => {
  // 调用 Pinia 的 action
  store.addTab({ title: 'New Console', type: 'console' })
}

const handleOpenTable = () => {
  store.addTab({ title: 'Student', type: 'table' })
}
</script>

<style scoped>
.sidebar-content { background-color: #313335; height: 100%; color: #afb1b3; }
.toolbar { padding: 5px; border-bottom: 1px solid #2b2b2b; }
.tree-mock { padding: 10px; cursor: pointer; user-select: none; }
.tree-mock:hover { background-color: #2d2f30; }
</style>
```

---

### 第四步：联动测试——编写 `src/components/WorkArea.vue`

这里是“状态响应者”。它会通过计算属性实时盯着 Pinia 里的 `tabs` 数组。

Code snippet

```
<template>
  <div class="work-area">
    <el-tabs
      v-model="store.activeTabId"
      type="card"
      closable
      @tab-remove="store.removeTab"
      v-if="store.tabs.length > 0"
    >
      <el-tab-pane
        v-for="item in store.tabs"
        :key="item.id"
        :label="item.title"
        :name="item.id"
      >
        <div class="pane-content">
          <h3>当前是：{{ item.type }} 面板</h3>
          <p>ID: {{ item.id }}</p>
        </div>
      </el-tab-pane>
    </el-tabs>

    <el-empty v-else description="请从左侧开启控制台或查看表数据" />
  </div>
</template>

<script setup>
import { useWorkspaceStore } from '../stores/useWorkspaceStore'
const store = useWorkspaceStore()
</script>

<style scoped>
.work-area { height: 100%; background-color: #2b2b2b; color: #cfd3dc; }
:deep(.el-tabs__header) { margin: 0; background-color: #3c3f41; }
.pane-content { padding: 20px; }
</style>
```

---

### 🏁 组装完成后的效果

1. **App 启动**：页面一片漆黑（IDEA 风格），中间显示“请从左侧开启...”。
    
2. **点击左侧图标**：Pinia 数组多了一项，右侧**瞬间**弹出一个标签页，标题是 `New Console`。
    
3. **双击模拟节点**：右侧又多了一个 `Student` 标签页，且 `activeTabId` 自动切换到新页。
    
4. **关闭标签**：调用 `removeTab`，数组减少，激活项按照我们写的“接班人算法”自动跳转。
    

### 💡 你作为“从零开始”者的下一步：

既然这套**“Sidebar -> Pinia -> WorkArea”**的链路已经打通，你现在的任务就是把 `DbSidebar.vue` 里的模拟数据，替换成真正的 `el-tree` 树形组件，并调用你之前写好的后端 `/api/metadata/tree` 接口。

**你想让我带你把左侧真正的“数据库树”渲染出来，还是先去研究右侧如何根据不同 Tab 加载不同的内容（比如数据表格）？**