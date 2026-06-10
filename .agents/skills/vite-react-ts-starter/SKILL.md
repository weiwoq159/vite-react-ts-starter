---
name: vite-react-ts-starter
description: Use this skill when the user asks to create or initialize a Vite + React + TypeScript frontend project with Ant Design, Sass, Dayjs, ahooks, React Router DOM, @ant-design/icons, unplugin-auto-import auto imports, src directory architecture, Vite aliases, tsconfig paths, and desktop global.scss layout.
---

# Vite React TypeScript Starter Skill

## Claude Code 执行规则

- 优先使用 `pnpm`，除非用户明确要求 `npm` 或 `yarn`。
- 如果当前目录已经是 Vite React TypeScript 项目，不要重复创建项目，只补齐依赖、目录、配置和初始化文件。
- 修改文件时以本 Skill 中的“最终配置”为准，不要保留旧版重复配置。
- 完成后运行构建校验；默认执行 `pnpm build`，如用户使用其他包管理器则使用对应命令。
- 不要默认加入 axios、状态管理库、mockjs、复杂 ESLint/Prettier 规则，除非用户明确要求。
- 遇到 `__dirname` 报错时，优先使用 `fileURLToPath(new URL(..., import.meta.url))` 的 ESM 兼容写法。

---

## 目标

当用户需要新建一个 `Vite + TypeScript + React` 前端项目时，自动完成以下事情：

1. 创建 Vite React TypeScript 项目。
2. 安装基础依赖：
   - `antd`
   - `sass`
   - `dayjs`
   - `ahooks`
   - `react-router-dom`
   - `@ant-design/icons`
3. 安装并配置自动导入：
   - `unplugin-auto-import`
   - React API 自动导入
   - React Router DOM API 自动导入
   - ahooks 常用 API 自动导入
4. 重构 `src` 目录结构。
5. 配置 `vite.config.ts` 的 alias。
6. 配置 `tsconfig.app.json` 的 paths。
7. 初始化 `src/styles/global.scss`。
8. 替换 Vite 默认初始页面，生成一个可运行的基础应用骨架。

---

## 适用场景

当用户说出类似需求时，使用本 Skill：

- 新建一个 Vite React TS 项目
- 初始化一个 React + Ant Design 项目
- 帮我搭一个 Furina 前端项目
- 创建一个桌面端前端基础工程
- 初始化 Vite 项目并配置 alias
- 创建 React 项目并安装 antd、sass、dayjs、ahooks、router
- 配置 React / React Router DOM / ahooks 自动导入

---

## 输入参数

如果用户没有明确说明，按以下默认值处理：

```txt
projectName: furina
packageManager: pnpm
template: react-ts
```

如果用户指定了项目名，以用户指定为准。

如果当前目录已经是一个 Vite React TS 项目，不要重新创建项目，只执行依赖安装、目录创建和配置修改。

---

## 技术栈

```txt
Vite
React
TypeScript
Ant Design
Sass
Dayjs
ahooks
React Router DOM
@ant-design/icons
unplugin-auto-import
```

---

## 执行步骤

### 1. 创建项目

如果当前目录不是已有项目，执行：

pnpm：

```bash
pnpm create vite@latest {{projectName}} -- --template react-ts
cd {{projectName}}
pnpm install
```

npm：

```bash
npm create vite@latest {{projectName}} -- --template react-ts
cd {{projectName}}
npm install
```

yarn：

```bash
yarn create vite {{projectName}} --template react-ts
cd {{projectName}}
yarn
```

---

### 2. 安装依赖

pnpm：

```bash
pnpm add antd sass dayjs ahooks react-router-dom @ant-design/icons
pnpm add -D unplugin-auto-import
```

npm：

```bash
npm install antd sass dayjs ahooks react-router-dom @ant-design/icons
npm install -D unplugin-auto-import
```

yarn：

```bash
yarn add antd sass dayjs ahooks react-router-dom @ant-design/icons
yarn add -D unplugin-auto-import
```

---

### 3. 创建 src 目录结构

在 `src` 下创建以下目录：

