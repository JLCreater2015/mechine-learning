# 资源

## 🖌 1、论文

### ✏ 1.1、综述论文

* **Deep Learning on Graphs: A Survey 【**[**链接**](https://arxiv.org/pdf/1812.04202.pdf)**】**
* **Graph Neural Networks: A Review of Methods and Applications 【**[**链接**](https://arxiv.org/abs/1812.08434)**】**
* **A Comprehensive Survey on Graph Neural Networks 【**[**链接**](https://ieeexplore.ieee.org/abstract/document/9046288)**】**
* **A Gentle Introduction to Deep Learning for Graphs 【**[**链接**](https://arxiv.org/pdf/1912.12693.pdf)**】**
* **Learning Representations of Grapg Data: A Survey 【**[**链接**](https://arxiv.org/pdf/1906.02989.pdf)**】**
* **Graph Neural Networks for Small Graph and Giant Network Representation Learning: An Overview 【**[**链接**](https://arxiv.org/abs/1908.00187)**】**
* **A Survey on The Expressive Power of Graph Neural Networks 【**[**链接**](https://arxiv.org/abs/2003.04078)**】**
* **Relational inductive biases, deep learning, and graph networks 【**[**链接**](https://arxiv.org/pdf/1806.01261.pdf)**】**
* **Adversarial Attacks and Defenses on Graphs: A Review, A Tool and Empirical Studies 【**[**链接**](https://arxiv.org/abs/2003.00653v3)**】**

### ✏ 1.2、GNN Model

| 关注点 | 类别 | 典型模型 | 引用 | GitHub |
| :--- | :--- | :--- | :--- | :--- |
| **图类型** | 无向 | GNN |  |  |
| **图类型** | 有向 | ADGPM | Rethinking knowledge graph propagation for zero-shot learning | \[[code](https://github.com/cyvius96/adgpm)\] |
| **图类型** | 异构图 | GraphInception | Deep collective classification in heterogeneous information networks | \[[code](https://github.com/zyz282994112/GraphInception)\] |
| **图类型** | 带有边信息的图 | G2S | Graph-to-sequence learning using gated graph neural networks | \[[code](https://github.com/beckdaniel/acl2018_graph2seq)\] |
| **图类型** | 带有边信息的图 | RGCN | Modeling relational data with graph convolutionalnetworks | \[[code](https://github.com/MichSchli/RelationPrediction)\] / \[[code](https://github.com/masakicktashiro/rgcn_pytorch_implementation%20)\] |
| **聚合更新** | 谱方法卷积聚合 | ChebNet |  |  |
| **聚合更新** | 非谱方法卷积聚合 | MoNet |  |  |
| **聚合更新** | 非谱方法卷积聚合 | DCNN | Diffusion-ConvolutionalNeural Networks | \[[code](https://github.com/jcatw/dcnn)\] |
| **聚合更新** | 非谱方法卷积聚合 | GraphSAGE | GraphSage: Representation Learning on Large Graphs | \[[code](https://github.com/williamleif/GraphSAGE)\] / \[[code](https://github.com/williamleif/graphsage-simple)\] |
| **聚合更新** | 注意力机制聚合 | GAT | Graph attention networks | \[[code](https://github.com/PetarV-/GAT)\] |
| **聚合更新** | 门更新机制 | GRU | Gated graphsequence neural networks | \[[code](https://github.com/JamesChuanggg/ggnn.pytorch%20)\] / \[[code](https://github.com/yujiali/ggnn)\] |
| **聚合更新** | 门更新机制 | LSTM | Improved semanticrepresentations from tree-structured long short-term memory networks | \[[code](https://github.com/ttpro1995/TreeLSTMSentiment)\] |
| **聚合更新** | 跳跃式连接 | Highway GNN | Semi-supervised user geolocation via graph convolutional networks | \[[code](https://github.com/afshinrahimi/geographconv)\] |
| **聚合更新** | 跳跃式连接 | Jump Knowledge Network | Representation learning on graphs with jumping knowledge networks |  |
| **训练方法** | 接受域的控制 |  |  |  |
| **训练方法** | 采样方法 | FastGCN | FastGCN: Fast Learning with Graph Convolutional Networks via Importance Sampling | \[[code](https://github.com/matenure/FastGCN)\] |
| **训练方法** | 梯度提升方法 | Co-Training GCN |  |  |
| **训练方法** | 梯度提升方法 | Self-training GCN |  |  |
| **通用框架** | 信息传播 | MPNN | Neural message passing for quantum chemistry | \[[code](https://github.com/brain-research/mpnn)\] |
| **通用框架** | 非局部神经网络 | NLNN | Non-local neuralnetworks | \[[code](https://github.com/nnUyi/Non-Local_Nets-Tensorflow)\] / \[[code](https://github.com/search?q=Non-local+neural+networks)\] |
| **通用框架** | 图神经网络 | GN | Relational inductive biases, deep learning, andgraph networks | \[[code](https://github.com/deepmind/graph_nets)\] |

## 🖌 2、工具

| 名称 | 类型 | 适用场景 | GitHub |
| :--- | :--- | :--- | :--- |
| OpenNE | 图表示学习 | 图节点表示学习，预训练 | [https://github.com/thunlp/OpenNE](https://github.com/thunlp/OpenNE) |
| Graph\_nets | 图神经网络 | 基于关系模糊的图数据推理 | [https://github.com/deepmind/graph\_nets](https://github.com/deepmind/graph_nets) |
| DGL | 图神经网络 | 建立图数据（可以无需通过networkx）并加载常用图神经网络 | [https://github.com/jermainewang/dgl](https://github.com/jermainewang/dgl) |
| GPF | 训练流程 | 基于关系数据的数据预测（节点分类、关系预测） | [https://github.com/xchadesi/GPF](https://github.com/xchadesi/GPF) |
| networkx | 图数据预处理 | 非大规模图数据预处理 | [https://github.com/networkx/networkx](https://github.com/networkx/networkx) |
| Euler | 工业级图深度学习框架 | 工业级图数据的用户研究快速进行算法创新与定制 | [https://github.com/alibaba/euler](https://github.com/alibaba/euler) |
| PyG | 几何深度学习 | 适合于图、点云、流形数据的深度学习，速度比DGL快 | [https://github.com/rusty1s/pytorch\_geometric](https://github.com/rusty1s/pytorch_geometric) |
| PBG | 图表示学习 | 高速大规模图嵌入工具,分布式图表示学习，使用pytorch | [https://github.com/facebookresearch/PyTorch-BigGraph](https://github.com/facebookresearch/PyTorch-BigGraph) |
| AliGraph | 图神经网络 | 阿里自研，大规模图神经网络平台 | [https://github.com/alibaba/graph-learn](https://github.com/alibaba/graph-learn) |
| NSL | 图神经网络 | 主要用来训练图神经网络 | [https://www.tensorflow.org/neural\_structured\_learning/](https://www.tensorflow.org/neural_structured_learning/) |
| NGra | 图神经网络 | 支持图神经网络的并行处理框架 |  |

## 🖌 3、应用

| 领域 | 应用 | 算法 | 引用 | GitHub |
| :--- | :--- | :--- | :--- | :--- |
| **通用** | 关系预测 | RGCN | 《Modeling Relational Data with Graph Convolutional Networks》 | [rgcn](https://github.com/masakicktashiro/rgcn_pytorch_implementation) |
| **通用** | 关系预测 | SEAL | 《Link Prediction Based on Graph Neural Networks》 | [SEAL](https://github.com/muhanzhang/SEAL) |
| **通用** | 节点分类 |  |  |  |
| **通用** | 社区检测 |  | 《Improved Community Detection using Deep Embeddings from Multilayer Graphs》 |  |
| **通用** | 社区检测 | Hierarchical GNN | 《Supervised Community Detection with Hierarchical Graph Neural Networks》 |  |
| **通用** | 图分类 |  | 《Graph Classification using Structural Attention》 |  |
| **通用** | 图分类 | DGCNN | 《An End-to-End Deep Learning Architecture for Graph Classification》 | [DGCNN](https://github.com/muhanzhang/pytorch_DGCNN) |
| **通用** | 推荐 | GCN | 《Graph Convolutional Neural Networks for Web-Scale Recommender Systems》 |  |
| **通用** | 图生成 | NetGAN | 《Net-gan: Generating graphs via random walks》 |  |
| **通用** | 图生成 | GraphRNN | 《GraphRNN: Generating Realistic Graphs with Deep Auto-regressive Models》 |  |
| **通用** | 图生成 | MolGAN | 《Molgan: An implicit generative model for small molecular graphs》 |  |
| **决策优化** | 旅行商问题 | GNN | 《Learning to Solve NP-Complete Problems: A Graph Neural Network for Decision TSP》《Attention solves your tsp》 | [TSP-GNN](https://github.com/machine-reasoning-ufrgs/TSP-GNN) / [attention-tsp](https://github.com/wouterkool/attention-tsp) |
| **决策优化** | 规划器调度 | GNN | 《Adaptive Planner Scheduling with Graph Neural Networks》《Revised note on learning quadratic assignment with graph neural networks》 |  |
| **决策优化** | 组合优化 | GCN structure2vec | 《Combinatorial Optimization with Graph Convolutional Networks and Guided Tree Search》《Learning combinatorial optimization algorithms over graphs》 | [NPHard](https://github.com/IntelVCL/NPHard) |
| **交通** | 出租车需求预测 |  | 《Deep Multi-View Spatial-Temporal Network for Taxi Demand Prediction》 | [DMVST-Net](https://github.com/huaxiuyao/DMVST-Net) |
| **交通** | 交通流量预测 |  | 《Spatio-Temporal Graph Convolutional Networks:A Deep Learning Framework for Traffic Forecasting》 | [STGCN](https://github.com/FelixOpolka/STGCN-PyTorch) |
| **交通** | 交通流量预测 |  | 《DIFFUSION CONVOLUTIONAL RECURRENT NEURAL NETWORK: DATA-DRIVEN TRAFFIC FORECASTING》 | [DCRNN](https://github.com/liyaguang/DCRNN) |
| **传感网络** | 传感器布局 |  | 《Distributed Graph Layout for Sensor Networks》 |  |
| **区域安全** | 疾病传播 |  | 《Predicting and controlling infectious disease epidemics using temporal networks》 |  |
| **区域安全** | 城市人流预测 |  | 《FCCF: Forecasting Citywide Crowd Flows Based on Big Data》 |  |
| **社交网络** | 影响力预测 | GCN/GAT | 《DeepInf: Social Influence Prediction with Deep Learning》 | [DeepInf](https://github.com/xptree/DeepInf) |
| **社交网络** | 转发动作预测 |  | 《Social Influence Locality for Modeling Retweeting Behaviors》 |  |
| **社交网络** | 转发动作预测 |  | 《Predicting Retweet via Social Influence Locality》 |  |
| **文本** | 文本分类 | GCN | 《Diffusion-convolutional neural networks》《 Convolutionalneural networks on graphs with fast localized spectral filtering》《Knowledgetransfer for out-of-knowledge-base entities : A graph neuralnetwork approach》《Deep convolutional networks on graph-structured data》《Semi-supervised classification with graph convolutional networks》《Geometric deep learning on graphs and manifolds using mixture model cnns》 | [dcnn](https://github.com/RicardoZiTseng/dcnn-tensorflow) |

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x9886;&#x57DF;</th>
      <th style="text-align:left">&#x5E94;&#x7528;</th>
      <th style="text-align:left">&#x7B97;&#x6CD5;</th>
      <th style="text-align:left">&#x5F15;&#x7528;</th>
      <th style="text-align:left">GitHub</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x6587;&#x672C;&#x5206;&#x7C7B;</td>
      <td style="text-align:left">GAT</td>
      <td style="text-align:left">&#x300A;Graph attention networks&#x300B;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x6587;&#x672C;&#x5206;&#x7C7B;</td>
      <td style="text-align:left">DGCNN</td>
      <td style="text-align:left">&#x300A;Large-scale hierarchical text classification with recursively
        regularized deep graph-cnn&#x300B;</td>
      <td style="text-align:left"><a href="https://github.com/HKUST-KnowComp/DeepGraphCNNforTexts">DeepGraphCNNforTexts</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x6587;&#x672C;&#x5206;&#x7C7B;</td>
      <td style="text-align:left">Text GCN</td>
      <td style="text-align:left">&#x300A;Graph convolutional networks for text classification&#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/yao8839836/text_gcn">text_gcn</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x6587;&#x672C;&#x5206;&#x7C7B;</td>
      <td style="text-align:left">Sentence LSTM</td>
      <td style="text-align:left">&#x300A;Sentence-state LSTM for text representation&#x300B;</td>
      <td style="text-align:left"><a href="https://github.com/leuchine/S-LSTM">S-LSTM</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x5E8F;&#x5217;&#x6807;&#x6CE8;(POS, NER)</td>
      <td style="text-align:left">Sentence LSTM</td>
      <td style="text-align:left">&#x300A;Sentence-state LSTM for text representation&#x300B;</td>
      <td style="text-align:left"><a href="https://github.com/leuchine/S-LSTM">S-LSTM</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x8BED;&#x4E49;&#x5206;&#x7C7B;</td>
      <td style="text-align:left">LSTM</td>
      <td style="text-align:left">&#x300A;Improved semantic representations from tree-structured long short-term
        memorynetworks&#x300B;</td>
      <td style="text-align:left"><a href="https://github.com/ttpro1995/TreeLSTMSentiment">TreeLSTMSentiment</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x8BED;&#x4E49;&#x89D2;&#x8272;&#x6807;&#x6CE8;Syntactic</td>
      <td style="text-align:left">GCN</td>
      <td style="text-align:left">&#x300A;Encoding sentences with graph convolutional networks for semantic
        role labeling&#x300B;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x673A;&#x5668;&#x7FFB;&#x8BD1;</td>
      <td style="text-align:left">GCN</td>
      <td style="text-align:left">&#x300A;Graph convolutional encoders for syntax-aware neural machine translation&#x300B;/&#x300A;
        Exploiting semantics in neural machine translation with graph convolutional
        networks&#x300B;&quot;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x673A;&#x5668;&#x7FFB;&#x8BD1;</td>
      <td style="text-align:left">GGNN</td>
      <td style="text-align:left">&#x300A;Graph-to-sequence learningusing gated graph neural networks. &#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/beckdaniel/acl2018_graph2seq">graph2seq</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x5173;&#x7CFB;&#x62BD;&#x53D6;</td>
      <td style="text-align:left">LSTM</td>
      <td style="text-align:left">&#x300A;End-to-end relation extraction usinglstms on sequences and tree
        structures&#x300B;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x5173;&#x7CFB;&#x62BD;&#x53D6;</td>
      <td style="text-align:left">Graph LSTM</td>
      <td style="text-align:left">&#x300A;Crosssentencen-ary relation extraction with graph lstms&#x300B;/&#x300A;
        N-ary relationextraction using graph state lstm&#x300B;</td>
      <td style="text-align:left"><a href="https://github.com/freesunshine0316/nary-grn">nary-grn</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x5173;&#x7CFB;&#x62BD;&#x53D6;</td>
      <td style="text-align:left">GCN</td>
      <td style="text-align:left">&#x300A;Graph convolution over pruned dependency trees improves relation
        extraction&#x300B;</td>
      <td style="text-align:left"><a href="https://github.com/qipeng/gcn-over-pruned-trees">gcn-over-pruned-trees</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x4E8B;&#x4EF6;&#x62BD;&#x53D6;</td>
      <td style="text-align:left">GCN</td>
      <td style="text-align:left">&#x300A;Jointly multiple events extractionvia attention-based graph information
        aggregation&#x300B;/&#x300A;Graph convolutional networks with argument-aware
        pooling for event detection&#x300B;</td>
      <td style="text-align:left"><a href="https://github.com/lx865712528/JMEE">JMEE</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x6587;&#x672C;&#x751F;&#x6210;</td>
      <td style="text-align:left">Sentence LSTM</td>
      <td style="text-align:left">&#x300A;A graph-to-sequence mdel for amr-to-text generation&#x300B;</td>
      <td
      style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x6587;&#x672C;&#x751F;&#x6210;</td>
      <td style="text-align:left">GGNN</td>
      <td style="text-align:left">&#x300A;Graph-to-sequence learningusing gated graph neural networks&#x300B;</td>
      <td
      style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x6587;&#x672C;</b>
      </td>
      <td style="text-align:left">&#x9605;&#x8BFB;&#x7406;&#x89E3;</td>
      <td style="text-align:left">Sentence LSTM</td>
      <td style="text-align:left">&#x300A;Exploring graph-structured passage representation for multihop
        reading comprehension with graph neural networks&#x300B;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x793E;&#x4F1A;&#x5173;&#x7CFB;&#x7406;&#x89E3;</td>
      <td style="text-align:left">GRM</td>
      <td style="text-align:left">&#x300A;Deep reasoning with knowledge graph for social relationship understanding&#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/wzhouxiff/SR">SR</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x56FE;&#x50CF;&#x5206;&#x7C7B;</td>
      <td style="text-align:left">GCN</td>
      <td style="text-align:left">&#x300A;Few-shot learning with graph neuralnetworks&#x300B;/&#x300A;Zero-shot
        recognition via semantic embeddings and knowledge graphs&#x300B;</td>
      <td
      style="text-align:left">
        <p><a href="https://github.com/louis2889184/gnn_few_shot_cifar100">gnn_few_shot</a> 
        </p>
        <p>/ <a href="https://github.com/JudyYe/zero-shot-gcn">zero-shot-gcn</a>
        </p>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x56FE;&#x50CF;&#x5206;&#x7C7B;</td>
      <td style="text-align:left">GGNN</td>
      <td style="text-align:left">&#x300A;Multi-label zero-shot learning with structured knowledge graphs&#x300B;</td>
      <td
      style="text-align:left"><a href="https://people.csail.mit.edu/weifang/project/vll18-mlzsl/">vll18-mlzsl</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x56FE;&#x50CF;&#x5206;&#x7C7B;</td>
      <td style="text-align:left">ADGPM</td>
      <td style="text-align:left">&#x300A;Rethinking knowledge graph propagation for zero-shot learning&#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/cyvius96/adgpm">adgpm</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x56FE;&#x50CF;&#x5206;&#x7C7B;</td>
      <td style="text-align:left">GSNN</td>
      <td style="text-align:left">&#x300A;The more you know: Using knowledge graphs for image classification&#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/KMarino/GSNN_TMYN">GSNN_TMYN</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x89C6;&#x89C9;&#x95EE;&#x7B54;</td>
      <td style="text-align:left">GGNN</td>
      <td style="text-align:left">&#x300A;Graph-structured representations for visual question answering&#x300B;/&#x300A;Deep
        reasoning with knowledge graph for social relationship understanding&#x300B;
        &quot;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x9886;&#x57DF;&#x8BC6;&#x522B;</td>
      <td style="text-align:left">GCNN</td>
      <td style="text-align:left">&#x300A;Iterative visual reasoning beyond convolutions&#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/coderSkyChen/Iterative-Visual-Reasoning.pytorch">Iterative-Visual-Reasoning</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x8BED;&#x4E49;&#x5206;&#x5272;</td>
      <td style="text-align:left">Graph LSTM</td>
      <td style="text-align:left">&#x300A;Interpretablestructure-evolving lstm&#x300B;&#x300A; Semantic
        objectparsing with graph lstm&#x300B;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x8BED;&#x4E49;&#x5206;&#x5272;</td>
      <td style="text-align:left">GGNN</td>
      <td style="text-align:left">&#x300A;Large-scale point cloud semantic segmentation with superpoint
        graphs&#x300B;</td>
      <td style="text-align:left"><a href="https://github.com/loicland/superpoint_graph">superpoint_graph</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x8BED;&#x4E49;&#x5206;&#x5272;</td>
      <td style="text-align:left">DGCNN</td>
      <td style="text-align:left">&#x300A;Dynamic graph cnn for learning on point clouds&#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/af13s/dgcnn-amino">dgcnn-amino</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x56FE;&#x50CF;/&#x89C6;&#x9891;</b>
      </td>
      <td style="text-align:left">&#x8BED;&#x4E49;&#x5206;&#x5272;</td>
      <td style="text-align:left">3DGNN</td>
      <td style="text-align:left">&#x300A;3d graph neural networks for rgbd semantic segmentation&#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/yanx27/3DGNN_pytorch">3DGNN</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x751F;&#x7269;&#x79D1;&#x6280;</b>
      </td>
      <td style="text-align:left">&#x7269;&#x7406;&#x7CFB;&#x7EDF;</td>
      <td style="text-align:left">IN</td>
      <td style="text-align:left">&#x300A;Interaction networks for learning about objects, relations and
        physics&#x300B;</td>
      <td style="text-align:left">
        <p><a href="https://github.com/higgsfield/interaction_network_pytorch">interaction_network_pytorch</a>
        </p>
        <p>/ <a href="https://github.com/jaesik817/Interaction-networks_tensorflow">Interaction-network_tf</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x751F;&#x7269;&#x79D1;&#x6280;</b>
      </td>
      <td style="text-align:left">&#x7269;&#x7406;&#x7CFB;&#x7EDF;</td>
      <td style="text-align:left">VIN</td>
      <td style="text-align:left">&#x300A;Visual interaction networks: Learning a physics simulator from
        video&#x300B;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x751F;&#x7269;&#x79D1;&#x6280;</b>
      </td>
      <td style="text-align:left">&#x7269;&#x7406;&#x7CFB;&#x7EDF;</td>
      <td style="text-align:left">GN</td>
      <td style="text-align:left">&#x300A;Graph networks as learnable physics engines for inference and
        control&#x300B;</td>
      <td style="text-align:left"><a href="https://github.com/fxia22/gn.pytorch">gn.pytorch</a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x751F;&#x7269;&#x79D1;&#x6280;</b>
      </td>
      <td style="text-align:left">&#x5206;&#x5B50;&#x6307;&#x7EB9;</td>
      <td style="text-align:left">GCN</td>
      <td style="text-align:left">&#x300A;Convolutional networks on graphs for learning molecular fingerprints&#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/fllinares/neural_fingerprints_tf">neural_fingerprints</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x751F;&#x7269;&#x79D1;&#x6280;</b>
      </td>
      <td style="text-align:left">&#x5206;&#x5B50;&#x6307;&#x7EB9;</td>
      <td style="text-align:left">NGF</td>
      <td style="text-align:left">&#x300A;Molecular graph convolutions: moving beyond fingerprints&#x300B;</td>
      <td
      style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x751F;&#x7269;&#x79D1;&#x6280;</b>
      </td>
      <td style="text-align:left">&#x86CB;&#x767D;&#x8D28;&#x754C;&#x9762;&#x9884;&#x6D4B;</td>
      <td style="text-align:left">GCN</td>
      <td style="text-align:left">&#x300A;Protein interfaceprediction using graph convolutional networks&#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/fouticus/pipgcn">pipgcn</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x751F;&#x7269;&#x79D1;&#x6280;</b>
      </td>
      <td style="text-align:left">&#x836F;&#x7269;&#x526F;&#x4F5C;&#x7528;&#x9884;&#x6D4B;</td>
      <td style="text-align:left">Decagon</td>
      <td style="text-align:left">&#x300A;Modeling polypharmacyside effects with graph convolutional networks&#x300B;</td>
      <td
      style="text-align:left"><a href="https://github.com/miliana/DecagonPython3">Decagon</a>
        </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>&#x751F;&#x7269;&#x79D1;&#x6280;</b>
      </td>
      <td style="text-align:left">&#x75BE;&#x75C5;&#x5206;&#x7C7B;</td>
      <td style="text-align:left">PPIN</td>
      <td style="text-align:left">&#x300A;Hybrid approach of relation network and localized graph convolutional
        filtering for breast cancer subtype classification&#x300B;</td>
      <td style="text-align:left"></td>
    </tr>
  </tbody>
</table>

