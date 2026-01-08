---
draft: false
---
# Background

We had use many of AI chats, but how to build an Chat for our own?

This article will lead you to build an AI Chat page.

# Preparation

1. Register Aliyun Bailian at: [https://bailian.console.aliyun.com/](https://bailian.console.aliyun.com/)
2. Generate an API Key

![370sZHLbm7RvsTlmYsRTjMM6634I3p02cdvMCCiuXJM=.png](370sZHLbm7RvsTlmYsRTjMM6634I3p02cdvMCCiuXJM.png)

# Create Project

We use nestjs in this project, first create an nestjs project.

```bash
nest new server
```

If you has not use nestjs before, just use this official [tutorial](https://docs.nestjs.com/first-steps), it’s very easy to get start with.

Once we created out project, install dependencies first:

> I use pnpm here, feel free to use npm or yarn what you familiar
> 

```bash
cd server
# install dependencies
pnpm i
```

Then run the server

```bash
pnpm run start:dev
```

Visit `localhost:3000` and we’ll see Hello World! as response.

Then we write chat codes.

# Implement AI Chat

We’ll create an `AIChatModule` to implement our chat logics.

## Type Defination

First we define some type:

- IMessage: describe an chat message, it’s also an openai message structur

```tsx
export interface IMessage {
  role: "user" | "assistant" | "system" | 'tool';  content: string;}
```

- CompletionRequestDto

```tsx
export interface CompletionRequestDto {
  messages: IMessage[];}
```

## Create AIChatModule

Create new floder `src/modules/ai-chat`, in ai-chat floder, create 3 files:

- ai-chat.services.ts

```tsx
import { Injectable, MessageEvent, Res } from "@nestjs/common";import { ConfigService } from "@nestjs/config";import OpenAI from "openai";import { CompletionRequestDto } from "@packages/utils";import { EventEmitter2 } from "@nestjs/event-emitter";@Injectable()
export class AiChatService {
  private readonly openai: OpenAI;  private readonly model: string = "qwen-turbo";  constructor(
    private readonly configService: ConfigService,    private eventEmitterService: EventEmitter2,  ) {
    const apiKey = this.configService.get("aliyun.bailian.sk");    this.openai = new OpenAI({
      apiKey,      baseURL: "https://dashscope.aliyuncs.com/compatible-mode/v1",    });  }
  async completions(
    body: CompletionRequestDto,  ) {
    const { messages } = body;    const completion = await this.openai.chat.completions.create({
      model: this.model,      messages: messages,      stream: true,    });    return completion;  }
}
```

- ai-chat.controller.ts

```tsx
import { PlainTextRoute } from "@/common/decorator/setMetadata";import {
  BadRequestException,  Body,  Controller,  Post,  Res,} from "@nestjs/common";import { CompletionRequestDto } from "@packages/utils";@Controller("ai-chat")
export class AiChatController {
  constructor(private readonly aiChatService: AiChatService) {}
  @Post("completions")
  @PlainTextRoute()
  async chat(@Body() body: CompletionRequestDto, @Res() res: Response) {
    if (!body.messages?.length) {
      throw new BadRequestException("messages is required");    }
    const stream = await this.aiChatService.completions(body);  }
}
```

- ai-chat.module.ts

```tsx
import { Module } from "@nestjs/common";import { AiChatController } from "./ai-chat.controller";import { AiChatService } from "./ai-chat.service";import { ConfigModule } from "@nestjs/config";@Module({
  imports: [ConfigModule],  controllers: [AiChatController],  providers: [AiChatService],})
export class AiChatModule {};
```

Register this module in AppModule, edit `src/app.module.ts`

```tsx
import { Module, NestModule } from "@nestjs/common";import { AiChatModule } from "./modules/ai-chat/ai-chat.module";@Module({
  imports: [
    AiChatModule,  ],  controllers: [],  providers: [],})
export class AppModule {}
```