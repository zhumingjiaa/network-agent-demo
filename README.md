# 🕸️ Auto-NetOps: 基于大模型与多智能体集群的企业级网络架构推演系统

[![GitHub Repo](https://img.shields.io/badge/GitHub-项目源码-181717.svg?style=for-the-badge&logo=github)](https://github.com/zhumingjiaa/network-agent-demo)
[![Agent Framework](https://img.shields.io/badge/Framework-Multi--Agent_Swarm-blue.svg?style=for-the-badge)]()
[![Token Usage](https://img.shields.io/badge/Token_Consumption-Massive_Scale-red.svg?style=for-the-badge)]()

### 1. 项目解决的核心痛点（为什么需要复杂的 AI 介入？）
在大型园区或数据中心的网络工程规划与运维中，工程师面临极端的“知识爆炸”与“配置地狱”。
* **异构设备的知识壁垒**：不同厂商（如华为、H3C 等）的路由协议（OSPF/BGP/IS-IS）底层实现、硬件转发表项（MAC/ARP/路由表）容量限制各不相同。人工阅读动辄数千页的英文技术白皮书和底层通信 RFC 文档效率极低。
* **高昂的试错成本**：传统的网络架构设计缺乏前置的逻辑验证。一旦 VLAN 划分失误或路由环路引发广播风暴，将导致严重的生产事故。
本项目旨在打造一个具备**“图纸设计 -> 海量文档解析 -> 虚拟环境推演 -> 代码级配置生成”**全链路能力的 AI 专家网络，替代传统高阶网络架构师的部分工作。

### 2. 核心逻辑流（高复杂度长链推理与多 Agent 协作）
本系统摒弃了简单的单向对话，采用了 **Hierarchical Multi-Agent Swarm（分层多智能体集群）** 架构，并深度集成虚拟仿真环境（如 eNSP），核心流转如下：

* **1️⃣ 超长上下文 RAG 与知识摄取层 (Knowledge Ingestion Agent)**
  * **机制**：接管数百份 PDF 格式的厂商产品手册、数据通信白皮书及历史故障工单。利用长上下文窗口（Long-Context）并行处理海量文本，提取硬性指标（背板带宽、端口形态、协议支持度），构建网络专用的向量数据库。
* **2️⃣ 多智能体辩论与拓扑推演层 (Debate & Reasoning Agents)**
  * **机制**：面对用户需求（如“设计一个支撑 5000 终端的高可用园区网”），系统会实例化多个角色 Agent（**路由专家、安全专家、QoS 流量专家**）。
  * **复杂互动**：路由专家可能提议使用 OSPF 多区域划分，而安全专家会根据零信任架构提出异议。各个 Agent 必须通过 **Self-Refine（自我修正）和相互辩论**，在 Token 密集型的多轮对话中，最终达成一份兼顾性能与安全的物理拓扑与逻辑架构蓝图。
* **3️⃣ 沙盒仿真与自动纠错闭环 (Execution & Feedback Loop)**
  * **机制**：这也是本系统最核心的“长链推理”体现。配置生成 Agent (CLI Coder) 会输出具体的设备配置脚本。随后，验证 Agent 会将这些脚本虚拟导入仿真沙盒（如基于 eNSP 的虚拟拓扑），并读取沙盒返回的日志（如“接口起不来”、“路由未收敛”）。
  * **闭环**：验证 Agent 会将错误日志作为 Observation（观察），重新输入给大模型进行反思（Thought），修改配置脚本后再次下发，直到全网互通。这种 ReAct 循环机制极大地提升了输出的可靠性。

### 3. 为什么本项目对大模型 Token 额度有极高需求？
* **超大规模 Prompt**：为了确保多厂商设备配置语句的绝对精准，每个 Agent 的 System Prompt 都预置了极其详尽的底层网络工程规范与华为/H3C 设备的 CLI 语法树。
* **极深的上下文交互**：多智能体之间的架构辩论、沙盒日志的循环读取与报错分析（一次报错可能包含上千行的路由表或抓包日志），需要消耗极其庞大的上下文 Token 吞吐量。
* **高并发分析**：在处理大型网络的路由协议震荡分析时，需并行处理多个节点的路由更新包（Update Packet）特征，对 API 的并发请求和 Token 消耗处于企业级规模。