```txt
src/
  app/
  layout/
  config/
  mocks/
  pages/
  plugins/
  router/
  services/
  shared/
  styles/
  types/
```

如果目录已经存在，不要删除，直接复用。

---

### 4. 推荐目录职责

```txt
src/app
应用入口层，放 App.tsx、全局 Provider、应用级配置组合。

src/layout
页面布局层，放主布局、侧边栏布局、顶部栏布局等。

src/config
项目配置层，放路由配置、主题配置、环境配置、常量配置等。

src/mocks
Mock 数据层，放本地模拟数据和接口假数据。

src/pages
页面层，放业务页面。

src/plugins
插件层，放插件注册、插件元信息、插件入口等。

src/router
路由层，放 react-router-dom 的路由定义。

src/services
接口服务层，放 HTTP 请求、API 封装、业务服务。

src/shared
公共能力层，放 hooks、utils、components、constants 等可复用内容。

src/styles
样式层，放 global.scss、variables.scss、reset.scss 等。

src/types
类型层，放全局类型、业务类型、模块声明。
```

---

## 5. 修改 vite.config.ts

优先使用 ESM 兼容写法，不直接使用 `__dirname`，因为 Vite 新模板通常是 ESM 项目。

将 `vite.config.ts` 修改为：

```ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import AutoImport from "unplugin-auto-import/vite";
import { fileURLToPath, URL } from "node:url";

/**
 * 将相对路径转换为绝对路径。
 *
 * Vite 默认使用 ESM 配置环境，
 * 所以这里不用 __dirname，避免在新项目中报错。
 */
const resolvePath = (path: string) => {
  return fileURLToPath(new URL(path, import.meta.url));
};

export default defineConfig({
  plugins: [
    react(),

    /**
     * 自动导入常用 API。
     *
     * 作用：
     * 1. 使用 useState、useEffect 等 React API 时，不需要手动 import。
     * 2. 使用 useNavigate、useParams、Outlet、Navigate 等 react-router-dom API 时，不需要手动 import。
     * 3. 使用 useRequest、useMount 等 ahooks API 时，不需要手动 import。
     * 4. 自动生成 src/auto-imports.d.ts，保证 TypeScript 能识别这些全局 API。
     *
     * 注意：
     * 这里只自动导入函数/API，不负责自动导入 antd 组件。
     */
    AutoImport({
      include: [/\.[tj]sx?$/],

      imports: [
        "react",
        "react-router-dom",
        {
          ahooks: [
            "useRequest",
            "useMount",
            "useUnmount",
            "useUpdateEffect",
            "useDebounce",
            "useDebounceFn",
            "useThrottle",
            "useThrottleFn",
            "useBoolean",
            "useToggle",
            "useSetState",
            "useLocalStorageState",
            "useSessionStorageState",
            "useMemoizedFn",
            "useCreation",
            "useReactive",
            "useSafeState",
            "useInterval",
            "useTimeout",
            "useEventListener",
            "useClickAway",
            "useSize",
            "useFullscreen",
            "usePagination",
          ],
        },
      ],

      /**
       * 自动生成类型声明文件。
       *
       * 这个文件不要手动维护，由插件自动生成。
       */
      dts: "src/auto-imports.d.ts",

      /**
       * 推荐开启，让 Vite 预构建自动导入的依赖，
       * 可以减少部分开发时依赖解析问题。
       */
      viteOptimizeDeps: true,

      /**
       * 如果项目启用了 ESLint no-undef，
       * 可以开启这个配置。
       *
       * 当前先关闭，避免初始化项目时多生成无关配置。
       */
      eslintrc: {
        enabled: false,
      },
    }),
  ],

  resolve: {
    alias: {
      "@": resolvePath("./src"),
      "@app": resolvePath("./src/app"),
      "@layout": resolvePath("./src/layout"),
      "@config": resolvePath("./src/config"),
      "@mocks": resolvePath("./src/mocks"),
      "@pages": resolvePath("./src/pages"),
      "@plugins": resolvePath("./src/plugins"),
      "@router": resolvePath("./src/router"),
      "@services": resolvePath("./src/services"),
      "@shared": resolvePath("./src/shared"),
      "@styles": resolvePath("./src/styles"),
      "@types": resolvePath("./src/types"),
    },
  },
});
```

