非常棒！你的 `import` 语句写得完全正确。这说明你已经抓住了 Vue 3 组合式 API（Hook）跨文件调用的核心思想。

针对你的问题，我的明确建议是：**先不要直接替换，我们继续拆分（继续“破”）**。

### 为什么现在不能直接替换？

如果你现在就把 `TableDataViewer.vue` 里的核心逻辑删掉并替换成这个 Hook，你的页面会立刻报错崩溃。

因为你 Vue 文件里剩下的**网络请求 (`fetchData`)** 和 **提交逻辑 (`handleSubmit`)**，它们内部都强依赖着 `tableData` 和 `diffPool`。我们现在只是把数据引擎搬出去了，还得把负责“搬运数据”的卡车（网络通信）也一起搬出去，并在外部让它们对接好。

---

要点：为什么传给后端只传主键 ,前端判断是否为自增列拒绝操作

为什么拆分，重点关注高内聚低耦合

### 下一步：抽离第二块核心 —— 网络与同步层

**任务**：在 `src/composables/` 目录下新建第二个文件：**`useTableSync.js`**。

这个文件的职责极其单一：**只负责和后端 Spring Boot 说话**。它需要从你刚才写的 `useTableDiff` 中借用数据池（`diffCore`），然后负责数据的拉取和提交。

请将以下代码放入 `useTableSync.js`：

JavaScript

```js
import { ref } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import axios from 'axios'

// 注意看参数：它需要接收 Vue 组件的 props 和 第一步抽离的核心差异引擎 diffCore
export function useTableSync(props, diffCore) {
    const loading = ref(false)
    const isSubmitting = ref(false)

    // ================= 1. 获取数据 =================
    const fetchData = async () => {
        if (!props.params.tableName) {
            ElMessage.warning('未指定表名')
            return
        }
        loading.value = true
        try {
            const res = await axios.get('http://localhost:8080/api/metadata/table/data', {
                headers: { 'Authorization': props.params.token || '' },
                params: { tableName: props.params.tableName, schema: props.params.schema }
            })
            if (res.data.code === 200) {
                const resultData = res.data.data
                // 🌟 核心关联点：把拿到的数据喂给 diffCore 去初始化
                diffCore.initData(resultData.columns, resultData.rows)
                ElMessage.success('查询成功！')
            }
        } catch (error) {
            console.error(error)
            ElMessage.error('获取数据失败')
        } finally {
            loading.value = false
        }
    }

    const handleRefresh = () => {
        if (diffCore.hasChanges.value) {
            ElMessageBox.confirm(
                '您有未提交的更改，刷新将丢失所有修改，是否继续刷新？',
                '警告',
                { confirmButtonText: '确定丢失', cancelButtonText: '取消', type: 'warning' }
            ).then(() => fetchData()).catch(() => {})
        } else {
            fetchData()
        }
    }

    // ================= 2. 提交数据 =================
    const handleSubmit = async () => {
        if (!diffCore.hasChanges.value) return
        isSubmitting.value = true
        try {
            let pkColumnName = []
            let identityColumnNames = []

            if (props.params.columns && Array.isArray(props.params.columns)) {
                pkColumnName = props.params.columns.filter(c => c.primarykey === true).map(c => c.name)
                identityColumnNames = props.params.columns.filter(c => c.type.toLowerCase().includes('identity')).map(c => c.name)
            }

            // --- 组装更新的数据 ---
            const updatedList = []
            diffCore.diffPool.updated.forEach(rowId => {
                const newRow = diffCore.tableData.value.find(r => r.__row_id === rowId)
                const oldRow = diffCore.originalData.value.find(r => r.__row_id === rowId)
                if (newRow && oldRow) {
                    const cleanedNewRow = diffCore.cleanRow(newRow)
                    const cleanedOldRow = diffCore.cleanRow(oldRow)
                    let finalOldRow = {}
                    if (pkColumnName.length > 0) {
                        pkColumnName.forEach(pk => finalOldRow[pk] = cleanedOldRow[pk])
                    } else {
                        finalOldRow = cleanedOldRow
                    }

                    identityColumnNames.forEach(idCol => delete cleanedNewRow[idCol])
                    updatedList.push({ oldRow: finalOldRow, newRow: cleanedNewRow })
                }
            })

            // --- 组装新增的数据 ---
            const insertedList = diffCore.tableData.value
                .filter(row => diffCore.diffPool.inserted.has(row.__row_id))
                .map(row => {
                    const cleanedRow = diffCore.cleanRow(row)
                    identityColumnNames.forEach(idCol => delete cleanedRow[idCol])
                    return cleanedRow
                })

            // --- 组装删除的数据 ---
            const deletedList = diffCore.originalData.value
                .filter(row => diffCore.diffPool.deleted.has(row.__row_id))
                .map(row => {
                    const cleanedRow = diffCore.cleanRow(row)
                    let finalDeletedRow = {}
                    if (pkColumnName.length > 0) {
                        pkColumnName.forEach(pk => finalDeletedRow[pk] = cleanedRow[pk])
                    } else {
                        finalDeletedRow = cleanedRow
                    }
                    return finalDeletedRow
                })

            const payload = {
                tableName: props.params.tableName,
                schema: props.params.schema,
                inserted: insertedList,
                updated: updatedList,
                deleted: deletedList
            }

            const res = await axios.post('http://localhost:8080/api/metadata/submit/sync', payload, {
                headers: { 'Authorization': props.params.token || '' }
            })

            if (res.data.code === 200) {
                ElMessage.success(`提交成功，共影响${diffCore.totalChanges.value}行数据`)
                await fetchData()
            } else {
                ElMessage.error('提交失败：' + (res.data.msg || '未知错误'))
            }
        } catch (error) {
            console.error(error)
        } finally {
            isSubmitting.value = false
        }
    }

    return { loading, isSubmitting, fetchData, handleRefresh, handleSubmit }
}
```

现在，网络请求层也独立出去了，而且通过依赖注入（`diffCore` 作为参数）与你的数据层产生了完美的联动。

创建好 `useTableSync.js` 后，你准备好剥离最后一部分的 UI 交互状态（`useTableUI.js`），然后迎接最终的“积木拼装”了吗？