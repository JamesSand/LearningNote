## LLM Auto Eval

> 主要 focus 在 automate generate evaluation queries 上边

从下边这三篇文章开始入手

[Ruozhi Bench](https://arxiv.org/pdf/2502.13125v1) [codebase](https://github.com/LibrAIResearch/ruozhibench) [2502, arxiv, Zenan Zhai, Haonan Li, LibrAI MBZUAI] 

在一些故意的 misleading questions 上 LLM 的能力。[原始的弱智吧 codebase](https://github.com/Leymore/ruozhiba)

他们出了生成 tricky questions，还生成了这些 tricky questions 对应的 normal 版本。这样的话能够更好地对比模型在 tricky questions 和 normal questions 上边表现的差距

<img src="LLM_auto_eval/image-20250423140609983.png" alt="image-20250423140609983" style="zoom:50%;" />

Evaluation 用的是 LLM as a Judge 的 framework。但是他们发现作为 evaluator 的模型本身有时候也很难发现 subtle 的 logic fallacy。所以他们构造了一个 Multi-Choice 版本的 ruozhi benchmark

<img src="LLM_auto_eval/image-20250423142253576.png" alt="image-20250423142253576" style="zoom:50%;" />


[CHASE: How to Get Your LLM to Generate Challenging Problems for Evaluation](https://arxiv.org/pdf/2502.14678) [codebase](https://github.com/McGill-NLP/CHASE) [2502, arixv, Arkil Patel, Mila and McGill, Canada CIFAR]

用 build up hard problem with simple components 的方法逐渐构造一个困难的问题来测试 LLM

<img src="LLM_auto_eval/image-20250423142811404.png" alt="image-20250423142811404" style="zoom:50%;" />

每一个 simple component 都要能够简单到 LLM verifier 能够判断出来是否正确。

automatic 生成问题的方式值得注意一下，我们记录以下 CHASE QA 的问题生成方式。本质上他们在做的事情是先知道答案，再根据答案生成问题，然后在问题中混入一些无关的信息，让问题变成更长的 document

1. Generate diverse scenarios
2. Generate QA pairs
3. Generate irrelevant information
4. Generate document

Evaluation 的方式仍然是 LLM as a Judge

对于 CHASE code 而言，他们首先生成了很多 python help functions。然后从这些 help functions 里边选一些出来作为 answer，构造一个 algorithm 的需求。第三步是根据 answer code 生成 test python code，如果 answer code 能够 pass test function 的话，就 discard answer code，然后用 test functions 测试 LLM 能不能正确解决这个问题


[Math Perturb](https://arxiv.org/pdf/2502.06453) [2502, arixv, Kaixuan Huang, Mengdi Wang, Princeton]

<img src="LLM_auto_eval/image-20250423145728677.png" alt="image-20250423145728677" style="zoom:50%;" />

通过给原有的 math problem 做 perturbation 来增加难度，本质上是一种 extendable Evaluation 的手段。很好奇他们是怎么做 perturbation 以及 perturbation 之后的 verification 的

TODO from here




https://arxiv.org/pdf/2308.00304