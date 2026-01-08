---
draft: false
create_data: 2026-01-08 16:37
---
# 背景

# 安装

```
npm i -g pnpm
```

# 初始化

```
mkdir xxx-mono
cd xxx-mono

pnpm init
```

# 文件结构准备

我们首先创建 apps 和 packages 两个文件夹。其中：

- packages 放公共项目，比如公共的 utils、组件库、类型定义等
- apps 放业务项目

# 创建业务项目

创建前端项目

```
# 先移动到 apps 文件夹
cd apps
# 创建子项目
pnpm create vite fe --template react
```

创建后端项目，这里使用我封装好的一个 [nestjs 的模板](https://github.com/i1mT/nestjs-starter)

```
# 根目录下执行
cd apps
# 克隆代码，并重命名为 server
git clone https://github.com/i1mT/nestjs-starter.git ./server

cd ./server

# 删除模板的 git 记录
rm -rf .git
```

# 创建 workspaces

在根目录创建一个 `pnpm-workspace.yaml` 文件，详细说明可以看官方文档：https://pnpm.io/pnpm-workspace_yaml

```yaml
# pnpm-workspace.yamlpackages:  - 'packages/*'  - 'apps/*'
```

# 创建公共子项目

我们创建一个 utils 子项目，作为公共包：

```
# 切到 packages 文件夹
cd packages

# 创建公共库
mkdir utils

cd utils

pnpm init
```

## 修改公共子项目

在公共子项目中，创建文件 `src/index.ts` ，内容如下：

```
export function formatDate(date: Date) {
  return date.toLocaleDateString('zh-CN', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  });
}
```

修改 utils 项目的 packages.json，改一下 name

```json
{  "name": "@packages/utils",  "version": "1.0.0",  "description": "",  "main": "index.js",  "scripts": {    "test": "echo \"Error: no test specified\" && exit 1"  },  "keywords": [],  "author": "",  "license": "ISC"}
```

# 引用公共项目

回到 fe 项目中，我们首先在 package.json 中添加依赖：

```
cd apps/fe

# 添加依赖
pnpm add @packages/utils
```

这里的包名，直接写 utils 的包名即可。可以看到它会在 packages.json 中新增一个依赖：

```
...
  "dependencies": {
    "@packages/utils": "workspace:^",
    "react": "^19.1.0",
    "react-dom": "^19.1.0"
  },
...
```

## 在代码中引用

在 fe 项目中，引用 utils 的 `formatDate` 函数

```tsx
import "./App.css";import { formatDate } from "@packages/utils";function App() {
  const date = formatDate(new Date());  console.log(date);  return (
    <div>      Date: {date}
    </div>  );}
export default App;
```

在 fe 目录下执行，pnpm run dev，查看：http://localhost:5173/

![csxlNZe724RDFmprv1d8_YiZsmdGHgEgRvl89XRs618=.png](csxlNZe724RDFmprv1d8_YiZsmdGHgEgRvl89XRs618.png)

可以看到 formatDate 函数正确执行了，引用没有问题。

在server端也是同样：

1. 执行 pnpm add @packages/utils
2. 引用

以上。