如果用户明确要求使用 `path.resolve(__dirname, ...)`，才使用下面这种写法：

```ts
import path from "node:path";
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import AutoImport from "unplugin-auto-import/vite";

export default defineConfig({
  plugins: [
    react(),
    AutoImport({
      include: [/\.[tj]sx?$/],
      imports: [
        "react",
        "react-router-dom",
        {
          ahooks: [
            "useRequest",
            "useMount",
            "useUnmount",
            "useUpdateEffect",
            "useDebounce",
            "useDebounceFn",
            "useThrottle",
            "useThrottleFn",
            "useBoolean",
            "useToggle",
            "useSetState",
            "useLocalStorageState",
            "useSessionStorageState",
            "useMemoizedFn",
            "useCreation",
            "useReactive",
            "useSafeState",
            "useInterval",
            "useTimeout",
            "useEventListener",
            "useClickAway",
            "useSize",
            "useFullscreen",
            "usePagination",
          ],
        },
      ],
      dts: "src/auto-imports.d.ts",
      viteOptimizeDeps: true,
      eslintrc: {
        enabled: false,
      },
    }),
  ],

  resolve: {
    alias: {
      "@": path.resolve(__dirname, "src"),
      "@app": path.resolve(__dirname, "src/app"),
      "@layout": path.resolve(__dirname, "src/layout"),
      "@config": path.resolve(__dirname, "src/config"),
      "@mocks": path.resolve(__dirname, "src/mocks"),
      "@pages": path.resolve(__dirname, "src/pages"),
      "@plugins": path.resolve(__dirname, "src/plugins"),
      "@router": path.resolve(__dirname, "src/router"),
      "@services": path.resolve(__dirname, "src/services"),
      "@shared": path.resolve(__dirname, "src/shared"),
      "@styles": path.resolve(__dirname, "src/styles"),
      "@types": path.resolve(__dirname, "src/types"),
    },
  },
});
```

注意：如果使用 `__dirname` 报错，需要改回 ESM 兼容写法。

---

## 6. 修改 tsconfig.app.json

找到 `tsconfig.app.json`，保留原有配置，只在 `compilerOptions` 中补充 `baseUrl` 和 `paths`。

推荐配置：

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@app/*": ["src/app/*"],
      "@layout/*": ["src/layout/*"],
      "@config/*": ["src/config/*"],
      "@mocks/*": ["src/mocks/*"],
      "@pages/*": ["src/pages/*"],
      "@plugins/*": ["src/plugins/*"],
      "@router/*": ["src/router/*"],
      "@services/*": ["src/services/*"],
      "@shared/*": ["src/shared/*"],
      "@styles/*": ["src/styles/*"],
      "@types/*": ["src/types/*"]
    }
  },
  "include": ["src", "src/auto-imports.d.ts"]
}
```

不要覆盖原有的 `target`、`module`、`jsx`、`strict`、`moduleResolution` 等配置。

如果 `compilerOptions` 已经存在，只追加：

```json
{
  "baseUrl": ".",
  "paths": {
    "@/*": ["src/*"],
    "@app/*": ["src/app/*"],
    "@layout/*": ["src/layout/*"],
    "@config/*": ["src/config/*"],
    "@mocks/*": ["src/mocks/*"],
    "@pages/*": ["src/pages/*"],
    "@plugins/*": ["src/plugins/*"],
    "@router/*": ["src/router/*"],
    "@services/*": ["src/services/*"],
    "@shared/*": ["src/shared/*"],
    "@styles/*": ["src/styles/*"],
    "@types/*": ["src/types/*"]
  }
}
```

如果原文件里已经有：

```json
"include": ["src"]
```

只需要改成：

```json
"include": ["src", "src/auto-imports.d.ts"]
```

---

## 7. 生成基础文件

### src/app/App.tsx

```tsx
import { ConfigProvider, App as AntdApp } from "antd";
import { RouterProvider } from "react-router-dom";

import { router } from "@router/index";

/**
 * 应用根组件。
 *
 * ConfigProvider:
 * 用于配置 Ant Design 主题、语言、组件默认配置。
 *
 * AntdApp:
 * 用于支持 message、modal、notification 等 Ant Design 全局反馈能力。
 *
 * RouterProvider:
 * 用于挂载 react-router-dom 路由系统。
 */
