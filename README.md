# 智能房间控制系统

一个基于 React + TypeScript + Socket.IO 的现代化智能房间管理平台，提供实时环境监控、设备控制和数据分析功能。

## 🚀 快速开始

### 环境要求

- Node.js >= 16.0.0
- pnpm >= 8.0.0（推荐）或 npm

### 安装和启动

```bash
# 克隆项目
git clone <repository-url>
cd smart-websocket

# 安装依赖
pnpm install

# 启动开发服务器
pnpm run dev
```

访问 http://localhost:5173 开始使用

## 📱 核心功能

### 🏠 智能房间控制

**路径**: `/dashboard/smart/room`

- **多房间管理**: 支持多个房间的独立控制和监控
- **实时环境数据**: 温度、湿度、电压、电流、气压、海拔、光照强度
- **设备控制**: 灯光、空调、窗帘等智能设备的远程控制
- **房间订阅**: 基于 Socket.IO 的实时房间数据订阅机制
- **自动重连**: 网络中断后自动重新连接

### 📊 环境历史数据

**路径**: `/dashboard/environment/history`

- **数据搜索**: 按房间、时间范围、温湿度范围筛选历史数据
- **数据表格**: 支持分页、排序的环境数据展示
- **实时更新**: 基于 TanStack Query 的数据管理
- **动态房间选择**: 从后端 API 动态加载可用房间列表

### 🔧 数据管理

- **API 集成**: RESTful API 接口进行数据查询
- **分页支持**: 高效的数据分页和加载
- **类型安全**: 完整的 TypeScript 类型定义

## �️ 技术架构

### 前端技术栈

- **React 19**: 现代化的前端框架
- **TypeScript**: 类型安全的开发体验
- **TanStack Router**: 类型安全的路由管理
- **TanStack Query v5**: 服务器状态管理和数据缓存
- **Ant Design**: 企业级 UI 组件库
- **Socket.IO Client**: 实时双向通信
- **Vite**: 快速的构建工具

### 项目结构

```
src/
├── components/          # 可复用组件
│   └── environment/     # 环境相关组件
│       └── history/     # 环境历史数据组件
├── hooks/              # 自定义 React Hooks
│   ├── environment/    # 环境数据相关 hooks
│   └── use-table.ts   # 表格工具 hook
├── resources/          # TypeScript 类型定义
├── routes/            # 路由页面
│   ├── dashboard.smart.room.lazy.tsx    # 智能房间控制
│   ├── dashboard.environment.history.lazy.tsx  # 环境历史
│   └── dashboard.lazy.tsx               # 主仪表板
└── types/             # 通用类型定义
```

## 🔌 Socket.IO 集成

### 支持的事件

#### 房间管理

- `subscribe-room`: 订阅房间实时数据
- `unsubscribe-room`: 取消房间订阅
- `room-subscribed`: 房间订阅成功确认
- `room-unsubscribed`: 房间取消订阅确认

#### 设备控制

- `sensorData`: 接收实时传感器数据
- 设备状态变更事件

### 自动重连机制

- 网络中断时自动尝试重连
- 重连成功后自动重新订阅房间
- 连接状态实时显示

## 📡 API 接口

### 环境历史数据

**接口**: `POST /api/rooms/basic-info/search`

**请求参数**:

```typescript
interface SearchEnvironmentHistoryBodyDTO {
  page: number;
  size: number;
  roomId?: string;
  startDate?: string;
  endDate?: string;
  minTemperature?: number;
  maxTemperature?: number;
  minHumidity?: number;
  maxHumidity?: number;
}
```

**响应数据**:

```typescript
interface PaginatedRoomEnvironmentResponseDto {
  data: RoomEnvironmentResponseDTO[];
  meta: CommonMetaResponseDTO;
}
```

### 房间列表

**接口**: `POST /api/rooms/SearchRoom`

## 🎯 数据类型

### 房间环境数据

```typescript
interface RoomEnvironmentResponseDTO {
  temperature: number; // 温度 (°C)
  humidity: number; // 湿度 (%)
  lightIntensity: number; // 光照强度 (lux)
  voltage: number; // 电压 (V)
  current: number; // 电流 (A)
  airPressure: number; // 气压 (hPa)
  altitude: number; // 海拔 (m)
  remarks?: string; // 备注
  room: {
    id: string; // 房间ID
  };
}
```

### 房间信息

```typescript
interface RoomResponseDTO {
  id: string; // 房间ID
  name: string; // 房间名称
  description: string; // 房间描述
  location: string; // 房间位置
  status: string; // 房间状态
  deviceSerialNumber: string; // 设备序列号
  isActive: boolean; // 是否激活
  createdAt: string; // 创建时间
  updatedAt: string; // 更新时间
}
```

## 🔧 开发指南

### 添加新页面

1. 在 `src/routes/` 目录创建新的 `.lazy.tsx` 文件
2. 使用 TanStack Router 的 `createLazyFileRoute` 创建路由
3. 在主仪表板菜单中添加导航项

### 自定义 Hook

项目使用自定义 hooks 管理状态：

- `useSearchEnvironmentHistory`: 环境历史数据查询
- `useSearchEnvironmentRoom`: 房间数据查询
- `useTable`: 表格功能增强

### 样式和主题

- 使用 Ant Design 的主题系统
- 响应式设计适配不同屏幕尺寸
- 支持暗色主题切换

## 📝 构建和部署

```bash
# 构建生产版本
pnpm run build

# 预览生产构建
pnpm run preview

# 代码检查
pnpm run lint
```

## 🔍 故障排除

### 常见问题

1. **Socket.IO 连接失败**

   - 检查服务器是否启动
   - 确认防火墙设置
   - 查看控制台错误日志

2. **数据加载失败**

   - 检查 API 接口地址
   - 确认网络连接
   - 查看开发者工具网络面板

3. **类型错误**
   - 运行 `pnpm run lint` 检查代码
   - 确保类型定义文件完整

## 🚀 未来规划

- [ ] 移动端适配和 PWA 支持
- [ ] 更多图表和数据可视化
- [ ] 用户权限管理系统
- [ ] 实时报警和通知
- [ ] 数据导出功能
- [ ] 多语言支持

## 📄 许可证

MIT License

---

**开发团队**: 智能房间控制系统开发组  
**更新时间**: 2025 年 8 月  
**版本**: v1.0.0
