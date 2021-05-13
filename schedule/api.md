# Schedule接口

## api介绍

### api目录结构

```shell
include/
├── schedule_api.h
├── schedule_type.h
```

### include/schedule_api.h

```c++
int SCHEAPI scheInit(const ResLoad* resLoad, SCHE_HMODULE* pHmod, void * reserved);
```

- 简介：创建一个module
- 返回值：错误码
- 参数说明：
  - `resLoad`：用于MasrNet初始化的资源buffer，见`include/schedule_type.h`
  - `pHmod`：返回的module的句柄
  - `reserved`：保留参数

```c++
int SCHEAPI scheUninit(SCHE_HMODULE hmod);
```

- 简介：销毁一个module
- 返回值：错误码
- 参数说明：
  - `hmod`：将要销毁的module的句柄

```c++
int SCHEAPI scheCreateInst(SCHE_HMODULE hmod, const ResInfo* resInfo, SCHE_INST* scheInst);
```

- 简介：创建一个instance
- 返回值：错误码
- 参数说明：
  - `hmod`：使用的module的句柄
  - `resInfo`：废弃参数，无作用
  - `scheInst`：返回的instance句柄

```c++
int SCHEAPI scheDestroyInst(SCHE_INST scheInst);
```

- 简介：销毁一个instance
- 返回值：错误码
- 参数说明：
  - `scheInst`：将要销毁的instance的句柄

```c++
int SCHEAPI scheSetParameter(SCHE_HMODULE hmod, const char* param, const char* value);
```

- 简介：设置module的指定参数
- 返回值：错误码
- 参数说明：
  - `hmod`：指定的module的句柄
  - `param`：想要设置的参数的名字
  - `value`：想要设置的的参数的值

```c++
int scheGetParameter(SCHE_HMODULE hmod, const char* param, char* value, int nMaxLen);
```

- 简介：获取module的指定参数
- 返回值：错误码
- 参数说明：
  - `hmod`：指定的module的句柄
  - `param`：想要获取的参数的名字
  - `value`：想要获取的参数的值
  - `nMaxLen`：`value`的buffer大小

```c++
int SCHEAPI scheProcess(SCHE_INST scheInst, const float* pIn, int nNumFrameIn, ScheFrameType type, float* pOut, int* pNumFrameOut);
```

- 简介：前向计算
- 返回值：错误码
- 参数说明：
  - `scheInst`：想要进行计算的instance
  - `pIn`：输入数据地址
  - `nNumFrameIn`：输入数据长度
  - `type`：输入数据类型
  - `pOut`：输出数据地址
  - `pNumFrameOut`：输出数据长度



### include/schedule_type.h

```c++
struct ResLoad
{
    const char*     pData;
    int             nDataSize;
    int             nDevId;
};
```

- 简介：用于MasrNet初始化的资源buffer，`pData`指定buffer地址，`nDataSize`指定buffer实际大小，`nDevId`指定GPU的ID

```c++
enum class ScheFrameType
{
    FIRST, MID, LAST, SOLE
};
```

- 简介：用于调用`scheProcess`时指定数据类型，每个数据类型的约定参见流式处理一节



### include/schedule_err.h

| 错误码                            | 值    | 涵义                           |
| --------------------------------- | ----- | ------------------------------ |
| SCHE_SUCCESS                      | 0     | 处理成功                       |
| SCHE_ERROR_GENERAL                | 50000 | 处理失败，但不指明具体错误类型 |
| SCHE_ERROR_ALREADY_INIT           | 50001 | 多次初始化                     |
| SCHE_ERROR_NOT_INIT               | 50002 | 未初始化                       |
| SCHE_ERROR_ALREADY_START          | 50003 | 处理已经开始                   |
| SCHE_ERROR_NOT_START              | 50004 | 处理还未开始                   |
| SCHE_ERROR_RESOURCE_NOT_EXIST     | 50005 | 访问的资源不存在               |
| SCHE_ERROR_RESOURCE_ALREADY_EXIST | 50006 | 更新的资源已经存在             |
| SCHE_ERROR_LOAD_FILE              | 50007 | 加载指定文件失败               |
| SCHE_ERROR_INVALID_PARA           | 50008 | 使用了不合法的参数名           |
| SCHE_ERROR_INVALID_PARA_VALUE     | 50009 | 使用了不合法的参数值           |
| SCHE_ERROR_NULL_HANDLE            | 50010 | 使用了空的句柄                 |
| SCHE_ERROR_INVALID_HANDLE         | 50011 | 使用了不合法的句柄             |
| SCHE_ERROR_DONNT_SUPPORT          | 50012 | 使用了不支持的参数组合         |
| SCHE_ERROR_RESOURCE_TOO_OLD       | 50013 | 使用了已被更新的资源           |
| SCHE_ERROR_BUFFER_SIZE_NOT_ENOUGH | 50014 | 提供的缓存空间过小             |
| SCHE_ERROR_FAIL                   | 50015 | 发生了严重的错误               |
| SCHE_ERROR_EXCEPTION              | 50016 | 发生了异常                     |

