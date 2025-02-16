---
title: AgentInterface
published: 2025-01-30
tags: [AI]
category: vtuber
draft: true
---

# This Article is a Draft


```python
from abc import ABC, abstractmethod
from typing import AsyncIterator
from loguru import logger

from ..output_types import AgentOutputBase


class AgentInterface(ABC):
    """Base interface for all agent implementations"""

    @abstractmethod
    async def chat(self, prompt: str) -> AsyncIterator[AgentOutputBase]:
        """
        Chat with the agent asynchronously.

        This function should be implemented by the agent.
        Output type depends on the agent's output_type:
        - SentenceOutput: For text-based responses with display and TTS text
        - AudioOutput: For direct audio output with display text and transcript

        Args:
            prompt: str - User input transcription

        Returns:
            AsyncIterator[AgentOutputBase] - Stream of agent outputs
        """
        logger.critical("Agent: No chat function set.")
        raise ValueError("Agent: No chat function set.")

    @abstractmethod
    def handle_interrupt(self, heard_response: str) -> None:
        """
        Handle user interruption. This function will be called when the agent is interrupted.

        Args:
            heard_response: str - The part of response heard before interruption
        """
        logger.warning(
            """Agent: No interrupt handler set. The agent may not handle interruptions
            correctly. The AI may not be able to understand that it was interrupted."""
        )
        pass

    @abstractmethod
    def set_memory_from_history(self, conf_uid: str, history_uid: str) -> None:
        """
        Load the agent's working memory from chat history

        Args:
            conf_uid: str - Configuration ID
            history_uid: str - History ID
        """
        pass

```


## 导入必要的模块
```python
from abc import ABC, abstractmethod  # 用于创建抽象基类和抽象方法
from typing import AsyncIterator     # 用于异步迭代器类型提示
from loguru import logger           # 用于日志记录

from ..output_types import AgentOutputBase  # 导入代理输出的基类
```
## 导入说明:
```
ABC, abstractmethod: 用于创建抽象类和抽象方法，确保子类必须实现这些方法
AsyncIterator: 用于异步迭代器的类型提示
logger: 用于记录日志
AgentOutputBase: 代理输出的基类，定义了输出格式
```
## 类定义:
```
继承自 ABC（Abstract Base Class）
定义了所有代理必须实现的接口
```
## 抽象方法:
```
chat: 处理对话，返回异步输出流
handle_interrupt: 处理用户中断
set_memory_from_history: 从历史记录加载记忆
```
## 这个接口的作用是：
```
定义标准接口，确保所有代理实现相同的方法
提供类型提示和文档
实现错误处理和日志记录
支持异步操作
```
---
title: AgentInterface
published: 2025-01-30
tags: [AI]
category: vtuber
draft: false
---
