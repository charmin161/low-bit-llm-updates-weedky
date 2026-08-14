# low-bit-llm-updates-weedky
低比特训推周报

## 仓库简介

本仓库用于跟踪和总结低比特大语言模型（Low-Bit LLM）在训练和推理方面的最新技术进展，每周更新。

## 内容结构

```
.
├── inference/          # 推理相关技术更新
│   └── 0814.md        # 2026年8月14日：vLLM/SGLang低比特推理周报
├── train/             # 训练相关技术更新
│   └── 0814.md        # 2026年8月14日：低比特训练技术简报
└── README.md          # 本文件
```

## 最新动态（2026年8月7日—8月14日）

### 🔥 推理亮点

#### vLLM 重要更新
- **DeepSeek-V4 MXFP4优化**：TP8分片不再强制padding，显存节省31.25 GiB/GPU，KV token容量提升82.76%
- **MI355X TurboQuant 4-bit KV解码**：专用FlyDSL kernel，比BF16 KV快7%，比原实现快4.1倍
- **Online MXFP4量化**：支持BF16/FP16 checkpoint直接启动后量化，无需预生成MXFP4 checkpoint
- **Online NVFP4 MoE修复**：解决重复reload后出现NaN/Inf的问题，对RL rollout场景至关重要
- **Blackwell FP8/MXFP4正确性修复**：针对SM100/SM120新路径的问题修复

#### SGLang 重要更新
- **MXFP8 CuTe DSL后端**：SM10x默认转向FlashInfer CuTe DSL，小batch decode性能提升最高3.8倍
- **MiniMax NVFP4路由优化**：SM100自动选择`flashinfer_trtllm_routed`后端，吞吐量提升32-35%
- **Nemotron W4A16-NVFP4**：区分W4A4与W4A16执行路径，W4A16走Marlin kernel
- **FP8激活量化JIT迁移**：从AOT迁到JIT，降低wheel依赖，H200几何平均提升9.5%
- **NVFP4 Embedding量化**：支持ModelOpt混合精度checkpoint中的embedding层NVFP4压缩

### 🚀 训练亮点

#### Transformer Engine
- **MXFP8二维量化**：新增`32×32`二维量化支持，权重转置不重新分组，仅支持Blackwell
- **NVFP4随机舍入修复**：修复SM120/121上静默输出全零的严重问题
- **MXFP8 MoE数据流优化**：保留低比特前向传播，同时为Wgrad重建转置表示

#### Model Optimizer
- **Nemotron-3.5-Lightning W4A16 NVFP4**：公开完整的4over6 QAD recipe和流程
- **TEGroupedMLP独立量化器**：每个expert独立选择scale，打破整层共享限制
- **untied lm_head修复**：修复QAD后静默回退BF16的高风险checkpoint问题

## 版本信息

- **vLLM**: v0.27.0 (8月10日) / v0.27.1 (8月11日)
- **SGLang**: v0.5.17 (8月8日)
- **PyTorch**: 2.13
- **Triton**: 3.7.1

## 关键趋势观察

1. **专用kernel路径分化**：低比特执行不再依赖通用GEMM，而是针对format × architecture × routing × shape的专用路径（FlashInfer CuTe DSL、TRTLLM routed、Marlin、DeepGEMM等）

2. **在线量化成为主流**：支持BF16/FP16 checkpoint直接启动后量化，对RL、持续学习、权重热更新场景至关重要

3. **4-bit KV Cache原生kernel竞争**：压缩格式原生attention kernel成为下一阶段竞争焦点，而非单纯存储更少bit

4. **混合精度成为常态**：同一模型不同组件使用不同量化格式（dense FP8 + routed MXFP4等）

## 推荐阅读优先级

1. ⭐⭐⭐⭐⭐ SGLang #34042：B200上测MXFP8 CuTe DSL
2. ⭐⭐⭐⭐⭐ vLLM #51473：DeepSeek-V4 MXFP4物理布局优化
3. ⭐⭐⭐⭐ MiniMax NVFP4后端选择对比
4. ⭐⭐⭐⭐ Nemotron W4A16 vs W4A16区分
5. ⭐⭐⭐ vLLM #50074：Online NVFP4重复reload稳定性

## 贡献

欢迎提交PR补充更多低比特LLM相关技术动态。

## 相关链接

- [vLLM GitHub](https://github.com/vllm-project/vllm)
- [SGLang GitHub](https://github.com/sgl-project/sglang)
- [Transformer Engine](https://github.com/NVIDIA/TransformerEngine)
- [Model Optimizer](https://github.com/NVIDIA/Model-Optimizer)

---

*最后更新：2026年8月14日*
