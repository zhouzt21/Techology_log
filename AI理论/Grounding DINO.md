# Grounding Dino

IDEA-Research/GroundingDINO: Official implementation of the paper "Grounding DINO: Marrying DINO with Grounded Pre-Training for Open-Set Object Detection" ([github.com](http://github.com))

根据人类文字输入去检测任意类别的目标，称作**开放世界目标检测问题（open-set object detection）。\**Grounding DINO can detect \*\*arbitrary objects\*\* with human inputs such as\** category names or referring expressions**. The key solution of open-set object detection is introducing language to a **closed-set detector DINO.**

- application: grounding dino + stable diffusion ⇒ (image editing)

  - stable diffusion

- grounding dino  aims: merge concepts found in the **DINO** and **GLIP**

  - **transformer-based** detection method

    performing vision-language **modality fusion** at** multiple phases**, including a **feature enhancer, a language-guided query selection module, and a cross-modality decoder**.

    - text backbone and image backbone
      - text: BERT transformer
      - image: SWIN transformer
    - objection performance and **end-to-end optimization**
    - eliminating the need for handcrafted modules like **NMS**

  - extend the evaluation of **open-set object detection** to **REC datasets.**（Referring Expression Comprehension）

  - DETR-like model DINO: (close-set detector) （√）

    - Encoder-Decoder->Attention->Transformer (√）

      - LSTM

        长短期记忆（Long short-term memory, LSTM）是一种特殊的RNN，主要是为了解决长序列训练过程中的梯度消失和梯度爆炸问题。简单来说，就是相比普通的RNN，LSTM能够在更长的序列中有更好的表现。

        相比RNN只有一个传递状态 $h^2$ ,LSTM有两个传输状态，一个  （cell state），和一个  （hidden state）。

        首先使用LSTM的当前输入xtx_txt和上一个状态传递下来的  拼接训练得到四个状态。

      - Encoder-Decoder

        - 提问：为什么要生成语义编码C？

        - 在NLP中Encoder-Decoder框架主要被用来处理序列-序列问题。也就是输入一个序列，生成一个序列的问题。这两个序列可以分别是任意长度。

          ![img](https://secure2.wostatic.cn/static/jsdZbu3rhVZE8ELEivFGAH/image.png?auth_key=1695635722-45VBX3gWC4RGRGkDXQh3WJ-0-b75e0df77439d13425dbb0c3ff7e3bb8)

          **Encoder**：编码器，对于输入的序列<x1,x2,x3…xn>进行编码，使其转化为一个语义编码C，这个C中就储存了序列<x1,x2,x3…xn>的信息。

          - 编码方式有很多种，在文本处理领域主要有RNN/LSTM/GRU/BiRNN/BiLSTM/BiGRU，可以依照自己的喜好来选择编码方式，先写了**RNN/LSTM/GRU**的详解

            以RNN为例来具体说明一下： 以上图为例，输入<x1,x2,x3,x4>，通过**RNN生成隐藏层（remain review）的状态值<h1,h2,h3,h4>**，如何**确定语义编码C**呢？最简单的办法直接**用最后时刻输出的ht作为C的状态值**，这里也就是可以用h4直接作为语义编码C的值，**也可以将所有时刻的隐藏层的值进行汇总，**然后生成语义编码C的值，这里就是C=q(h1,h2,h3,h4)，q是非线性激活函数。 得到了语义编码C之后，接下来就是要在Decoder中对语义编码C进行解码了。

          **Decoder**：解码器，根据输入的语义编码C，然后将其解码成序列数据，解码方式也可以采用RNN/LSTM/GRU/BiRNN/BiLSTM/BiGRU。Decoder和Encoder的编码解码方式**可以任意组合**。

          - https://arxiv.org/abs/1406.1078

            **Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation**

            因为**语义编码C**包含了**整个输入序列**的信息，所以在解码的每一步都引入C。文中Ecoder-Decoder均是使用RNN，在计算每一时刻的输出yt时，都应该输入语义编码C，即ht=f(ht-1,yt-1,C)，p(yt)=f(ht,yt−1,C)。ht为当前t时刻的隐藏层的值，yt-1为上一时刻的预测输出，作为t时刻的输入，每一时刻的语义编码C是相同地。

          - https://arxiv.org/abs/1409.3215

            **Sequence to Sequence Learning with Neural Networks**

            论文2的方式相对简单，只在Decoder的**初始输入引入语义编码C**，将语义编码**C作为隐藏层状态值h0的初始值**，p(yt)=f(ht,yt−1)。

          至于Decoder到底使用哪一种呢，答案是两种都不建议使用！

      - Attention

        ![img](https://secure2.wostatic.cn/static/gzgBXYnL3fBdTaDsfAt3Zf/image.png?auth_key=1695635727-3wdr1NgiL2stD2zSAeMrtw-0-5202eb3999c34963001cb6b684a59917)

        图就是引入了Attention 机制的Encoder-Decoder框架。咱们一眼就能看出上图不再只有一个单一的语义编码C，而是有多个C1,C2,C3这样的编码。

        当我们在预测Y1时，可能Y1的注意力是放在C1上，那咱们就用C1作为语义编码，当预测Y2时，Y2的注意力集中在C2上，那咱们就用C2作为语义编码，以此类推，就模拟了人类的注意力机制。

        - 怎么计算出C1，C2，C3…Cn呢？如何判断我每次在做解码的时候注意力应该放在哪个位置呢？

          - 在生成每个单词Yi的时候，原先都是相同的中间语义表示C会替换成根据当前生成单词而不断变化的Ci。
          - 以普通的RNN-RNN Encoder-Decoder结构为例，上图Decoder初始输入用**初始符，**这里使用eos作为初始符。将输入eos与初始S0通过RNN生成h(eos)，然后分别计算h(eos)与h1，h2，h3…hm的相关性
          - decoder上一时刻的输出值Yi-1与上一时刻传入的隐藏层的值Si-1通过RNN生成**Hi**，然后计算Hi与h1，h2，h3…hm的相关性，得到**相关性评分**[f1,f2,f3…fm]，然后对Fi进行softmax就得到注意力分配αij。

        - attention思想

          参照上图可以这么来理解Attention，将Source中的构成元素想象成是由一系列的**<Key,Value>数据对**构成（对应到咱们上里面的例子，key和value是相等地，都是**encoder的输出值h**），此时给定**Target中的某个元素Query**（对应到上面的例子也就是decoder中的hi），通过计算**Query和各个Key的相似性或者相关性**，得到每个**Key**对应Value的**权重系数**，然后对**Value进行加权求和**，即得到了最终的Attention数值。所以本质上Attention机制是对Source中元素的Value值进行加权求和，而Query和Key用来计算对应Value的权重系数。

          ![img](https://secure2.wostatic.cn/static/sHNQJsU8qb7Kt33Ppsgdxb/image.png?auth_key=1695635728-3H8DYTL5T7NEgCLq4Ep9GK-0-ab087a34bf24ff2d8b949643de994e73)

          优点： 1.速度快。Attention机制**不再依赖于RNN**，解决了RNN不能并行计算的问题。(?) 这里需要说明一下，基于Attention机制的seq2seq模型，因为是有监督的训练，所以咱们在**训练**的时候，在decoder阶段并不是说预测出了一个词，然后再把这个词作为下一个输入，因为**有监督训练**，**咱们已经有了target的数据**，所以**是可以并行输入的**，可以并行计算decoder的每一个输出，但是再**做预测的时候**，是没有target数据地，这个时候**就需要基于上一个时间节点的预测值**来当做下一个时间节点decoder的输入。所以节省的是训练的时间。 2.效果好。效果好主要就是因为注意力机制，**能够获取到局部的重要信息，能够抓住重点。** 缺点： 1.**只能在Decoder阶段实现并行运算**，**Encoder部分依旧采用的是RNN**，LSTM这些按照顺序编码的模型，Encoder部分还是无法实现并行运算，不够完美。 2.就是因为Encoder部分目前仍旧依赖于RNN，所以对于中长距离之间，两个词相互之间的关系没有办法很好的获取。

          为了改进上面两个缺点，更加完美的Self-Attention出现了。

        - **self-attention**

          在一般任务的Encoder-Decoder框架中，输入Source和输出Target内容是不一样的，比如对于英-中机器翻译来说，Source是英文句子，Target是对应的翻译出的中文句子，Attention机制发生在Target的元素和Source中的所有元素之间。而Self Attention顾名思义，指的不是Target和Source之间的Attention机制，而是Source内部元素之间或者Target内部元素之间发生的Attention机制，也可以理解为**Target=Source这种特殊情况下的注意力计算机制**。其具体计算过程是一样的，只是计算对象发生了变化而已，相当于是Query=Key=Value，计算过程与attention一样，所以这里不再赘述其计算过程细节。 如此引入Self Attention后会更容易捕获句子**中长距离的相互依赖的特征**，因为如果是**RNN或者LSTM，需要依次序序列计算，**对于远距离的相互依赖的特征，**要经过若干时间步步骤的信息累积**才能将两者联系起来，而距离越远，有效捕获的可能性越小。

          ![img](https://secure2.wostatic.cn/static/645o9KituQvQBgtY1JTjF5/image.png?auth_key=1695635728-bSSvzbSpt6me4vcGx9QC1u-0-22ee570140516ffda22b4077bcc8e59e)

      - Transformer

        https://blog.csdn.net/Tink1995/article/details/105080033?ops_request_misc={"request_id"%3A"169554730816800182777063"%2C"scm"%3A"20140713.130102334.."}&request_id=169554730816800182777063&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-105080033-null-null.142^v94^chatsearchT3_1&utm_term=Transformer&spm=1018.2226.3001.4187

        [Transformer](https://so.csdn.net/so/search?q=Transformer&spm=1001.2101.3001.7020)是什么？一句话来讲，就是完全利用attention机制来解决自然语言翻译问题。

        ![img](https://secure2.wostatic.cn/static/rjopN6b4Mty1rBhdNXtyMW/image.png?auth_key=1695635731-b9jA8PYytudmiBSf3r2KqV-0-6f48feb68af46ddc27af3425456c0718)

        - Transformer的inputs 输入

          - 注意到，输入inputs embedding后需要给每个word的词向量添加位置编码positional encoding: 是因为Transformer 的是完全基于self-Attention，self-attention是不能获取词语位置信息的，就算打乱一句话中词语的位置，每个词还是能与其他词之间计算attention值，就相当于是一个功能强大的词袋模型，对结果没有任何影响。
          - 正余弦位置编码。位置编码通过使用不同频率的正弦、余弦函数生成，然后和对应的位置的词向量相加，位置向量维度必须和词向量的维度一致。

        - Transformer的Encoder

          - Multi-Head Attention: 在self-attention的基础上，对于输入的embedding矩阵，self-attention只使用了一组W^Q , W^K , W^V 来进行变换得到Query，Keys，Values。而Multi-Head Attention使用多组W^Q , W^K , W^V得到多组Query，Keys，Values，然后每组分别计算得到一个Z矩阵，最后将得到的多个Z矩阵进行拼接。

            ![img](https://secure2.wostatic.cn/static/6YX6Lu9Nu5DAvG6pLtErAd/image.png?auth_key=1695635731-qAhGsn4hyszfZ8W1pmPMhW-0-6a5950e173c8ee5386df414f65cd22b9)

            - Add＆Normalize：

            Add：就是在**Z的基础上加了一个残差块X**，加入残差块X的目的是为了防止在深度神经网络训练中发生退化问题，**退化的意思就是深度神经网络通过增加网络的层**数，**Loss逐渐减小，然后趋于稳定达到饱和，然后再继续增加网络层数，Loss反而增大**。

            ResNet 残差神经网络来解决神经网络退化的问题：上图就是构造的一个残差块，可以看到X是这一层残差块的输入，也称作F(X)为残差，X为输入值，F(X)是经过第一层线性变化并激活后的输出，该图表示在残差网络中，第二层进行线性变化之后激活之前，F(X)加入了这一层输入值X，然后再进行激活后输出。在第二层输出值激活前加入X，这条路径称作shortcut连接。

            ![img](https://secure2.wostatic.cn/static/r1w8dvomCpWgkRUk6RFTeg/image.png?auth_key=1695635731-k7fswDCxMbTfcR9hmUtrNa-0-0376b890bbd5cf34b4fef85c436aa82f)

            经网络通过训练变成0是比变成X容易很多地，因为大家都知道咱们一般初始化神经网络的参数的时候就是设置的[0,1]之间的随机数嘛。所以经过网络变换后很容易接近于0。使用ResNet的网络很大程度上解决了**学习恒等映射**的问题，用学习残差F(x)=0更新该冗余层的参数来代替学习h(x)=x更新冗余层的参数。

            - Feed-Forward Networks

              全连接层公式如下：

              FFN(x)=max(0,xW_1+b_1)W_2+b_2

              这里的全连接层是一个两层的神经网络，先线性变换，然后ReLU非线性，再线性变换。这里的x就是我们Multi-Head Attention的输出Z

        - decoder:

          Decoder的输入分为两类： 一种是**训练**时的输入，一种是**预测**时的输入。(?) 训练时的输入就是已经对准备好对应的target数据。例如翻译任务，Encoder输入"Tom chase Jerry"，Decoder输入"汤姆追逐杰瑞"。 预测时的输入，一开始输入的是起始符，然后每次输入是上一时刻Transformer的输出。例如，输入""，输出"汤姆"，输入"汤姆"，输出"汤姆追逐"，输入"汤姆追逐"，输出"汤姆追逐杰瑞"，输入"汤姆追逐杰瑞"，输出"汤姆追逐杰瑞"结束。

        - output: Output如图中所示，首先经过一次线性变换，然后Softmax得到输出的概率分布，然后通过词典，输出概率最大的对应的单词作为我们的预测输出。

    - cross attention

    - vanilla self-attention   deformable self-attention

    - NMS （√）非极大值抑制

      非极大值抑制: 其思想是搜素局部最大值，抑制非极大值。

      - 在计算机视觉任务中得到了广泛的应用，例如边缘检测、人脸检测、目标检测（DPM，YOLO，SSD，Faster R-CNN）等。
      - 以目标检测为例：目标检测的过程中在同一目标的位置上会产生大量的候选框，这些候选框相互之间可能会有重叠，此时我们需要利用非极大值抑制找到最佳的目标边界框，消除冗余的边界框。
        - 根据置信度得分进行排序
        - 选择置信度最高的比边界框添加到最终输出列表中，将其从边界框列表中删除
        - 计算所有边界框的面积
        - 计算置信度最高的边界框与其它候选框的IoU。
        - 删除IoU大于阈值的边界框（intersection-over-union，即两个边界框的交集部分除以它们的并集）
        - 重复上述过程，直至边界框列表为空。

    ![img](https://secure2.wostatic.cn/static/axCnM9oTdTXbaJM5rF28Bp/image.png?auth_key=1695635716-uq6w4CsMnyNTRkVhc1UR7c-0-790e53d0d4b4a3837e21171c721d9545)

  - GLIP（ Grounded Language-Image Pre-training （目标检测+定位）（√）

    - https://blog.csdn.net/qq_43687860/article/details/129357440

    - focus on **phrase grounding** (linking **texual description** to their respective **visual representations**)**输入句子和图片，将句子中提到的物体都框出来**。

    - GLIP 模型统一了目标检测（object detection）和定位（grounding）两个任务，构建了一个统一的训练框架，从而将两个任务的数据集都利用起来。（

      定位任务

      与

      图像检测任务

      非常类似，都是去图中找目标物体的位置，目标检测为给出一张图片找出

      bounding box

      ，

      定位为给出一个图片和文本

      ，

      根据文本找出物体

      ）

      - detection 和 grouding 任务的目标函数都是由两部分损失组成，即分类损失和定位损失。

        - 定位损失不必多说，直接去计算与标注中的 GT 框的距离即可。

        - 而对于分类损失，则有所不同。

          - 对于 detection 任务来说，分类的标签是一个**类别单词**，在计算分类损失时，每个**区域框特征**与分**类头计算得到 logits**，输出 logits 经过 nms 筛选之后，与 GT 计算交叉熵损失即可。

            给定一个图片Img，通过图像的backbone得到**region embedding**，O是N*d的一个region embedding，即如果有n个bounding box 每个bounding box embedding的维度就是d。之后再接一个分类头，判断bounding box里的物体是哪个类，**分类头W是一个矩阵**，维度为c*d，c是有多少个类别，将region embedding与W**相乘得最后分类的logits S**，之后用nms把bounding box筛选一下再跟groundtruth算交叉熵得到最终的loss。

          - 对于 vision grounding 任务来说，**标签是一个句子**，不是用分类头，而是通过文本编码器得到**文本特征**，计算文本特征与区域框**特征**的相似度，得到**匹配**分数，想看看图像区域和句子里的单词是怎么匹配的。

            给定一个图片Img，通过图像的backbone得到**region embedding**，接下来输入一个句子至文本编码器得到**文本embedding**，之后文本embedding与图像的region embedding算**相似性。**

          - 作者提出，只要判断一下两个任务中什么时候是 positive match，什么时候是 negative match，就能将两个任务统一起来了。

      - 目标检测任务的训练必须要 GT 框，单独的图文对数据没法直接用

  - BERT

    *Bidirectional Encoder Representations from Transformers* (*BERT*)

  - SWIN transformer

- architecture

  ![img](https://secure2.wostatic.cn/static/wqvRtjiPicEEoQ1tDpd99e/image.png?auth_key=1695566533-rrrQ6SiUUb8yfn5iiDHJxk-0-236724c97798c65ec791bb94cfe5b09e)

  - Grounding DINO is a dual-encoder-single-decoder architecture.
    - an** image backbone **for image feature extraction
    - a** text backbone** for text feature extraction
    - a **feature enhancer** for image and text feature fusion
    - a **language-guided query selection module** for query initialization (Sec. 3.2)
    - a **cross-modality decoder** for box refinement (Sec. 3.3).预测框微调

需要继续补充的基础知识：

- loss
  - GIOU
  - contrastive loss
  - box regression and classification costs
  - auxiliary loss (DETR)
- 补充各种常用数据集的认识
- 补充基本神经网络结构
  - RPF
  - RNN   马尔科夫

其他基本概念：

- ROC

  ROC即Receiver Operating Characteristics, 中文一般翻译成“受试者工作特性曲线”。

  这是一种度量二分类性能的指标。直观来讲，ROC曲线表示的是`模型在准确识别正例`和`不把负例错误的识别成正例`这两种能力之间`相互制约的关系`(当我们需要“宁可错杀一千，也不放过一个”的时候，ROC能告诉你到底要错杀多少才能一个坏人都不放过)。

  在详细解释ROC之前需要先解释两个前置概念，即TPR（True Positive Rate）和FPR（False Positive Rate）。

  **TPR 真正率（召回率）**：找出的正例占所有的正例的比率。比如有10人换糖尿病，通过模型确诊了其中的8个，则 TPR=0.8

  **FPR 假正率**： 即所有的负例中分类错误的比例。比如有十个人没有患糖尿病（这里把患病作为正例），但是模型错误的将其中一个人误诊为患病，则FPR=0.1

  ROC即为以FPR为横轴，以TPR为纵轴的一条曲线（如下图），有了这条曲线你就能清楚的回答下面这些问题

- Backbone

  Backbone, 译作骨干网络，**主要指用于特征提取的，已在大型数据集(例如ImageNet|COCO等)上完成预训练，拥有预训练参数的卷积神经网络**，

- embedding

  Embedding 的本质是“压缩”，用较低维度的 [k 维特征](https://www.zhihu.com/search?q=k 维特征&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":1639626458})去描述有冗余信息的较高维度的 [n 维特征](https://www.zhihu.com/search?q=n 维特征&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":1639626458})，也可以叫用较低维度的 k [维空间](https://www.zhihu.com/search?q=维空间&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":1639626458})去描述较高维度的 n 维空间。在思想上，与线性代数的[主成分分析](https://www.zhihu.com/search?q=主成分分析&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":1639626458}) PCA，[奇异值分解](https://www.zhihu.com/search?q=奇异值分解&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":1639626458}) SVD 异曲同工，事实上，PCA 和 SVD 也可以叫做 Embedding 方法。

  - 颜色可以使用RGB表示法，这就是一种分布式表示
  - 具体到NLP中，词的Embedding，实际上也是一样的——每一个词都被表示成指定维度（比如300或者768）的向量，每一个维度对应词的一种语义特征。不过有一点跟颜色不同，我们很明确地知道RGB表示法中三个特征的物理意义（对应三原色），但是在NLP中，我们显然不可能从语言学角度先验地知道每一个维度具体表示哪一种语义特征，也没法知道一个Token对应的特征值具体是多少，所以这就需要通过语言模型训练来得到对应的值。
  - 揭示了 Embedding 技术压缩数据的本质 +通常是丢失信息的。

- CLIP

CLIP（Contrastive Language–Image Pre-training），是**一种基于对比的图片-文本学习的跨模态预训练模型**，由OpenAI于去年1月发布。

- DETR     Object query

  Detr是**Facebook提出来的一种目标检测结构**，使用了一种基于transformer的全新网络结构，在没有使用以往的诸如yolo之类的算法的情况下就能取得相当不错的表现，再次印证了transformer的优越性能。

  Object query 是DETR 中的一个重要概念，是指**用于检测模型输出检测目标的预测框的一个向量表示**。 在DETR 中，Object query 是由transformer 解码器产生的，它由一组预定义的向量组成，每个向量代表一个预测框。是一个embedding形式的learned anchor，目的是让网络自己根据数据集自己学习anchor。并且DETR的实验结果也证明embedding已经足够学习anchor了。

- anchor

  anchor到底是什么呢？如果我们用一句话概括——**就是在图像上预设好的不同大小，不同长宽比的参照框。**

- hugging face

  这家以emoji“抱抱脸”命名的开源创业公司，以一种连创始团队不曾预料的速度成为了AI开源社区的顶级“网红”。

  Hugging Face已经共享了超100,000个预训练模型，10,000个数据集，涵盖了 NLP、计算机视觉、语音、时间序列、生物学、强化学习等领域，以帮助科学家和相关从业者更好地构建模型，并将其用于产品或工作流程。

- zero shot

  Zero-shot learning 就是希望我们的模型能够对其从没见过的类别进行分类，让机器具有推理能力，实现真正的智能。其中零次（Zero-shot）是指对于要分类的类别对象，一次也不学习。