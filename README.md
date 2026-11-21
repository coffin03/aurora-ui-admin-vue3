# Aurora Admin

现代化简约 SaaS 风格的中后台前端，基于 Vue 3 + TypeScript + Vite + Element Plus 构建。

> **衍生项目声明**
>
> 本项目是 [yudao-ui-admin-vue3](https://gitee.com/yudaocode/yudao-ui-admin-vue3) 的二次开发版本，主要进行了整体 UI 与布局重构。
> 上游代码版权归 Archer 所有，依据 MIT 许可证授权；修改部分版权归 Aurora Admin contributors 所有。
> 本项目为独立衍生项目，与上游作者无隶属或合作关系，不代表上游官方版本。
> 详细声明见 [LICENSE](./LICENSE) 与 [NOTICE](./NOTICE)。

## 界面特性

- **统一设计体系**：品牌主色靛蓝紫 `#6366F1`，统一的圆角、间距、阴影、字号与色阶令牌
- **布局框架**：侧边栏、Logo、标签页、内容容器、顶部栏整体重构，顶栏图标与操作项像素级对齐
- **登录页**：重新设计的左右分栏登录页，含扫码登录、社交登录样式
- **首页仪表盘**：卡片化数据概览与图表配色重做
- **业务页**：列表页/表单页统一的卡片、表格、查询区与操作区样式
- **主题能力完整适配**：主题色、头部主题、菜单主题、暗色模式均可通过「项目配置」切换
- **设置面板**：优化后的悬浮设置按钮与配置抽屉

## 技术栈

| 分类 | 选型 |
| --- | --- |
| 框架 | Vue 3 + TypeScript |
| 构建 | Vite |
| UI 库 | Element Plus |
| 状态 | Pinia |
| 路由 | Vue Router |
| 样式 | SCSS + UnoCSS |
| 图表 | ECharts |
| 图标 | Iconify |

## 快速开始

环境要求：Node.js > 16.18.0，pnpm > 8.6.0（强制使用 pnpm）。

```bash
# 安装依赖
pnpm i

# 本地开发（默认读取 .env.local，后端地址 http://localhost:48080）
npm run dev

# 本地构建
npm run build:local
```

其他构建模式：`build:dev`、`build:test`、`build:stage`、`build:prod`，分别对应 `.env.dev`、`.env.test`、`.env.stage`、`.env.prod`。

## 与后端的关系

本项目只包含前端代码，需要配合独立的 Java 后端使用（上游的 yudao / ruoyi-vue-pro 后端工程）。

- 后端仓库有**独立的许可证**，部分模块（如工作流、商城、ERP 等）属于商业授权范围，请自行确认后再使用
- 接口地址在 `.env.*` 的 `VITE_BASE_URL` 与 `VITE_API_URL` 中配置
- `.env.dev`、`.env.stage` 中保留的上游演示环境地址仅作参考，请替换为你自己的环境

## 品牌与配置

| 内容 | 位置 |
| --- | --- |
| 系统标题 | `.env` 的 `VITE_APP_TITLE` |
| 默认登录租户 | `.env` 的 `VITE_APP_DEFAULT_LOGIN_TENANT`（须与后端实际租户名一致） |
| Logo | `src/assets/imgs/logo.png`、`public/logo.png`（矢量源文件：`design/logo/logo.svg`） |
| favicon | `public/favicon.png` |
| 默认主题色 | `src/store/modules/app.ts`，或运行后在「项目配置」中调整 |
| 百度统计 | `.env` 的 `VITE_APP_BAIDU_CODE`，留空则不启用 |

> 如果你直接连接上游演示后端，请把 `VITE_APP_DEFAULT_LOGIN_TENANT` 改回该后端的默认租户名，否则默认账号无法登录。

## 关于上游文档链接

部分业务页面保留了指向 `doc.iocoder.cn` 的文档提示链接（`doc-alert` 组件），这些内容属于上游文档，可按需替换为你自己的文档地址。

## 许可证

[MIT](./LICENSE)。基于 Archer 的 [yudao-ui-admin-vue3](https://gitee.com/yudaocode/yudao-ui-admin-vue3) 二次开发，详见 [NOTICE](./NOTICE)。