## 可选配置介绍

配置可通过配置文件设置，或者通过`scheSetParameter`进行设置

| 参数名                     | 设置模型输入维度                                             |
| -------------------------- | ------------------------------------------------------------ |
| sche.input_dim             | 模型的输入维度，总是需要正确设置                             |
| sche.output_dim            | 模型的输出维度，总是需要正确设置                             |
| sche.chunk_frame_num       | 流式处理时，每次处理的数据长度。更小的值可能意味着更小的时延，也总是意味着更大的计算负载 |
| sche.running_mode          | 批处理模式，0代表固定长度批计算模式；1已废弃；2代表非流式模式批计算模式；3代表任意类型批计算模式 |
| sche.max_batch_size        | 批计算的最大批大小，更大的批可能意味着更高的吞吐，也可能带来更大的存储占用 |
| sche.left_chunk_frame_num  | 流式计算中，每个chunk和上一个chunk的重复帧                   |
| sche.right_chunk_frame_num | 流式计算中，每个chunk和下一个chunk的重复帧                   |
| sche.block_mode            | 流式处理时，在能够有计算结果时，是否阻塞等待计算结果。0代表阻塞，1代表不阻塞，2代表在第一次计算结果返回前等待，然后不再阻塞 |
| sche.out_to_in_frame_ratio | 输出数据长度和输入数据长度的比值的最大值，总是应该设置为充分大 |

## 快速开始

- 将训练好的模型转化为MasrNet后端格式的模型文件，这部分可参见MasrNet关于模型格式转化的[资料](../marsnet/model_convert.md)

- 编译一个调度模块的库文件，可以使用`src/make.sh`脚本来从源码编译

- 在推理服务上集成调度模块，下面是一个示例

1. 初始化的准备工作

```c++
    //第一步，从文件中读取MasrNet后端资源
    std::vector<char> buf;
    std::string model_config;
    ret = read_text("model.json", model_config);
    if (ret != 0)
    {
        printf("error read model config\n");
        return 0;
    }

    std::vector<char> model_weight;
    read_binary("model.bin", model_weight);
    if (ret != 0)
    {
        printf("error read model weight\n");
        return 0;
    }

    init_model_cpu(model_config, model_weight, buf);

    //第二步，使用上一步得到的模型资源来初始化一个module
    ResLoad res;
    res.nDataSize = buf.size();
    res.pData = buf.data();
    res.nDevId = 0;

    SCHE_HMODULE hmod;
    ret = scheInit(&res, &hmod, NULL);
    if (ret != 0)
    {
        printf("error scheInit\n");
        return 0;
    }

    //第三步，对第二步得到的module设置一些必要的参数，至此为止完成了准备工作
    constexpr int MAX_BUF_LEN = 200;
    char para[MAX_BUF_LEN];
    scheGetParameter(hmod, "infer.input_dim", para, MAX_BUF_LEN);
    int indim = std::stoi(para);
    scheGetParameter(hmod, "infer.output_dim", para, MAX_BUF_LEN);
    int outdim = std::stoi(para);

```

