###### LMDeploy 的量化和部署

### 为什么需要量化

首先我们需要明白一点，服务部署和量化是没有直接关联的，量化的最主要目的是降低显存占用，主要包括两方面的显存：模型参数和中间过程计算结果。前者对应《3.2 W4A16 量化》，后者对应《3.1 KV Cache 量化》。

量化在降低显存的同时，一般还能带来性能的提升，因为更小精度的浮点数要比高精度的浮点数计算效率高，而整型要比浮点数高很多。

所以我们的建议是：在各种配置下尝试，看效果能否满足需要。这一般需要在自己的数据集上进行测试。具体步骤如下。

Step1：优先尝试正常（非量化）版本，评估效果。
如果效果不行，需要尝试更大参数模型或者微调。
如果效果可以，跳到下一步。
Step2：尝试正常版本+KV Cache 量化，评估效果。
如果效果不行，回到上一步。
如果效果可以，跳到下一步。
Step3：尝试量化版本，评估效果。
如果效果不行，回到上一步。
如果效果可以，跳到下一步。
Step4：尝试量化版本+ KV Cache 量化，评估效果。
如果效果不行，回到上一步。
如果效果可以，使用方案。**。

根据实践经验，一般情况下：

精度越高，显存占用越多，推理效率越低，但一般效果较好。
Server 端推理一般用非量化版本或半精度、BF16、Int8 等精度的量化版本，比较少使用更低精度的量化版本。
端侧推理一般都使用量化版本，且大多是低精度的量化版本。这主要是因为计算资源所限。
以上是针对项目开发情况，如果是自己尝试（玩儿）的话：

如果资源足够（有GPU卡很重要），那就用非量化的正常版本。
如果没有 GPU 卡，只有 CPU（不管什么芯片），那还是尝试量化版本。
如果生成文本长度很长，显存不够，就开启 KV Cache。
建议大家根据实际情况灵活选择方案。