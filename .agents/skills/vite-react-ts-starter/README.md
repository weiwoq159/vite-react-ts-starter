# vite-react-ts-starter

一个用于初始化 `Vite + React + TypeScript` 前端项目的 Claude Skill。

该 Skill 适合快速创建现代化桌面端或 Web 前端基础工程，自动完成项目创建、依赖安装、目录结构初始化、Vite alias 配置、TypeScript paths 配置、React Router 初始化、Ant Design 接入、Sass 全局样式配置，以及常用 API 的 Auto Import 配置。

## 功能特性

使用该 Skill 后，Claude 可以自动完成以下工作：

- 创建 `Vite + React + TypeScript` 项目
- 安装基础依赖：
  - `antd`
  - `sass`
  - `dayjs`
  - `ahooks`
  - `react-router-dom`
  - `@ant-design/icons`

- 安装并配置 `unplugin-auto-import`
- 自动导入常用 API：
  - React
  - React Router DOM
  - ahooks

- 创建标准化 `src` 目录结构
- 配置 `vite.config.ts` alias
- 配置 `tsconfig.app.json` paths
- 初始化 `src/styles/global.scss`
- 替换 Vite 默认页面
- 生成可直接运行的基础应用骨架

## 适用场景

该 Skill 适合以下场景：

- 初始化 React + TypeScript 项目
- 创建 Vite 前端基础工程
- 创建 Ant Design 管理后台项目
- 创建桌面端前端 UI 工程
- 创建 Electron / Tauri 前端页面工程
- 快速生成带有路由、布局、样式和 alias 的项目模板

## 项目结构

Skill 目录结构如下：

```txt
vite-react-ts-starter/
  SKILL.md
  README.md
```

其中：

```txt
SKILL.md
```

是 Claude 读取和执行的核心技能文件。

```txt
README.md
```

是当前项目说明文档，用于说明该 Skill 的作用、使用方式和维护规则。

## 生成的前端项目结构

使用该 Skill 初始化项目后，会生成类似以下结构：

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

目录职责如下：

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

## 默认技术栈

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

## 默认配置

如果用户没有额外说明，Skill 默认使用以下配置：

```txt
projectName: furina
packageManager: pnpm
template: react-ts
```

如果用户指定了项目名、包管理器或其他约束，应以用户输入为准。

## Alias 规则

Skill 会自动配置以下 alias：

```txt
@          -> src
@app       -> src/app
@layout    -> src/layout
@config    -> src/config
@mocks     -> src/mocks
@pages     -> src/pages
@plugins   -> src/plugins
@router    -> src/router
@services  -> src/services
@shared    -> src/shared
@styles    -> src/styles
@types     -> src/types
```

这些 alias 会同时写入：

```txt
vite.config.ts
tsconfig.app.json
```

这样既能保证 Vite 构建识别路径，也能保证 TypeScript 和编辑器识别路径。

## Auto Import

Skill 会配置 `unplugin-auto-import`，用于自动导入常用 API。

默认支持：

```txt
react
react-router-dom
ahooks
```

示例：

```tsx
const [count, setCount] = useState(0);

useEffect(() => {
  console.log("mounted");
}, []);

const navigate = useNavigate();

useMount(() => {
  console.log("dashboard mounted");
});
```

这些 API 不需要手动 import。

Skill 会自动生成：

```txt
src/auto-imports.d.ts
```

用于让 TypeScript 正确识别自动导入的类型。

## 全局样式

Skill 会初始化 `src/styles/global.scss`，默认面向桌面端应用布局：

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
```

该配置适合 Dashboard、工坊、插件中心、桌面客户端等复杂布局场景。

## 使用方式

在 Claude 中启用该 Skill 后，可以这样描述需求：

```txt
使用 vite-react-ts-starter skill，帮我创建一个 Furina 前端项目。
```

或者：

```txt
帮我初始化一个 Vite + React + TypeScript 项目，使用 Ant Design、Sass、dayjs、ahooks、react-router-dom，并配置 alias 和 auto import。
```

Claude 应该根据 `SKILL.md` 中的规则自动执行项目初始化。

## 生成项目后的校验命令

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

## 完成标准

当以下条件满足时，可以认为初始化完成：

- 项目可以正常启动
- 项目可以正常 build
- Ant Design 样式正常生效
- Sass 全局样式正常生效
- React Router DOM 路由正常工作
- Dayjs 可以正常使用
- ahooks 可以正常使用
- `@ant-design/icons` 可以正常使用
- `unplugin-auto-import` 已生效
- `src/auto-imports.d.ts` 已生成
- 所有 src 一级目录均已创建
- `vite.config.ts` alias 配置完整
- `tsconfig.app.json` paths 配置完整
- 默认 Vite 页面已替换为项目基础骨架

## 维护原则

维护该 Skill 时，需要遵守以下原则：

1. 保持项目初始化流程简单直接。
2. 不默认引入过重架构。
3. 不默认加入状态管理库，除非用户明确要求。
4. 不默认加入 axios，除非用户明确要求。
5. 不默认加入 mockjs，除非用户明确要求。
6. 不默认加入复杂 eslint / prettier 规则，除非用户明确要求。
7. 优先保证项目干净、可运行、目录清晰、alias 完整。
8. 如果后续新增依赖，必须同步更新：
   - 安装命令
   - 技术栈说明
   - 完成标准
   - 校验规则

## License

MIT