```c++
void init_model_cpu(const std::string& model_json, const std::vector<char>& model_data, std::vector<char>& model_buf)
{
    int model_size = model_data.size();
    int conf_size = model_json.length() + 1;
    model_buf.clear();
    model_buf.resize(sizeof(int) + conf_size + sizeof(int) + model_size);
    int offset = 0;
    memcpy(model_buf.data() + offset, &conf_size, sizeof(int));
    offset += sizeof(int);
    memcpy(model_buf.data() + offset, model_json.c_str(), conf_size);
    offset += conf_size;
    memcpy(model_buf.data() + offset, &model_size, sizeof(int));
    offset += sizeof(int);
    memcpy(model_buf.data() + offset, model_data.data(), model_size);
    offset += model_size;
}

template <typename T>
int read_binary(const std::string& file, std::vector<T>& data)
{
    FILE* fp = fopen(file.c_str(), "rb");
    if (fp == nullptr)
    {
        return -1;
    }
    int size = 0;
    fseek(fp, 0L, SEEK_END);
    size = (int)ftell(fp);
    data.resize(size / sizeof(T));
    fseek(fp, 0L, SEEK_SET);
    fread(data.data(), sizeof(T), size / sizeof(T), fp);
    fclose(fp);
    return 0;
}

int read_text(const std::string& file, std::string& text)
{
    std::ifstream t(file);
    if (!t.good())
    {
        return -1;
    }
    std::stringstream buffer;
    buffer << t.rdbuf();
    text = buffer.str();
    return 0;
}
```

2. 对输入进行前向计算

```c++
//非流式计算示例
//hmod module的句柄
//indim 模型的输入维度，在第一步获得
//outdim 模型的输出维度，在第一步获得
//in 模拟输入数据
void run(const SCHE_HMODULE hmod, const int indim, const int outdim, const std::vector<float>& in)
{
    int ret = 0;
    // 第一步，创建一个instance
    SCHE_INST hinst;
    ResInfo res_info;
    res_info.strSpeakerId = "default";
    ret = scheCreateInst(hmod, &res_info, &hinst);
    if (ret != 0)
    {
        printf("error scheCreateInst\n");
        return;
    }

    int in_frame = in.size() / indim;
    std::vector<float> out(in_frame * outdim);

    int out_frame = 0;
    ret = scheProcess(hinst, in.data(), in_frame, ScheFrameType::SOLE, out.data(), &out_frame);
    if (ret != 0)
    {
        printf("error scheProcess\n");
        return;
    }

  	//do anything with output

    //第三步，销毁一个instance
    ret = scheDestroyInst(hinst);
    if (ret != 0)
    {
        printf("error scheDestroyInst\n");
        return;
    }
}
```

```c++
//流式计算示例
//hmod module的句柄
//indim 模型的输入维度，在第一步获得
//outdim 模型的输出维度，在第一步获得
//in 模拟输入数据
void run_stream(const SCHE_HMODULE hmod, const int indim, const int outdim, const std::vector<float>& in)
{
    int ret = 0;
    // 第一步，创建一个instance
    SCHE_INST hinst;
    ResInfo res_info;
    res_info.strSpeakerId = "default";
    ret = scheCreateInst(hmod, &res_info, &hinst);
    if (ret != 0)
    {
        printf("error scheCreateInst\n");
        return;
    }

    int in_frame = in.size() / indim;
    std::vector<float> out(in_frame * outdim);

    int total_frame = 0;
    int stream_data_len = 0;
    for (int cur_frame = 0, next_frame = 0; cur_frame < in_frame; cur_frame = next_frame)
    {
        ScheFrameType type = ScheFrameType::SOLE;
        stream_data_len = rand() % (in_frame - total_frame) + 1; //模拟流式场景下，每次输入的数据长度
        next_frame = cur_frame + stream_data_len;

        if (next_frame > in_frame)
        {
            next_frame = in_frame;
        }

        if (cur_frame == 0 && next_frame == in_frame)
        {
            type = ScheFrameType::SOLE;
        }
        else if (cur_frame == 0)
        {
            type = ScheFrameType::FIRST;
        }
        else if (next_frame == in_frame)
        {
            type = ScheFrameType::LAST;
        }
        else
        {
            type = ScheFrameType::MID;
        }

        int out_frame = 0;
        ret = scheProcess(hinst, in.data() + cur_frame * indim, next_frame - cur_frame, type, out.data() + total_frame * outdim, &out_frame);
        total_frame += out_frame;

        //do anything with output
    }


    //第三步，销毁一个instance
    ret = scheDestroyInst(hinst);
    if (ret != 0)
    {
        printf("error scheDestroyInst\n");
        return;
    }
}
```

3. 处理完成，回收资源

```c++
scheUninit(hmod)
```