---
draft: false
create_data: 2026-01-08 16:37
---
# 0. 为什么要用 deepseek

因为它是目前最强，但是价格却最低（关于这个结论自行搜过）的大模型。

cursor 的订阅太贵了，但是cursor 支持自定义模型，使用自定义模型cursor是免费的。这样我们就可以去其他平台，用它的open api 接入到 cursor，然后充值到其他平台。

估算用 deepseek，假设平均每天 20w tokens，一个月大概只需要5块钱，约 250w tokens。是真的香~

![image.png](image.png)

# 1. 创建 deepseek 开放平台 api key

进入 [https://platform.deepseek.com/](https://platform.deepseek.com/) 注册账号，中国手机号免费赠送 10￥，相当于 500万 tokens。

注册之后如下：

![image.png](image%201.png)

然后点击 【API keys】创建 api

![image.png](image%202.png)

![image.png](image%203.png)

复制好 API key 之后，就可以去 cursor 接入了。

# 2. cursor 添加模型

cmd + shift + p，输入 cursor settings，打开 cursor 设置

![image.png](image%204.png)

选择 Models 模型

![image.png](image%205.png)

滑到下面找到 OpenAI API key，下面 ①、②分别填写：

① 刚刚复制的 api key

② 填写 [`https://api.deepseek.com`](https://api.deepseek.com/)

![image.png](image%206.png)

然后点击上面的 Add Model，填写 `deepseek-chat`  回车，就可以看到 Model Names 多了一个 deepseek-chat 的模型。

最后一步，点击 Verify，等待验证 api key 成功，如果失败的话一定是复制的 key 不对，重新去复制一个就行。

![image.png](image%207.png)

# 3. 验证

然后来使用 deepseek-chat 这个模型，我们把其他的模型都关掉，只开启 deepseek-chat

![image.png](image%208.png)

现在去对话试试，比如问他来自哪里

![image.png](bb9bc877-0b0d-4fd7-969a-30dd31114e87.png)

可以看到 deepseek 已经接入成功了。