# useTable Hook 使用指南

这是一个专为 Ant Design Table 组件设计的通用 React Hook，提供了统一的表格配置和状态管理。**现已支持 React Query 的 useMutation Hook！**

## 功能特性

- ✅ **统一的表格配置**: 标准化的分页、排序、筛选配置
- ✅ **React Query 集成**: 支持 `useMutation` 类型的请求 Hook
- ✅ **行选择支持**: 内置多选、全选功能
- ✅ **序号列**: 自动添加序号列，支持跨页计算
- ✅ **操作列**: 灵活的操作按钮配置
- ✅ **数据操作**: 增删改查操作的封装
- ✅ **加载状态**: 内置 loading 状态管理
- ✅ **样式定制**: 支持斑马纹、边框等样式配置
- ✅ **TypeScript**: 完整的类型支持
- ✅ **向后兼容**: 保持对旧版本 API 的支持

## 新特性：React Query 集成

### 1. 使用 useMutation Hook

```typescript
import useTable from "@@/hooks/use-table";
import { useSearchEnvironmentHistory } from "@@/hooks/environment/use-environment-history";
import type { SearchEnvironmentHistoryBodyDTO } from "@@/resources/SearchEnvironmentHistoryBodyDTO";

const { tableProps, refresh, mutationState } = useTable<
  DataType,
  SearchEnvironmentHistoryBodyDTO
>({
  // 使用 React Query mutation hook
  mutationHook: useSearchEnvironmentHistory,

  // 构建请求参数
  buildRequestParams: (pagination) => ({
    current: pagination.current,
    pageSize: pagination.pageSize,
    // 其他搜索参数...
  }),

  // 解析响应数据
  parseResponse: (response) => ({
    data: response.data,
    total: response.total,
    success: response.success,
  }),
});
```

### 2. mutation 状态监控

```typescript
const { mutationState } = useTable({...})

// 访问 mutation 状态
if (mutationState?.isLoading) {
  // 显示加载状态
}

if (mutationState?.isError) {
  // 处理错误
  console.error(mutationState.error)
}

if (mutationState?.isSuccess) {
  // 处理成功
}
```

## 基础用法

### 1. 导入 Hook

```typescript
import useTable from "../hooks/use-table";
import type { UserData } from "../types/table";
```

### 2. 基础配置

```typescript
const {
  tableProps,
  getIndexColumn,
  getActionColumn,
  selectedRows,
  selectedRowKeys,
  removeData,
  clearSelection,
} = useTable<UserData>({
  initialData: mockData,
  showIndex: true, // 显示序号列
  showSelection: true, // 显示选择框
  striped: true, // 斑马纹
  bordered: true, // 边框
  size: "middle", // 表格大小
});
```

### 3. 定义表格列

```typescript
const columns = [
  getIndexColumn(), // 序号列
  {
    title: "姓名",
    dataIndex: "name",
    key: "name",
  },
  {
    title: "状态",
    dataIndex: "status",
    key: "status",
    render: (status) => (
      <Tag color={status === "active" ? "green" : "red"}>
        {status === "active" ? "活跃" : "非活跃"}
      </Tag>
    ),
  },
  getActionColumn({
    render: (record, index) => (
      <Space>
        <Button onClick={() => handleView(record)}>查看</Button>
        <Button onClick={() => handleEdit(record)}>编辑</Button>
        <Popconfirm
          title="确定删除吗？"
          onConfirm={() => removeData(record.id)}
        >
          <Button danger>删除</Button>
        </Popconfirm>
      </Space>
    ),
  }), // 操作列
];
```

### 4. 渲染表格

```typescript
<Table {...tableProps} columns={columns} />
```

## 高级用法

### 1. 远程数据加载

```typescript
const { tableProps, refresh } = useTable({
  request: async (params) => {
    const response = await fetchUsers(params);
    return {
      data: response.list,
      total: response.total,
      success: response.success,
    };
  },
});

// 刷新数据
const handleRefresh = () => {
  refresh();
};
```

### 2. 批量操作