export default function App() {
  return (
    <ConfigProvider
      theme={{
        token: {
          colorPrimary: "#7d9cc7",
          borderRadius: 12,
        },
      }}
    >
      <AntdApp>
        <RouterProvider router={router} />
      </AntdApp>
    </ConfigProvider>
  );
}
```

---

### src/main.tsx

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import "antd/dist/reset.css";

import App from "@app/App";
import "@styles/global.scss";

/**
 * React 应用入口。
 *
 * 这里保持入口足够干净：
 * 1. 引入 Ant Design reset 样式
 * 2. 引入全局 Sass 样式
 * 3. 挂载 App 根组件
 */
ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);
```

---

### src/router/index.tsx

```tsx
import { RootLayout } from "@layout/RootLayout";
import { DashboardPage } from "@pages/DashboardPage";

/**
 * 应用路由配置。
 *
 * createBrowserRouter 和 Navigate 由 unplugin-auto-import 自动导入。
 */
export const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    children: [
      {
        index: true,
        element: <Navigate to="/dashboard" replace />,
      },
      {
        path: "dashboard",
        element: <DashboardPage />,
      },
    ],
  },
]);
```

---

### src/layout/RootLayout.tsx

```tsx
import { Layout, Menu, Typography } from "antd";
import { AppstoreOutlined, SettingOutlined } from "@ant-design/icons";

const { Sider, Header, Content } = Layout;

/**
 * 项目主布局。
 *
 * useNavigate 和 Outlet 由 unplugin-auto-import 自动导入。
 */
export function RootLayout() {
  const navigate = useNavigate();

  return (
    <Layout className="app-shell">
      <Sider width={240} className="app-sider">
        <div className="app-logo">Furina</div>

        <Menu
          theme="dark"
          mode="inline"
          defaultSelectedKeys={["dashboard"]}
          items={[
            {
              key: "dashboard",
              icon: <AppstoreOutlined />,
              label: "Dashboard",
              onClick: () => navigate("/dashboard"),
            },
            {
              key: "settings",
              icon: <SettingOutlined />,
              label: "Settings",
            },
          ]}
        />
      </Sider>

      <Layout>
        <Header className="app-header">
          <Typography.Title level={4} style={{ margin: 0 }}>
            Furina Workspace
          </Typography.Title>
        </Header>

        <Content className="app-content">
          <Outlet />
        </Content>
      </Layout>
    </Layout>
  );
}
```

---

### src/pages/DashboardPage.tsx

```tsx
import { Card, Space, Typography } from "antd";
import dayjs from "dayjs";

/**
 * 默认 Dashboard 页面。
 *
 * 用于验证：
 * - Ant Design 是否正常
 * - dayjs 是否正常
 * - ahooks auto import 是否正常
 * - router 是否正常
 * - alias 是否正常
 */
export function DashboardPage() {
  useMount(() => {
    console.log("Dashboard mounted");
  });

  return (
    <Space direction="vertical" size={16} style={{ width: "100%" }}>
      <Typography.Title level={2}>Dashboard</Typography.Title>

      <Card>
        <Typography.Paragraph>
          Vite + React + TypeScript + Ant Design 项目初始化完成。
        </Typography.Paragraph>

        <Typography.Text type="secondary">
          当前时间：{dayjs().format("YYYY-MM-DD HH:mm:ss")}
        </Typography.Text>
      </Card>
    </Space>
  );
}
```

---

### src/styles/global.scss

