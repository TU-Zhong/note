没问题，自己亲自动手重构（Refactoring）是真正掌握 Vue 3 组合式 API（Composition API）精髓的最佳途径。之前代码全量覆盖容易导致上下文断层，排错极其困难。

现在，我们按照**“守破离”**的原则，摒弃黑盒复制，一步一步来拆解。

### 第一阶段：守（理清代码边界）

在动手建文件之前，先审视你现在的 `TableDataViewer.vue` 的 `<script setup>`。它虽然臃肿，但逻辑可以清晰地划分为三大阵营：

1. **核心差异引擎（Diff Engine）**：负责记录谁被新增了、谁被修改了、谁被删除了。包含 `diffPool`、`originalData`、生成虚拟 ID 以及比对差异的逻辑。
    
2. **网络与同步（Sync）**：负责和后端打交道。包含 `fetchData` 获取数据，以及最复杂的 `handleSubmit` 组装 payload 提交数据。
    
3. **视图与交互（UI & View）**：负责页面怎么亮、输入框什么时候出来。包含过滤搜索、高亮选中行（`selectedCell`）、编辑模式（`editingCell`）和动态 Class。
    

### 第二阶段：破（抽离第一个 Hook - 核心差异引擎）

我们先拿最独立、最底层的**核心差异引擎**开刀。

**任务 1**：在你的项目里新建一个文件夹 `src/composables`（如果有就不用建了），然后在里面新建一个空文件：**`useTableDiff.js`**。

**任务 2**：将 `TableDataViewer.vue` 中属于“差异引擎”的状态和方法**剪切**（或复制）到这个新文件中。你的 `useTableDiff.js` 应该写成这样：

JavaScript

```
import { ref, reactive, computed } from 'vue'

export function useTableDiff() {
  // 1. 基础数据容器
  const tableData = ref([])
  const tableColumns = ref([])
  const originalData = ref([])

  // 2. 差异追踪池
  const diffPool = reactive({
    inserted: new Set(),
    updated: new Set(),
    deleted: new Set()
  })

  // 3. 计算属性
  const hasChanges = computed(() => diffPool.inserted.size > 0 || diffPool.updated.size > 0 || diffPool.deleted.size > 0)
  const totalChanges = computed(() => diffPool.inserted.size + diffPool.updated.size + diffPool.deleted.size)

  // 4. 工具方法：生成前端虚拟主键
  const generateRowId = () => `row_${Date.now()}_${Math.floor(Math.random() * 10000)}`

  // 5. 数据操作方法 (从你原来的代码中提取)
  const addRow = () => {
    const newRow = { __row_id: generateRowId() }
    tableColumns.value.forEach(col => { newRow[col] = null })
    tableData.value.unshift(newRow)
    diffPool.inserted.add(newRow.__row_id)
  }

  const deleteRow = (rowId) => {
    if (diffPool.inserted.has(rowId)) {
      const index = tableData.value.findIndex(r => r.__row_id === rowId)
      if (index !== -1) tableData.value.splice(index, 1)
      diffPool.inserted.delete(rowId)
    } else {
      diffPool.deleted.add(rowId)
    }
  }

  // 工具方法：丢掉前端的行唯一标识 (提交时用)
  const cleanRow = (row) => {
    const cleanData = { ...row }
    delete cleanData.__row_id
    return cleanData
  }

  // 🌟 必须将外部需要用到的变量和方法 return 出去
  return {
    tableData, tableColumns, originalData, diffPool,
    hasChanges, totalChanges,
    generateRowId, addRow, deleteRow, cleanRow
  }
}
```

---

这是重构的第一步，我们先不动另外两个模块（网络和 UI）。

你先完成这第一步文件的创建和代码转移。**当你把这段代码放进 `useTableDiff.js` 后，能否告诉我，你打算如何在 `TableDataViewer.vue` 中引入并使用这个 Hook 里的 `tableData` 和 `addRow` 方法？** 我们确认你理解了挂载方式后，再进行下一步的网络层剥离。