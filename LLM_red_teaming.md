
## LLM Red Teaming

### Greedy Coordinate Gradient (GCG)

[paper link](https://arxiv.org/abs/2307.15043v2)  [codebase](https://github.com/llm-attacks/llm-attacks)

[lightweight pytorch implementation of GCG](https://github.com/GraySwanAI/nanoGCG/tree/main)

利用梯度信息调整 suffix，使得模型输出想要的答案。

通过不同模型之间的联合 optimize，找到适合大多数模型的 suffix

---

以下是 paper 一些原文

<img src="LLM_red_teaming/image-20250414150742632.png" alt="image-20250414150742632" style="zoom:50%;" />

这里问题定义为需要 optimize prompt suffix，使得模型的输出是 Sure, here is how to build a bomb. 我们需要关注的是去 optimize equation(4)

给出的算法如下

<img src="LLM_red_teaming/image-20250414151047222.png" alt="image-20250414151047222" style="zoom:50%;" />

首先对每个位置选出 top-K 个可能的 cadidate

然后用一个 batch，对于 batch 里边的每个 x，随机选取一个位置，在这个位置中随机选取一个 potential 的字符进行替换。经过 T 轮迭代

当然上述算法很容易拓展到 multi model （注意这里是多模型，不是多模态）的版本。

这里本质上是拿白盒模型选出这样的 prompt，然后直接放到黑盒模型里边用

### Probe Sampling for accelerate GCG

[paper link](https://arxiv.org/pdf/2403.01251)   [codebase](https://github.com/zhaoyiran924/Probe-Sampling)

当小的 prompt model 和大的 model 的 logits 足够相似的时候，用小的 draft model 去帮助 filt out candidate prompt。

之前 GCG 的问题是对于 B 个 Greedy selection，需要 B 次 forward 来确定 probe。通常这里的 B = 512，太大了，是性能的瓶颈

<img src="LLM_red_teaming/image-20250414155938198.png" alt="image-20250414155938198" style="zoom:50%;" />

这里用了一个小的 draft model 来 filt 需要在 large model 里边测试的 candidate

注意第 15 行，filtered set 的选择是根据 draft model loss 从低到高选择的。具体的数量是通过 $(1 - \alpha) B / R$ 确定的

这里的 agreement score 是用 Spearman rank correlation 计算的

<img src="LLM_red_teaming/image-20250414160154957.png" alt="image-20250414160154957" style="zoom:50%;" />



### Introduction

[docs link](https://www.promptfoo.dev/docs/red-team/)

LLM red teaming 分为 model 本身和 application level 两个层面

攻击方式分为 white box and black box

white box 里边比较常见的是 greedy coordinate descent 和 AutoDAN (TODO: learn these two)

几种 red team 的分类
- Privacy violations [2022 work](https://arxiv.org/pdf/2202.03286), 用 LLM 来攻击 LLM 获取 personal identifiable information (PII) 
- Prompt injection 将不受信任的输入和收信任的，由开发人员构建的 prompt 链接在一起。[2023 的一篇 prompt to sql 攻击的 paper](https://arxiv.org/abs/2308.01990)
- Jailbreak [用 tree of pruning 的方式找到能够攻击的 prompt](https://arxiv.org/abs/2312.02119)
- Generate unwanted contents: 如何防止模型生成人们不想要的内容，也是 LLM Red Team 的一个方向

**Best Practice**

在进行 red team 之前需要 define 好以下几个方面
1. Vurability focus
2. 在模型生命周期的哪个阶段 red team: model testing; pre-deployment; CI/CD; post-depolyment
3. 能用来 red team 的 resource 有多少。这里可以做 resource constraint 的 red team
4. 



## LLM Workflow Automation

### LLM-AutoDiff

[paper link](https://arxiv.org/pdf/2501.16673)  [codebase](https://github.com/SylphAI-Inc/AdalFlow) (code 在这里边，具体怎么用还得看看)

这个 paper 考虑的是不只一个 LLM 情况下的 optimization，他们把 LLM system 定义成下边的样子

<img src="LLM_red_teaming/image-20250414165226395.png" alt="image-20250414165226395" style="zoom:50%;" />

N 是系统里不同的 LLM 的数量，$\epsilon$ 是 LLM 之间互相连接的边。

他们 define 了一个奇怪的 Textual Loss 

<img src="LLM_red_teaming/image-20250414165508561.png" alt="image-20250414165508561" style="zoom:50%;" />

这里奇怪的地方在于 $e_{\mathrm{desc}, v}$ 是对模型输出错误的 text Description。我不确定这个东西怎么能被用来算 loss

LLM system 里边除了 LLM 之外还有一些 Non-LLM 的东西，比如说 retriver 和 combinelist。这种东西在 back propagate 的时候需要把 gradient 传递给对应正确的 predecessor

<img src="LLM_red_teaming/image-20250414170113559.png" alt="image-20250414170113559" style="zoom:50%;" />

![image-20250414170426575](LLM_red_teaming/image-20250414170426575.png)

这是整个的算法。two stage validation 就是先用一个 minibatch verify 一下，如果通过了再用真正的 full set verify。这里最大的问题是怎么通过 optimizer LLM generate new prompt？

说了半天 optimizer LLM 用的是 ORPO ，只不过增加了 components awareness，以及所谓的 text gradient history

[OPRO Optimization by prompting](https://arxiv.org/pdf/2309.03409)



## LLM Automatic Prompt Optimization

### Amazon Survey

[paper link](https://arxiv.org/pdf/2502.16923) 

TODO: read this survey