```scss
html,
body,
#root {
  width: 100vw;
  height: 100vh;
  min-width: 1280px;
  overflow: hidden;
  padding: 0;
  margin: 0;
}

* {
  box-sizing: border-box;
}

body {
  font-family:
    Inter,
    -apple-system,
    BlinkMacSystemFont,
    "Segoe UI",
    sans-serif;
  background: #f5f7fb;
}

.app-shell {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}

.app-sider {
  height: 100vh;
}

.app-logo {
  height: 64px;
  padding: 0 24px;
  display: flex;
  align-items: center;
  color: #ffffff;
  font-size: 18px;
  font-weight: 700;
}

.app-header {
  height: 64px;
  padding: 0 24px;
  display: flex;
  align-items: center;
  background: #ffffff;
  border-bottom: 1px solid #edf0f5;
}

.app-content {
  height: calc(100vh - 64px);
  padding: 24px;
  overflow: auto;
}
```

---

## 8. 创建占位文件

如果对应目录为空，可以创建以下占位文件：

```txt
src/config/index.ts
src/mocks/index.ts
src/plugins/index.ts
src/services/index.ts
src/shared/index.ts
src/types/global.d.ts
```

内容可以先写：

```ts
export {};
```

`src/types/global.d.ts` 内容：

```ts
declare global {
  type Nullable<T> = T | null;
}

export {};
```

---

## 9. 清理 Vite 默认文件

如果存在以下文件，并且已经不再被引用，可以删除：

```txt
src/App.css
src/index.css
src/assets/react.svg
public/vite.svg
```

如果 `src/App.tsx` 是 Vite 默认文件，需要移动或替换为：

```txt
src/app/App.tsx
```

不要保留重复的默认 App 文件，避免入口混乱。

---

## 10. 校验

完成后执行：

pnpm：

```bash
pnpm build
pnpm dev
```

npm：

```bash
npm run build
npm run dev
```

yarn：

```bash
yarn build
yarn dev
```

如果出现 alias 不识别，优先检查：

1. `vite.config.ts` 是否配置了 `resolve.alias`
2. `tsconfig.app.json` 是否配置了 `compilerOptions.paths`
3. `src/main.tsx` 是否从 `@app/App` 引入
4. TypeScript Server 是否需要重启
5. IDE 是否需要重新加载项目

如果出现 `useMount is not defined`、`useNavigate is not defined`、`Outlet is not defined` 之类的问题，优先检查：

1. `vite.config.ts` 是否正确引入了 `AutoImport`。
2. `AutoImport` 是否在 `plugins` 数组中。
3. `dts` 是否生成了 `src/auto-imports.d.ts`。
4. `tsconfig.app.json` 的 `include` 是否包含 `src/auto-imports.d.ts`。
5. 开发服务器是否重启。
6. IDE TypeScript Server 是否重启。

---

## 11. 完成标准

当以下条件满足时，认为任务完成：

- 项目可以正常启动。
- 项目可以正常 build。
- `antd` 样式正常生效。
- `sass` 全局样式正常生效。
- `react-router-dom` 路由正常工作。
- `dayjs` 可以正常使用。
- `ahooks` 可以正常使用。
- `@ant-design/icons` 可以正常使用。
- 已安装 `unplugin-auto-import`。
- `vite.config.ts` 已配置 `AutoImport`。
- React API 可以自动导入。
- React Router DOM API 可以自动导入。
- ahooks 常用 API 可以自动导入。
- 已生成 `src/auto-imports.d.ts`。
- `tsconfig.app.json` 已包含 `src/auto-imports.d.ts`。
- 所有 src 一级目录均已创建。
- `vite.config.ts` alias 配置完整。
- `tsconfig.app.json` paths 配置完整。
- `global.scss` 已初始化桌面端全屏布局规则。
- 默认 Vite 页面已替换为项目基础骨架。

---

## 12. 注意事项

不要为了初始化项目引入过重架构。

不要一开始就加入状态管理库，除非用户明确要求。

不要默认加入 axios，除非用户明确要求。

不要默认加入 mockjs，除非用户明确要求。

不要默认加入 eslint/prettier 复杂规则，除非用户明确要求。

不要把新增配置作为补丁追加在文件末尾。需要把重复章节合并成最终版，避免同一个文件里出现两套依赖安装、两套 `vite.config.ts`、两套 `global.scss`。

优先保证项目干净、可运行、目录清晰、alias 完整、自动导入配置稳定。
