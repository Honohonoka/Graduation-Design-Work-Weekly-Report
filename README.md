# GraphQA：一个图问答框架和评估基准

## 项目简介

GraphQA 是针对大语言模型（LLM）在图结构数据上进行精准推理的研究项目。本项目提出了 **GraphRAFT**（Graph Reasoning Agent with Function-call Tools），一个基于原生工具调用代理范式的 training-free 图问答框架，以及 **GraphFORD**（Graph Fundamental Operation and Reasoning Diagnostics），一个涵盖 20 种基础图任务的综合评估基准。

## 核心创新

### GraphRAFT 框架

采用 LLM 与图数据**严格隔离**的设计原则：

- 图数据以 NetworkX 程序对象的形式完全驻留于工具层，不在 LLM 上下文中出现
- LLM 仅通过 JSON Schema 约束的结构化函数调用接口间接与图交互
- 包含五个功能模块的图工具套件（Graph Explorer、Universal Query、Node Analyzer、Universal Stats、Algorithm Selector）
- 支持多种 LLM 接入方式，无需任何训练即可部署

**设计优势**：消除了图序列化的信息损失、突破了上下文长度限制、使 LLM 能专注于推理规划。

### GraphFORD 评估基准

提供统一的评测框架，填补现有文献空白：

- **20 种基础图任务**，按三个层次组织：局部结构分析（6 项）、全局结构分析（6 项）、图算法分析（8 项）
- **首次充分评估** BridgeDetection 与 MaximumClique 等长期被忽视的任务
- **自动化数据生成流水线**，支持 7 种图分布与有向/无向两种图类型
- **效率指标追踪**，同步记录 LLM 调用次数、Token 消耗和推理时间

## 实验成果

在 GraphFORD 基准上的系统性评估表明：

- **Qwen3-14B** 在全部适用任务上均取得 **100% 的精确匹配准确率**
- 工具调用范式将算法密集型任务（最短路径、PageRank、最大流）的准确率从纯 LLM 的 15%–70% 提升至 **94%–100%**
- 无需微调的前提下，优于监督微调基线 GraphWiz，与 GPT-4-turbo 多智能体系统性能相当

## 项目结构

```
GraphQA/
├── framework/          # GraphRAFT 框架实现
│   ├── agent_core/    # Agent 核心模块
│   ├── tool_suite/    # 图操作工具套件
│   ├── schema_discovery/  # 模式发现系统
│   └── data_loader/   # 数据加载模块
├── benchmark/         # GraphFORD 评估基准
│   ├── data_generator/    # 自动化数据生成
│   ├── task_configs/      # 20 种任务配置
│   └── evaluation/        # 评测脚本与指标
├── experiments/       # 实验结果与分析
│   ├── vertical_comparison/   # 纵向对比（模型规模）
│   ├── horizontal_comparison/ # 横向对比（不同范式）
│   └── ablation_study/       # 消融实验
└── docs/              # 文档与参考资料
    ├── 参考文献.bib
    ├── 工具模块分类.md
    └── 任务分层.md
```

## 快速开始

### 环境要求

- Python 3.8+
- NetworkX、LangChain、vLLM（可选，用于本地模型部署）
- CUDA 11.8+（GPU 推理）

### 基础使用

```python
from graphraft import GraphRAFT

# 初始化框架
framework = GraphRAFT(model_name="Qwen3-14B", backend="vllm")

# 加载图数据
framework.load_graph("graph_data.csv")

# 提交问题并获取答案
question = "What is the shortest path from node A to node B?"
answer = framework.query(question)
print(answer)
```

## 主要成果

| 维度 | 成果 |
|------|------|
| **框架设计** | 图-LLM 严格隔离、工具模块化分层、容错机制完善 |
| **任务覆盖** | 20 种基础图任务，填补 BridgeDetection 等评测空白 |
| **性能指标** | Qwen3-14B 全任务 100% 准确率，超越微调基线 |
| **效率评估** | 首次系统记录工具调用的计算成本（LLM 调用次数、Token 消耗） |

## 参考文献

详见 `参考文献.bib`，共包含 41 篇相关文献，涵盖：
- 图神经网络基础理论（GNN、WL 测试）
- 基于 LLM 的图推理方法（GraphWiz、GraphInstruct、Graph-ToolFormer）
- Agent 与工具调用范式（ReAct、Toolformer、原生工具调用）
- 评估基准（GraphOmni、GraphBench、GraphEval36K）

## 论文状态

本项目为北京语言大学信息科学学院本科毕业设计（2026 届）。指导教师：刘鹏远。

---

**关键词**：图问答、工具调用代理、大语言模型、图推理、评估基准
