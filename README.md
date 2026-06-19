# AgentMemory

四层闭环记忆系统 (L1 工作记忆 / L2 短期 / L3 长期向量 / L4 知识图谱), 多 provider 自适应.

> **V 6/19 18:26 README 修真** (SOP #34 跨仓 L1 对比, 4 次修真):
> - 失实 1: `from agentmemory import MemoryStore` — MemoryStore 不存在
> - 失实 2: `from agentmemory import MiniLMEmbedder` — MiniLMEmbedder 在 `agentmemory.providers.embedder.minilm`
> - 失实 3: `Memory(embedder=...)` — Memory 是 ABC, 不可直接实例化
> - 失实 4: `provider.query()` — 实际是 `search()`
> - **真实 API**: YintaMemoryProvider.add/search/update/delete/get/history/reset

## Install (editable)

```bash
pip install -e .
```

## 快速使用

```python
from agentmemory.extensions.adapters.yinta_memory_provider import YintaMemoryProvider

provider = YintaMemoryProvider(
    root_dir='~/am_yinta_data',   # 存 _root (Path)
    library_name='memory_library',
    whitelist=['default', 'work', 'personal'],
)

# add: content 是 str | list[str], 返 list[str] (memory IDs)
memory_ids = await provider.add(
    content="今天学了 AgentMemory 的四层架构",
    user_id="浮光",
)

# search: 返 list[SearchResult]
results = await provider.search(
    query="AgentMemory 架构",
    user_id="浮光",
    top_k=3,
)
```

## 底层组件 (V 6/19 18:26 L1 验证)

```python
from agentmemory.providers.storage.sqlite import SQLiteStorage
from agentmemory.providers.embedder.minilm import MiniLMEmbedder

storage = SQLiteStorage(db_path='agentmemory.db', audit=True)
embedder = MiniLMEmbedder(model_name='sentence-transformers/all-MiniLM-L6-v2')
```

## 顶层 API (L1 验证)

```python
from agentmemory import (
    # 抽象
    MemoryProvider, Memory, Embedder, VectorStore, GraphStore,
    LLMProvider, FileStore, Retriever, FactExtractor, Reranker,
    # 安全
    PIIRedactor, RateLimiter, CircuitBreaker,
    # 可观测
    get_logger, MetricsCollector, EventBus,
    # 默认实现 (Factory alias)
    DefaultEmbedder,    # → MiniLMEmbedder
    DefaultStorage,     # → SQLiteStorage
    DefaultLLM, DefaultVectorStore, DefaultGraphStore,
    DefaultDecay, DefaultExtractor, DefaultReranker,
    # Pipeline
    EmbedPipeline, ExtractPipeline, DecayPipeline,
    # 错误
    AgentMemoryError, AuthenticationError, ConfigurationError,
)
```

## 文档

详见 `agentmemory/` 包内每个模块的 docstring.
