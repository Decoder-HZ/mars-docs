# MarsNet能力

## 算子

| MarsNet支持的op | Op对应的描述 | GPU | CPU | Arm |
| :----: | :----: | :----: | :----: | :----: |
| Active | softmax、Log、tanh | ✔︎ | | |
| Argmax | Argmax | ✔︎ | | |
| Argmin | Argmin | ✔︎ | | |
| BatchNorm | BatchNormalization | ✔︎ | | |
| Broadcast | Matrix Add/Sub/Mul/Div Vector | ✔︎ | | |
| Concat | Concat | ✔︎ | | |
| Conv | Conv1d、Conv2d、Conv3d、depthwise、group | ✔︎ | | |
| Delay | Delay | ✔︎ | | |
| Dense | MatMul | ✔︎ | | |
| Elem | Add、Sub、Mul、Div | ✔︎ | | |
| Emb | Embdding | ✔︎ | | |
| Gru | Gru | ✔︎ | | |
| Input | Const | ✔︎ | | |
| Lstm | Lstm | ✔︎ | | |
| Mean | ReduceMean | ✔︎ | | |
| Padding | Padding1d、Padding2d、ReflectionPadding1d | ✔︎ | | |
| Pooling | MaxPooling1d | ✔︎ | | |
| Relu | Relu、LeakyRelu、Selu、clip | ✔︎ | | |
| Reshape | Reshape | ✔︎ | | |
| Scale | Scale | ✔︎ | | |
| Self-Attention | MultiHead Self-Attention | ✔︎ | | |
| Sum | ReduceSum | ✔︎ | | |
| Transpose | Transpose | ✔︎ | | |

**注**：所有算子均支持多输入和流式

## 后端

Custom | MNN | PyTorch | Tensorflow | TensorRT |
:------: | :------: | :------: | :------: | :------: |
✔︎ | ✔︎ | ✔︎ | ✔︎ | ✔︎ |

## 硬件

ARM | CPU | GPU |
:------: | :------: | :------: |
✔︎ | ✔︎ | ✔︎ |

## 框架

MXNet | PyTorch |Tensorflow |
:------: | :------: | :------: |
✔︎ | ✔︎ | ✔︎ |

## 模型

TODO
