# MarsNet网络结构配置

MarsNet网络结构配置分为3块： 网络头部信息、拓扑结构信息、Layer信息

## 网络头部信息

- 示例

```json
{
    "version": "v1.0.0",
    "name": "ocr.direction",
    "input_dim": 1,
    "output_dim": 4,
    "prefix": "",
    "mode": "brief",
    "format": "nchw",
    "data_type": "float",
    "extra":
    {
        "key": "value"
    }
}
```

- 字段说明

| 字段名 | 类型 | 描述 | 说明 |
| :------: | :------: | :------: | :------: |
| **version** | str | 模型版本号 | |
| **name** | str | 模型名称 | |
| **input_dim** | int | 输入dim | |
| **output_dim** | int | 输出dim | |
| **prefix** | str | 权重公共命名空间 | |
| **mode** | str | 是否采用brief模式 | brief模式可以去除中间IO,提升性能；<br/>要求模型无状态，且不存在多输入多输出；<br/>默认不采用|
| **format** | str | 数据格式: nhwc/nchw | 默认nhwc |
| **data_type** | str | 数据精度: float/float16/int8 | 默认float |
| **extra** | dict | 额外配置信息 | 可将模型信息返回给应用程序 |

## 拓扑结构信息

- 示例

```json
{
    "layers": [
        {
            "id": 0,
            "name": "input0",
            "inputs": []
        },
        {
            "id": 1,
            "name": "input_frame",
            "inputs": [],
        },
        {
            "id": 2,
            "name": "fc_in",
            "inputs": [0]
        },
        {
            "id": 3,
            "name": "speak_embed",
            "inputs": [2]
        },
        {
            "id": 4,
            "name": "fc_in_speak",
            "inputs": [2, 3]
        },
        {
            "id": 5,
            "name": "up_layer",
            "inputs": [1, 4]
        },
        {
            "id": 6,
            "name": "down_layer",
            "inputs": [5]
        }
    ]
}
```

layers数组表示网络结构，每条item表示一个layer。

- 字段说明

| 字段名 | 类型 | 描述 | 说明 |
| :------: | :------: | :------: | :------: |
| **id** | int | layer唯一标识 | |
| **name** | str | layer名称 | |
| **inputs** | array | layer依赖的输入 | |

## layer信息

- 示例

1. Dense Layer

```json
{
    "id": 1,
    "name": "dense0",
    "scope": "dense.",
    "type": "Dense",
    "input_dim": 1286,
    "output_dim": 512,
    "active": "Relu",
    "bias": true,
    "inputs": [0]
}
```

2. Conv1d

```json
{
    "id": 2,
    "name": "conv0",
    "scope": "cbhg.convolutions.0.0.conv1d.",
    "type": "Conv1d",
    "input_dim": 512,
    "output_dim": 512,
    "kernel_size": 5,
    "inputs": [1]
}
```

3. BatchNorm

```json
{
    "id": 3,
    "name": "bn0",
    "scope": "cbhg.convolutions.0.1.bn.",
    "type": "BatchNorm",
    "input_dim": 512,
    "output_dim": 512,
    "active": "Relu",
    "inputs": [2]
}
```

MarsNet的Layer可以是`小op`，如`Gemm`、`Conv2d`、`relu`等，也可以是`大op`，如`multi-head-attetion`、`PSEBlock`、`RestackLayer`等;

- 字段说明

| 字段名 | 类型 | 描述 | 属性 | 适用层 | 说明 |
| :------: | :------: | :------: | :------: | :------: | :------: |
| **scope** | str | 权重的scope | `公有` | all | 没有参数的layer可缺省 |
| **type** | str+enum | layer类型 | `公有` | all | 可选[类型](./support.md) |
| **input_dim** | int | 输入维度 | `公有` | all | |
| **output_dim** | int | 输出维度 | `公有` | all | |
| **active** | str+enum | 激活函数类型 | `公有`| all | Null(`默认`)<br/>Relu<br/>PRelu<br/>LeakyRelu<br/>Sigmoid<br/>Tanh<br/>Swish |
| **print** | bool | 将层输出写入文件 | `公有`| all | false(`默认`)<br/>true |
| **append** | bool | 将层输出以追加方式写入文件 | `公有`| all | false(`默认`)<br/>true |
| **math** | str+enum | 四则运算 | `特有`| ElemwiseLayer<br/>BraodCastLayer | Add<br/>Sub<br/>Mul<br/>Div|
| **bias** | bool | 是否有偏置 | `特有` | DenseLayer<br/>Conv1dlayer<br/>Conv2dLayer | false<br/>true(`默认`) |
| **alpha** | float | 尺度变换 | `特有` | ScaleLayer | |
| **beta** | float | 尺度变换 | `特有` |ScaleLayer | |
| **left** | int | 左侧pad个数 | `特有` | PaddingLayer<br/>ReflectionPadding1d | |
| **right** | int | 右侧pad个数 | `特有` | PaddingLayer<br/>ReflectionPadding1d | |
| **val** | float | pad值 | `特有` | PaddingLayer | 默认0 |
| **layers** | int | 层数 | `特有` | GRULayer<br/>LstmLayer | |
| **kernel_size** | int | 卷积核大小 | `特有` | Conv1dlayer<br/>Conv2dLayer | |
| **stride** | int | 卷积核步长 | `特有` | Conv1dlayer | |
| **dilation** | int | 卷积核扩张大小 | `特有` | Conv1dlayer | |
| **padding** | bool | 卷积核是否pad | `特有` | Conv1dlayer | false(`默认`)<br/>true |
| **stride_w** | int | 卷积核w维步长 | `特有` | Conv2dlayer | |
| **stride_h** | int | 卷积核h维步长 | `特有` | Conv2dlayer | |
| **pad_h** | int | 卷积核h维pad个数 | `特有` | Conv2dlayer | |
| **pad_w** | int | 卷积核w维pad个数 | `特有` | Conv2dlayer | |
| **eps** | float | BatchNorm稳定系数 | `特有` | BatchNormLayer | |
| **heads** | int | MultiHeadAttention的head数 | `特有` | FastSpeechMultiHeadAttentionLayer | |
