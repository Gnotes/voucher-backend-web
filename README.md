# 后台管理系统（admin）

礼品券兑换系统的后台管理端。基于 **Vue3 + Vite + Element-Plus + Pinia + Axios**。

## 技术栈
- Vue3（`<script setup>`）+ Vite
- Element-Plus（UI 组件库 + 图标）
- Pinia（用户 / 角色 / 权限状态）
- Axios（请求封装，token 注入 + 统一错误处理）
- Vue-Router（history 模式，含角色权限 `meta.roles` + 全局守卫）

## 目录规划
```
admin/
├── src/
│   ├── api/          # 请求封装（request.js）+ 各业务接口
│   ├── constants/    # 角色与权限常量（roles.js）
│   ├── layout/       # 主框架（菜单由路由 meta 驱动）
│   ├── router/       # 路由表 + 全局守卫 + 导出 routes
│   ├── store/        # Pinia 用户态（角色 / 权限）
│   ├── permission/   # usePermission 组合式 + v-permission 指令
│   ├── styles/       # 全局样式
│   ├── views/        # 页面（login / dashboard / tenant / member / profile / coupon / order / logistics / notification / 403）
│   ├── App.vue
│   └── main.js       # 入口
├── task/             # 规划文档（任务拆分、RBAC 设计规划）
├── docs/             # 项目规范（架构 / 编码 / 权限 / 接口）
├── vite.config.js
└── package.json
```

## 角色与权限（RBAC）
三类角色：**平台管理员** / **企业管理员** / **企业成员**。
- 路由级：路由 `meta.roles` + 全局守卫拦截，无权限跳 `/403`。
- 功能级：权限码 `模块:资源:动作` + `v-permission` 指令控制按钮显隐。
- 企业管理员可管理本企业成员与用户基本信息；企业成员可维护本人资料、编辑自己的订单与物流（行级由后端约束）。
- 券码分配链路：**批次创建即生成券码（平台持有）→ 平台 `coupon:batch:allocate` 分配给企业 → 企业 `coupon:code:allocate` 下发给成员**（`tenantId` 归属企业 / `memberId` 下发成员，券所有权始终在平台/企业侧）。
- 详见 [`docs/rbac.md`](./docs/rbac.md)，设计规划见 [`task/RBAC.md`](./task/RBAC.md)。

## 模块（路由清单）
| 路由 | 页面 | 权限 |
|------|------|------|
| `/login` | 登录（演示角色下拉） | 公开 |
| `/dashboard` | 数据看板 | 登录 |
| `/tenant` | 租户管理 | super_admin |
| `/member` | 成员管理 | super_admin / enterprise_admin |
| `/profile` | 个人资料 | 全员（维护本人信息） |
| `/coupon/template` | 券模板 | super_admin / enterprise_admin |
| `/coupon/batch` | 批次库存 | super_admin / enterprise_admin |
| `/coupon/code` | 券码列表 | 全员（成员可查看） |
| `/order` | 兑换订单 | 全员（成员可编辑自己的） |
| `/logistics` | 物流管理 | 全员（成员可发货+编辑自己的） |
| `/notification` | 消息通知 | 全员（成员仅查看） |
| `/403` | 无权限提示 | — |

## 文档
- [架构规范](./docs/architecture.md)
- [编码规范](./docs/coding-standard.md)
- [权限模型规范（RBAC）](./docs/rbac.md)
- [接口约定](./docs/api.md)
- 规划：[任务拆分](./task/TASKS.md) · [RBAC 设计规划](./task/RBAC.md)

## 启动
```bash
npm install
npm run dev      # http://localhost:8080
npm run build    # 产物 dist/
```
> API 前缀 `/api` 代理到 `http://localhost:3000`，待后端确定地址后调整 `vite.config.js`。
