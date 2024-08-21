---
title: Zed 编辑器自定义 Assistant API Endpoint
description: 在 Zed.dev 编辑器中配置自定义 LLM API Endpoint 以启用 Inline Assisant 等功能
published: 2024-08-21
category: Dev
tags: [IDE, Tips]
language: zh
---

在 Zed 编辑器内使用 `Command + ,` 或 `Ctrl + ,` 快捷键打开 `settings.json` 设置文件，添加以下内容:

```json
{
  "language_models": {
    "some-provider": {
      "api_url": "http://localhost:11434/v1"
    }
  }
}

```

其中，`some-provider` 可为 `anthropic`, `google`, `ollama`, `openai` 任意之一。

后续即可在编辑器内搭配自定义 LLM API Endpoint 进行代码补全等操作。

> [!NOTE]
> 更多配置请见 [Assistant Configuration - Zed Docs](https://zed.dev/docs/assistant/configuration)
