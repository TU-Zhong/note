这是转换后的 **TypeScript** 版本。

我在转换过程中做了以下优化和修正：

1. **定义了接口 (Interfaces)**：明确了 `Tab` 和 `TabParams` 的结构，这样你在组件里使用 `tab.xxx` 时会有智能提示。
    
2. **修复了一个逻辑 Bug**：在 `addTab` 中，你原本写的是 `activeTabId.value = existingTab`（赋值了整个对象），TS 会直接报错，因为 `activeTabId` 是字符串。我将其修正为 `activeTabId.value = existingTab.id`。
    
3. **类型推导**：为 `ref` 增加了泛型 `<Tab[]>` 和 `<string>`。
    

### 📄 `src/stores/useWorkspaceStore.ts`

TypeScript

```ts
import { defineStore } from 'pinia'
import { ref } from 'vue'

// 1. 定义数据结构规范 (Interfaces)
// Params 可以根据需要扩展，比如 tableName, initSql 等
export interface TabParams {
    tableName?: string
    initSql?: string
    [key: string]: any // 允许其他任意属性
}

// Store 内部存储的完整 Tab 对象
export interface Tab {
    id: string
    title: string
    type: string // 如果类型固定，可以改为联合类型：'table' | 'console'
    params: TabParams
}

// addTab 函数接收的参数（允许 id 或 params 为空）
export interface AddTabPayload {
    id?: string
    title: string
    type: string
    params?: TabParams
}

export const useWorkspaceStore = defineStore('workspace', () => {
    // 2. State 类型标注
    const tabs = ref<Tab[]>([])
    const activeTabId = ref<string>('')

    // 3. Actions
    const addTab = (node: AddTabPayload) => {
        // 构造 ID：如果有传 id 就用，没有就按规则生成
        const tabId = node.id || `${node.type}_${node.title}`
        
        const existingTab = tabs.value.find(tab => tab.id === tabId)

        if (existingTab) {
            // 🐛 修复原逻辑 Bug: 
            // 原代码: activeTabId.value = existingTab (这是赋对象给字符串，TS 会报错)
            // 修正后: activeTabId.value = existingTab.id
            activeTabId.value = existingTab.id
        } else {
            // 推入新 Tab
            tabs.value.push({
                id: tabId,
                title: node.title,
                type: node.type,
                params: node.params || {} // 确保 params 至少是个空对象，不为 undefined
            })
            activeTabId.value = tabId
        }
    }

    const removeTab = (targetId: string) => {
        const tabsList = tabs.value
        let activeId = activeTabId.value

        if (activeId === targetId) {
            tabsList.forEach((tab, index) => {
                if (tab.id === targetId) {
                    const nextTab = tabsList[index + 1] || tabsList[index - 1]
                    if (nextTab) {
                        activeId = nextTab.id
                    } else {
                        // 如果删完了，置为空字符串，而不是 null/undefined，保持类型一致
                        activeId = '' 
                    }
                }
            })
        }

        activeTabId.value = activeId
        tabs.value = tabsList.filter(tab => tab.id !== targetId)
    }

    return {
        tabs,
        activeTabId,
        addTab,
        removeTab
    }
})
```

### 💡 如何在组件中使用？

TS 转换后，你的 `WorkArea.vue` 或其他组件可能会需要更新类型引入。例如，如果你想在组件里显式使用 Tab 类型：

TypeScript

```
import { useWorkspaceStore } from '@/stores/useWorkspaceStore'
import type { Tab } from '@/stores/useWorkspaceStore' // 引入类型

const store = useWorkspaceStore()
// store.tabs 现在会自动提示它是 Tab[] 类型
```

**下一步**：将这个文件保存为 `.ts` 后，你可以去 `MainLayout.vue` 或 `DbSidebar.vue` 检查一下调用 `store.addTab({...})` 的地方，看看 IDE 是否给出了友好的属性提示。如果有红线报错，通常是因为参数拼写错误或者缺少必填字段。