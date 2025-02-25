> 以下问题及解答基于OpenStation识别的常见问题。请注意，问题及解决方案可能会根据技术迭代和平台更新而有所调整。

## 1. 平台支持部署哪些模型？模型的获取方式及资源需求如何？

OpenStation支持部署多种**DeepSeek**相关模型，包括完整模型及蒸馏版模型，并提供一键部署功能。以下是支持的模型列表、下载地址及其资源需求：

| 模型                            | 参数量  | 下载地址                                                                                                                                                                   | 数据类型 | 最低显存需求（预估） |
| ----------------------------- | ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---- | ---------- |
| DeepSeek-R1                   | 671B | [HuggingFace](https://huggingface.co/deepseek-ai/DeepSeek-R1) / [魔搭社区](https://modelscope.cn/models/deepseek-ai/DeepSeek-R1/files)                                     | FP8  | 807GB      |
| DeepSeek-R1-BF16              | 671B | [HuggingFace](https://huggingface.co/unsloth/DeepSeek-R1-BF16) / [魔搭社区](https://modelscope.cn/models/unsloth/DeepSeek-r1-bf16/files)                                   | BF16 | 1612GB     |
| DeepSeek-V3                   | 671B | [HuggingFace](https://huggingface.co/deepseek-ai/DeepSeek-V3) / [魔搭社区](https://modelscope.cn/models/deepseek-ai/DeepSeek-V3/files)                                     | FP8  | 807GB      |
| DeepSeek-V3-BF16              | 671B | [HuggingFace](https://huggingface.co/unsloth/DeepSeek-V3-bf16) / [魔搭社区](https://modelscope.cn/models/unsloth/DeepSeek-V3-bf16/files)                                   | BF16 | 1612GB     |
| DeepSeek-R1-Distill-Llama-70B | 70B  | [HuggingFace](https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Llama-70B) / [魔搭社区](https://modelscope.cn/models/deepseek-ai/DeepSeek-R1-Distill-Llama-70B/files) | BF16 | 170GB      |
| DeepSeek-R1-Distill-Llama-8B  | 8B   | [HuggingFace](https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Llama-8B) / [魔搭社区](https://modelscope.cn/models/deepseek-ai/DeepSeek-R1-Distill-Llama-8B/files)   | BF16 | 21GB       |
| DeepSeek-R1-Distill-Qwen-32B  | 32B  | [HuggingFace](https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-32B) / [魔搭社区](https://modelscope.cn/models/deepseek-ai/DeepSeek-R1-Distill-Qwen-32B/files)   | BF16 | 79GB       |
| DeepSeek-R1-Distill-Qwen-14B  | 14B  | [HuggingFace](https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-14B) / [魔搭社区](https://modelscope.cn/models/deepseek-ai/DeepSeek-R1-Distill-Qwen-14B/files)   | BF16 | 36GB       |
| DeepSeek-R1-Distill-Qwen-7B   | 7B   | [HuggingFace](https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-7B) / [魔搭社区](https://modelscope.cn/models/deepseek-ai/DeepSeek-R1-Distill-Qwen-7B/files)     | BF16 | 19GB       |
| DeepSeek-R1-Distill-Qwen-1.5B | 1.5B | [HuggingFace](https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B) / [魔搭社区](https://modelscope.cn/models/deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B/files) | BF16 | 6GB        |

注：**最低显存需求**计算方式参考[TransformerMath101](https://blog.eleuther.ai/transformer-math/#total-inference-memory)，计算公式如下：

最低显存需求（预估）= 模型权重显存 × 1.2 + 推理引擎预留显存(典型值约2GB）

请确保部署节点的**总显存**尽量大于**最低显存需求**，以确保模型的稳定运行。

## 2. 除显存资源外，节点还需要满足哪些要求？

在OpenStation平台上部署**DeepSeek**服务时，除了显存要求，还需要满足以下条件：

**硬盘空间：**
- 如果使用671B的模型，需要保证节点磁盘空间大于1.6TB（BF16格式）或800GB（FP8格式）。
- 其他蒸馏模型一般需要10-200GB存储即可

**网络需求：**
- 所有计算节点需能通过管理网络互通
- 如果有防火墙策略，需要确保集群内部节点能够相互访问，集群管理节点对外开放以下端口：
  - 32206端口（管理平台）
  - 8080端口（推理服务OpenAI API Endpoint）

## 3. 服务器部署时是否可以调整推理引擎的参数？支持哪些参数调整？

在OpenStation平台上，当前支持vLLM、SGLang推理引擎，因此部分推理参数可以通过扩展参数进行调整。

* 以下是vLLM推理引擎常见的可调参数及其作用：

| 参数                         | 示例                           | 说明                                                                                                                                                              |
| -------------------------- | ---------------------------- |-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tensor-parallel-size       | --tensor-parallel-size 8     | 控制张量并行，默认值为节点的GPU数量。-在分布式部署时，该参数通常为第一个节点的GPU数量。-某些模型的attention heads或vocab\_size可能无法被节点的GPU数量整除，此时需要手动指定一个合适的值。                                                 |
| pipeline-parallel-size     | --pipeline-parallel-size 1   | 控制流水线并行，默认值为服务实例的节点数，在单机本地实例中默认为1，分布式部署时默认为节点数。-分布式部署时，默认tp（张量并行）为加速卡数，pp（流水线并行）为节点数，可以根据模型支持情况和性能影响进行调整。                                                     |
| gpu-**memory-utilization** | --gpu-memory-utilization 0.9 | 分配给模型参数的显存比例，最大为1                                                                                                                                             |
| **max-model-len**          | --max-model-len 102400       | 控制模型处理的上下文长度，未指定时将使用模型配置文件进行默认设置。                                                                                                                              |
| max-num-seqs               | --max-num-seqs 256           | 控制并发序列数，一般与服务的并发推理性能相关。-过大的值会导致显存占用较高，影响响应时延。                                                                                                                  |
| **dtype**                  | --dtype auto                 | 控制模型权重和激活数据类型，常见值包括bfloat16、float16（half）、fp8等。-默认值为auto，系统会自动选择合适的数据类型。-若模型的数据类型与GPU设备不匹配，可手动指定。例如，在NVIDIA T4上部署DeepSeek蒸馏模型时，通常需要使用--dtype=half以适应float16计算能力。 |

**在部署时，多个扩展参数可以组合使用以优化推理性能。例如：**

`--max-model-len 65536 --gpu-memory-utilization 0.98 --max-num-seqs 256`

**示例参数解析：**

`--max-model-len 65536`：设置模型上下文长度为`65536`，适用于需要处理长文本的场景。

`--gpu-memory-utilization 0.98`：分配98% GPU显存供模型参数和计算使用，确保高效资源利用。

`--max-num-seqs 256`：设置最大并发序列数为`256`，影响推理吞吐量，但会占用较高的显存。

**注意：**

`gpu-memory-utilization`设得过高可能会导致OOM（显存溢出），建议根据GPU规格适当调整。

`max-model-len`受模型架构和GPU内存限制，过大可能降低推理速度。

* 以下是SGLang推理引擎常见的可调参数及其作用：

| 参数                   | 示例                          | 说明                                                                                    |
| -------------------- | --------------------------- | ------------------------------------------------------------------------------------- |
| tp-size              | --tp-size 8                 | 控制张量并行，默认值为节点的GPU数量。-某些模型的attention heads或vocab\_size可能无法被节点的GPU数量整除，此时需要手动指定一个合适的值。 |
| chunked-prefill-size | --chunked-prefill-size 2048 | 如果OOM发生在预填充期间，尝试将`--chunked-prefill-size`降低到`4096`或`2048`                            |
| mem-fraction-static  | --mem-fraction-static 0.75  | 减少KV缓存内存池的内存使用量                                                                      |
| max-running-requests | --max-running-requests 1024 | 控制并发请求数，尝试降低会降低显存占用                                                                  |

多个扩展参数可以组合使用以优化推理性能。

## 4. 常见服务器部署问题及解决方案

### 4.1 T4等旧型号GPU部署失败

**错误信息：**
```bash
Value Error: Bfloat16 is only supported on GPU which compute capability of at least8.0.
Your Tesla T4 GPU has compute capability 7.5. You can use float16 instead by explicitly setting the `dtype` flag in CLI, for example: --dtype=half
```
**原因：**

- Bfloat16（BF16）仅支持计算能力8.0及以上的GPU（如A100、H100）。

- T4GPU（计算能力7.5）不支持Bfloat16，导致部署失败。

**解决方案：** 扩展参数中指定
```bash
--dtype half
```

### 4.2 V100系列GPU使用vLLM引擎时，推理卡顿或服务崩溃

**问题描述**：在**V100系列GPU**上使用**vLLM推理引擎**时，可能出现：

* 对话时卡顿

* 服务进程崩溃（挂掉)

**解决方案：** 
- 添加扩展参数
```bash
--enable-chunked-prefill False
```

该参数用于优化vLLM在V100上的适配性，可避免部分计算过程导致的卡顿问题。

- 更新数据类型：
V100不支持BF16格式，必须在扩展参数中手动指定：
```bash
--dtype half
```


### 4.3 671B模型实例分布式部署失败，等待资源（no resource demands）

**问题描述**：在分布式部署时，可能出现实例一直无法成功部署，报错：`no resource demands`

**请检查以下方面：**
  1. 节点间网络状况：确保所有计算节点间网络互通，可以使用ping测试对端机器。
  2. 资源配置是否正确：各节点的GPU卡总资源是否满足推理服务并发需求；另外确保各节点GPU资源没有被占用或者没有掉卡。

### 4.4 量化模型部署

量化模型可以降低计算成本，提高推理效率。在OpenStation平台上，部署量化模型需要在`服务部署扩展参数`中指定量化类型。

1. **vLLM推理引擎中的扩展参数：**

   1. **在vLLM推理引擎中，使用`--quantization type`进行量化，`type`可取以下值：**

   `aq1m,awq,deepspeedfp,tpu_int8,fp8,fbgemm_fp8,modelopt,marlin,gguf,gptq_marlin_24,gptq_marlin,awq_marlin,gptq,compressed-tensors,bitsandbytes,qqq,hqq,experts_int8,neuron_quant,ipex,quark,moe_wna16,None`

   * **部分格式需要额外指定`--load-format`，取值为：**

   `auto,pt,safetensors,npcache,dummy,tensorizer,sharded_state,gguf,bitsandbytes,mistral,runai_streamer`

2. **SGLang推理引擎中的扩展参数：**

   在SGLang推理引擎中，使用`--quantization type`进行量化，`type`可取以下值：

   `awq,fp8,gptq,marlin,gptq_marlin,awq_marlin,bitsandbytes,gguf,modelopt,w8a8_int8`

注：部署量化模型，可能存在相关优化策略，可以查阅相关社区资料通过扩展参数的方式进行设置。



# 安装部署FAQ

## 1. GPU驱动安装失败的定位及处理方法

在**安装部署**或**扩容节点**过程中，如果**GPU驱动安装失败**，可以按照以下步骤排查：

## 2. 离线部署情况下，如何新增推理引擎

1. 下载推理引擎压缩包

在离线环境下，如需新增推理引擎，请先下载对应的引擎压缩包：

| 推理引擎           | 文件名          | 说明                            | 下载地址                                                                           |
| -------------- | ------------ | ----------------------------- |--------------------------------------------------------------------------------|
| vLLM（GPU）      | vllm.tgz     | 默认内置，一般无需手动下载；支持单机和分布式部署      | [点击下载](https://fastaistack.oss-cn-beijing.aliyuncs.com/openstation/vllm.tgz)                                                                       |
| SGLang（GPU）    | sglang.tgz   | 支持GPU单机部署，对于DeepSeek等服务优化效果明显 | [点击下载](https://fastaistack.oss-cn-beijing.aliyuncs.com/openstation/sglang.tgz)   |
| vLLM（CPU-only） | vllm-cpu.tgz | CPU推理引擎，适用于纯CPU节点             | [点击下载](https://fastaistack.oss-cn-beijing.aliyuncs.com/openstation/vllm-cpu.tgz) |

* 将下载的压缩包上传到OpenStation部署节点的以下目录即可。