```typescript
const { selectedRows, selectedRowKeys, removeData, clearSelection } = useTable({
  showSelection: true,
})

// 批量删除
const handleBatchDelete = () => {
  if (selectedRowKeys.length === 0) {
    message.warning('请选择要删除的数据')
    return
  }

  removeData(selectedRowKeys)
  message.success(`已删除 ${selectedRowKeys.length} 条数据`)
  clearSelection()
}

// 渲染批量操作按钮
<Card
  extra={
    <Space>
      {selectedRowKeys.length > 0 && (
        <Button danger onClick={handleBatchDelete}>
          批量删除 ({selectedRowKeys.length})
        </Button>
      )}
      <Button type="primary">添加</Button>
    </Space>
  }
>
```

### 3. 数据操作

```typescript
const { addData, updateData, removeData } = useTable();

// 添加数据
const handleAdd = (newRecord) => {
  addData(newRecord);
  message.success("添加成功");
};

// 更新数据
const handleUpdate = (id, updates) => {
  updateData(id, updates);
  message.success("更新成功");
};

// 删除数据
const handleDelete = (id) => {
  removeData(id);
  message.success("删除成功");
};
```

## API 参考

### UseTableOptions

| 参数              | 说明           | 类型                             | 默认值                                   |
| ----------------- | -------------- | -------------------------------- | ---------------------------------------- |
| initialData       | 初始数据       | `T[]`                            | `[]`                                     |
| initialPagination | 初始分页配置   | `object`                         | `{ current: 1, pageSize: 10, total: 0 }` |
| showIndex         | 是否显示序号列 | `boolean`                        | `false`                                  |
| showSelection     | 是否显示选择框 | `boolean`                        | `false`                                  |
| size              | 表格大小       | `'small' \| 'middle' \| 'large'` | `'middle'`                               |
| bordered          | 是否显示边框   | `boolean`                        | `false`                                  |
| striped           | 是否显示斑马纹 | `boolean`                        | `false`                                  |
| request           | 远程请求函数   | `function`                       | -                                        |

### UseTableReturn

| 参数            | 说明           | 类型                                         |
| --------------- | -------------- | -------------------------------------------- |
| dataSource      | 表格数据       | `T[]`                                        |
| loading         | 加载状态       | `boolean`                                    |
| pagination      | 分页配置       | `object`                                     |
| rowSelection    | 行选择配置     | `object`                                     |
| selectedRows    | 选中的行数据   | `T[]`                                        |
| selectedRowKeys | 选中的行键     | `React.Key[]`                                |
| tableProps      | 表格属性       | `TableProps<T>`                              |
| refresh         | 刷新数据       | `() => void`                                 |
| reset           | 重置表格       | `() => void`                                 |
| setDataSource   | 设置数据       | `(data: T[]) => void`                        |
| addData         | 添加数据       | `(data: T \| T[]) => void`                   |
| updateData      | 更新数据       | `(key: React.Key, data: Partial<T>) => void` |
| removeData      | 删除数据       | `(key: React.Key \| React.Key[]) => void`    |
| clearSelection  | 清空选择       | `() => void`                                 |
| getIndexColumn  | 获取序号列配置 | `() => TableColumnType<T>`                   |
| getActionColumn | 获取操作列配置 | `(actions) => TableColumnType<T>`            |

## 样式定制

在 CSS 中可以自定义斑马纹样式：

```css
/* 表格斑马纹样式 */
.table-row-striped {
  background-color: #fafafa;
}

.table-row-striped:hover {
  background-color: #e6f7ff !important;
}
```

## 最佳实践

1. **类型定义**: 为数据定义明确的 TypeScript 类型
2. **数据键值**: 确保每行数据有唯一的 `id` 或 `key` 字段
3. **操作确认**: 删除等危险操作使用 `Popconfirm` 组件确认
4. **加载状态**: 使用内置的 `loading` 状态提升用户体验
5. **错误处理**: 在数据操作中添加适当的错误处理
6. **性能优化**: 对于大量数据，考虑使用虚拟滚动或分页加载

## 示例代码

完整的使用示例请参考 `src/routes/index.tsx` 文件。
