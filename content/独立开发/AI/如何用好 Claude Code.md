---
draft: "true"
---
# 购买

## Claude code 官网
直接操作购买即可
## OpenRouter【推荐】
AI 网关，提供所有模型。你充的钱，可以花在它支持的任何模型上，不用担心模型强度变化了，在原平台的钱白充了。

所有AI 网关类，都可以使用 Claude code router 来集成到 claude code 中。
使用方式如下：

### 环境准备

安装 npm
### 安装
> 参考：[claude code router](https://github.com/musistudio/claude-code-router)

安装 claude code router
```
npm install -g @musistudio/claude-code-router
```

安装 claude code
```
npm install -g @anthropic-ai/claude-code
```

### 配置API

1. 执行 `ccr ui`
2. 在网页中添加 Provider
	![[Pasted image 20260105171348.png]]
3. 填写 api `https://openrouter.ai/api`
4. 填写在 openrouter 生成的 sk
	![[Pasted image 20260105171509.png]]
5. 添加模型；在 openrouter 中复制模型id即可。比如：`anthropic/claude-sonnet-4.5`

	![[Pasted image 20260105171155.png]]
6. 最后 save & restart 即可
	![[Pasted image 20260105171558.png]]
### 使用
配置好之后，执行 `ccr code` 即可启动，和官网充值体验相同。
![[Pasted image 20260105171728.png]]

# 配置
## 全局规则

在 `~/.claude/CLAUDE.md` 中编写的规则，会在每个session中都使用。

可以将一些全局规则写在这个文档中，如：

```
# 全局规则
- 请总是使用中文返回
```

## 通知
可以在任务需要人工确认，或者任务完成时，发送提醒。

以iTerm为例，设置步骤如下：

1. 打开通知
	在 claude code 中执行 /config，找到 Notification，修改为 terminal_bell
2. 打开 iTerm 设置 -> Profile -> Terminal
3. 打开 Slicence bell
4. 点击 Filter Alert，选中 Send escape sequence-generated alerts

参考 [Claude code 官方文档](https://code.claude.com/docs/en/terminal-config#iterm-2-system-notifications)

# 提示词
## 输入

1. 通过`ctrl + g` 进入 vim 编辑器，编写大段的 prompt

# 技巧

## slash command



## agent



# 方法论

## GEB 管理法

在 Vibe coding 的过程中，核心问题是：

***如何在代码膨胀的过程中始终保证项目清晰可读***

这是无论 AI 或 开发 可迭代的关键因素。

为了解决这个问题，我们只需要采用一个简单的方法：
- 记录
- 更新
- 进化

具体操作：
1. 在项目的 CLAUDE.md 文件中写下：
```
## GEB管理方法

- 主文档：项目根目录的 `CLAUDE.md`
- 子文档：除项目根目录之外，其他所有目录下的 `CLAUDE.md`，里面记录该目录下每个文件的名字、地位、功能。
  
### 规范

1. 任何功能、架构、代码更新必须在工作结束后，更新相关目录的子文档。
2. 每个文件夹下面都有一个子文档，并且文件开头声明：一旦我所属的文件夹有所变化，请务必更新我。
3. 项目中的每个文件的开头，都写下以下注释：文件input(依赖外部的什么)、文件output(对外提供什么)、文件pos(在系统局部的地位是什么)。在开头同样写下：一旦我被更新，务必更新我的开头注释，以及所属文件夹的子文档。

```
2. 让claude code 根据该管理方法，进行初始化
```
请你根据 CLAUDE.md 文件中【GEB管理方法】：
1. 创建当前项目的所有子文档
2. 给每个文件的开头添加对应的注释
```




# 其他

## 移动端管理

可以使用 happy 在手机、平板上查看任务进度。开启新任务等。


