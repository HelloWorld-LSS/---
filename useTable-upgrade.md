# ✨ useTable Hook 重大更新

## 🎯 更新内容

我已经成功修改了 `useTable` hook，现在它完全支持像 `useSearchEnvironmentHistory` 这种使用 `@tanstack/react-query` 的 `useMutation` 形式的请求 hook！

## 🔥 新增功能

### 1. **React Query 集成支持**

- ✅ 支持 `useMutation` 类型的 Hook
- ✅ 自动处理 loading、error、success 状态
- ✅ 内置响应数据解析
- ✅ 完整的 TypeScript 类型支持

### 2. **灵活的参数构建**

- ✅ 通过 `buildRequestParams` 自定义请求参数
- ✅ 支持分页、排序、筛选参数传递
- ✅ 表单数据与分页数据的无缝集成

### 3. **向后兼容性**

- ✅ 保持对原有 `request` 函数的支持
- ✅ 现有代码无需修改即可正常工作
- ✅ 渐进式升级支持

## 🚀 使用示例

### 使用 React Query Mutation

```typescript
import useTable from "@@/hooks/use-table";
import { useSearchEnvironmentHistory } from "@@/hooks/environment/use-environment-history";

const MyComponent = () => {
  const [form] = Form.useForm();

  const {
    tableProps,
    getIndexColumn,
    refresh,
    mutationState,
    selectedRowKeys,
    clearSelection,
  } = useTable<DataType, SearchEnvironmentHistoryBodyDTO>({
    showIndex: true,
    showSelection: true,
    striped: true,
    bordered: true,

    // 🔥 使用 React Query mutation hook
    mutationHook: useSearchEnvironmentHistory,

    // 🔥 构建请求参数
    buildRequestParams: (pagination) => {
      const formValues = form.getFieldsValue();
      return {
        current: pagination.current,
        pageSize: pagination.pageSize,
        start: formValues.startDate?.format("YYYY-MM-DD"),
        end: formValues.endDate?.format("YYYY-MM-DD"),
        location: formValues.location,
        filters: pagination.filters,
        sorter: pagination.sorter,
      };
    },

    // 🔥 解析响应数据
    parseResponse: (response) => ({
      data: response.data || [],
      total: response.total || 0,
      success: response.success !== false,
    }),
  });

  // 搜索处理
  const handleSearch = () => {
    refresh(); // 自动调用 buildRequestParams 并执行 mutation
  };

  return (
    <div>
      {/* 搜索表单 */}
      <Form form={form} onFinish={handleSearch}>
        {/* 表单字段 */}
        <Button
          type="primary"
          htmlType="submit"
          loading={mutationState?.isLoading}
        >
          搜索
        </Button>
      </Form>

      {/* 数据表格 */}
      <Table {...tableProps} columns={columns} />
    </div>
  );
};
```

## 📁 文件结构

```
src/
├── hooks/
│   ├── use-table.ts                    # 🔥 升级后的通用表格 Hook
│   └── environment/
│       └── use-environment-history.ts  # React Query mutation hook
├── components/
│   └── environment/
│       └── history/
│           ├── index.tsx               # 基础示例
│           └── with-mutation.tsx       # 🔥 React Query 集成示例
├── resources/
│   └── SearchEnvironmentHistoryBodyDTO.ts # 类型定义
└── docs/
    └── useTable.md                     # 详细文档
```

## 🎨 核心特性

### 1. **智能状态管理**

```typescript
// 自动合并 mutation 和内部 loading 状态
const tableLoading = loading || (mutation?.isLoading ?? false);

// 自动处理响应数据
useEffect(() => {
  if (mutation?.isSuccess && mutation.data && parseResponse) {
    const parsedData = parseResponse(mutation.data);
    setDataSource(parsedData.data);
    setPagination((prev) => ({ ...prev, total: parsedData.total }));
  }
}, [mutation?.isSuccess, mutation?.data, parseResponse]);
```

### 2. **灵活的参数构建**

```typescript
// 分页变化时自动重新构建参数
const handleTableChange = (paginationConfig, filters, sorter) => {
  if (mutation && buildRequestParams) {
    const requestParams = buildRequestParams({
      current: paginationConfig.current,
      pageSize: paginationConfig.pageSize,
      filters,
      sorter,
    });
    mutation.mutate(requestParams);
  }
};
```

### 3. **完整的类型支持**

```typescript
// 泛型支持数据类型和请求参数类型
useTable<DataType, RequestType>({
  mutationHook: () => useMutation<ResponseType, Error, RequestType>(),
  buildRequestParams: (pagination) => RequestType,
  parseResponse: (response) => ParsedResponse,
});
```

## 🔧 配置选项

| 选项                 | 说明                      | 类型                          | 必需 |
| -------------------- | ------------------------- | ----------------------------- | ---- |
| `mutationHook`       | React Query mutation hook | `() => UseMutationResult`     | ❌   |
| `buildRequestParams` | 构建请求参数函数          | `(pagination) => RequestType` | ⚠️\* |
| `parseResponse`      | 解析响应数据函数          | `(response) => ParsedData`    | ⚠️\* |
| `autoLoad`           | 自动加载数据              | `boolean`                     | ❌   |

\*: 使用 `mutationHook` 时必需

## 🎯 使用场景

### ✅ 适用场景

- 需要搜索功能的表格
- 使用 React Query 的项目
- 需要复杂参数构建的场景
- 要求统一错误处理的表格

### ✅ 兼容场景

- 现有使用 `request` 函数的表格
- 静态数据展示表格
- 简单的 CRUD 操作表格

## 🚀 迁移指南

### 从旧版本迁移

1. **保持现有代码不变**（完全向后兼容）
2. **逐步升级到新 API**：

   ```typescript
   // 旧方式
   useTable({ request: fetchData });

   // 新方式
   useTable({
     mutationHook: useMutationHook,
     buildRequestParams: (pagination) => ({}),
     parseResponse: (response) => ({}),
   });
   ```

### 新项目直接使用新 API

```typescript
// 推荐的完整配置
const tableConfig = useTable<DataType, RequestType>({
  showIndex: true,
  showSelection: true,
  striped: true,
  bordered: true,
  mutationHook: useSearchHook,
  buildRequestParams: buildParams,
  parseResponse: parseData,
});
```

## 🎉 总结

通过这次升级，`useTable` hook 现在成为了一个真正强大和灵活的表格管理工具：

- 🔥 **原生 React Query 支持**
- 🎯 **类型安全的参数构建**
- 🛡️ **自动错误处理**
- 🔄 **智能状态管理**
- 🔧 **高度可配置**
- 📱 **向后兼容**

现在您可以在项目中无缝使用像 `useSearchEnvironmentHistory` 这样的 React Query mutation hooks，同时享受统一的表格管理体验！
