# MarsNet接口

## api介绍

### api目录结构

```shell
mars/
├── mars.h
├── mars_err.h
├── common.h
```

#### mars/mars.h

```c++
typedef void* HMODULE;
typedef void* HINSTANCE;
```

```c++
int MarsNetInit(HMODULE* phmod, const char* data, int size_in_byte, int dev_id, void* reserved);
```

- 简介：初始化MarsNet，创建一个全局Module句柄
- 返回值：错误码
- 参数说明：
  - `phmod`：返回的全局Module句柄
  - `data`：模型配置和参数数据
  - `size_in_byte`：模型配置和参数数据字节大小
  - `dev_id`：设备id号
  - `reserved`：保留参数

```c++
int MarsNetCreateInst(HMODULE hmod, HINSTANCE* phinst);
```

- 简介：创建一个线程级Instance句柄
- 返回值：错误码
- 参数说明：
  - `hmod`：全局Module句柄
  - `phinst`：返回的线程级Instance句柄

```c++
int MarsNetBatchInference(HMODULE hmod, const float* input, float* output, int* frame_out, int batch, int frame_in, FrameType type, HINSTANCE* hinsts);
```

- 简介：进行Batch前向推理
- 返回值：错误码
- 参数说明：
  - `hmod`：全局Module句柄
  - `input`：输入数据
  - `output`：输出数据
  - `frame_out`：输出数据帧数
  - `batch`：输入数据batch大小
  - `frame_in`：输入数据帧数
  - `type`：输入数据帧类型
  - `hinsts`：线程级Instance句柄数组

```c++
int MarsNetBatchInference(HMODULE hmod, const float* input, float* output, int* frame_out, int batch, const int* frame_in, FrameType type, HINSTANCE* hinsts);
```

- 简介：进行Batch前向推理
- 返回值：错误码
- 参数说明：
  - `hmod`：全局Module句柄
  - `input`：输入数据
  - `output`：输出数据
  - `frame_out`：输出数据帧数
  - `batch`：输入数据batch大小
  - `frame_in`：输入数据帧数数组（batch内输入数据帧数不等长）
  - `type`：输入数据帧类型（？？？）
  - `hinsts`：线程级Instance句柄数组

```c++
int MarsNetBatchInference(HMODULE hmod, const float* input, float* output, int* frame_out, int batch, const int* frame_in, const FrameType* type, HINSTANCE* hinsts);
```

- 简介：进行Batch前向推理
- 返回值：错误码
- 参数说明：
  - `hmod`：全局Module句柄
  - `input`：输入数据
  - `output`：输出数据
  - `frame_out`：输出数据帧数
  - `batch`：输入数据batch大小
  - `frame_in`：输入数据帧数数组（batch内输入数据帧数不等长）
  - `type`：输入数据帧类型
  - `hinsts`：线程级Instance句柄数组

```c++
int MarsNetGetLayerOutput(HMODULE hmod, const char* layer_str, float* output, int& out_size, int& dim);
```

- 简介：获取某一层输出
- 返回值：错误码
- 参数说明：
  - `hmod`：全局Module句柄
  - `layer_str`：层名
  - `output`：输出数据
  - `out_size`：输出数据字节大小
  - `dim`：输入数据batch大小

```c++
int MarsNetDestroyInst(HINSTANCE hinst);
```

- 简介：销毁线程级Instance句柄
- 返回值：错误码
- 参数说明：
  - `hinst`：线程级Instance句柄

```c++
int MarsNetFini(HMODULE hmod);
```

- 简介：销毁全局Module句柄
- 返回值：错误码
- 参数说明：
  - `hmod`：全局Module句柄

```c++
int MarsNetSetParameter(HMODULE hmod, const char* param, const char* value);
```

- 简介：设置全局Module句柄的参数
- 返回值：错误码
- 参数说明：
  - `hmod`：全局Module句柄
  - `param`：参数键名
  - `value`：参数值

```c++
int MarsNetGetParameter(HMODULE hmod, const char* param, char* value, int nMaxLen);
```

- 简介：获取全局Module句柄的参数
- 返回值：错误码
- 参数说明：
  - `hmod`：全局Module句柄
  - `param`：参数键名
  - `value`：参数值
  - `nMaxLen`：参数值最大长度

```c++
int MarsNetInstSetParameter(HINSTANCE hinst, const char* param, const char* value);
```

- 简介：设置线程级Instance句柄的参数
- 返回值：错误码
- 参数说明：
  - `hinst`：线程级Instance句柄
  - `param`：参数键名
  - `value`：参数值

```c++
int MarsNetInstGetParameter(HINSTANCE hinst, const char* param, char* value, int nMaxLen);
```

- 简介：获取线程级Instance句柄的参数
- 返回值：错误码
- 参数说明：
  - `hinst`：线程级Instance句柄
  - `param`：参数键名
  - `value`：参数值
  - `nMaxLen`：参数值最大长度

#### mars/mars_err.h

| 错误码                            | 值    | 涵义                           |
| --------------------------------- | ----- | --------------------------|
| MARS_SUCCESS                      | 0     | 处理成功                    |
| MARS_ERROR_GENERAL                | 10000 | 处理失败，但不指明具体错误类型 |
| MARS_ERROR_ALREADY_INIT           | 10001 | 多次初始化                  |
| MARS_ERROR_NOT_INIT               | 10002 | 未初始化                    |
| MARS_ERROR_RESOURCE_TOO_OLD       | 10003 | 使用了已被更新的资源          |
| MARS_ERROR_RESOURCE_NOT_EXIST     | 10004 | 访问的资源不存在             |
| MARS_ERROR_LOAD_FILE              | 10005 | 加载指定文件失败             |
| MARS_ERROR_INVALID_PARA           | 10006 | 使用了不合法的参数名          |
| MARS_ERROR_INVALID_PARA_VALUE     | 10007 | 使用了不合法的参数值          |
| MARS_ERROR_BUFFER_SIZE_NOT_ENOUGH | 10008 | 提供的缓存空间过小            |
| MARS_ERROR_LAYER_INPUT_SIZE_INVALID  | 10009 | 提供的层输入大小非法       |
| MARS_ERROR_LAYER_OUTPUT_DATA_INVALID | 10010 | 层输出数据非法            |
| MARS_ERROR_NET_TOPOLOGICAL_INVALID   | 10011 | 网络拓扑结构非法           |
| MARS_ERROR_NULL_HANDLE            | 10012 | 使用了空的句柄               |
| MARS_ERROR_INVALID_HANDLE         | 10013 | 使用了不合法的句柄            |
| MARS_ERROR_DONNT_SUPPORT          | 10014 | 使用了不支持的参数组合         |
| MARS_ERROR_EXCEPTION              | 10015 | 发生了异常                   |

#### mars/common.h

```c++
enum FrameType
{
  FIRST, MID, LAST, SOLE, ANY
};
```

## 快速开始

- 将训练好的模型转化为MasrNet后端格式的模型文件，参见MasrNet[模型格式转化](./model_convert.md)

- 编译一个MarsNet库

- 在应用中集成MarsNet库

1. 初始化的准备工作

```c++
//第一步，从文件中读取MasrNet后端资源
char* model_config = "model.json";
char* model_weight = "model.bin";

std::vector<char> model_buf;
build_model_buf(model_config, model_weight, model_buf);

//第二步，使用上一步得到的模型资源来初始化一个module，完成初始化工作
HMODULE hmod = NULL;
ret = MarsNetInit(&hmod, model_buf.data(), model_buf.size(), dev_id, NULL);
```

```c++
int build_model_buf(char* config_path, char* model_path, std::vector<char>& buf)
{
    int ret = 0;

    std::vector<char> conf_data;
    FILE* fp = fopen(config_path, "rb");
    int conf_size = 0;
    fseek(fp, 0L, SEEK_END);
    conf_size = (int)ftell(fp);
    conf_size += 1;
    conf_data.resize(conf_size);
    fseek(fp, 0L, SEEK_SET);
    fread(conf_data.data(), sizeof(char), conf_size, fp);
    conf_data.push_back('\0');
    fclose(fp);

    std::vector<char> model_data;
    fp = fopen(model_path, "rb");
    int model_size = 0;
    fseek(fp, 0L, SEEK_END);
    model_size = (int)ftell(fp);
    model_data.resize(model_size);
    fseek(fp, 0L, SEEK_SET);
    fread(model_data.data(), sizeof(char), model_size, fp);
    fclose(fp);

    buf.clear();
    buf.resize(sizeof(int) + conf_size + sizeof(int) + model_size);
    int offset = 0;
    memcpy(buf.data() + offset, &conf_size, sizeof(int));
    offset += sizeof(int);
    memcpy(buf.data() + offset, conf_data.data(), conf_size);
    offset += conf_size;
    memcpy(buf.data() + offset, &model_size, sizeof(int));
    offset += sizeof(int);
    memcpy(buf.data() + offset, model_data.data(), model_size);
    offset += model_size;

    return ret;
}
```

2. 前向推理准备工作

```c++
//第三步，创建前向推理instance，一个batch对应一个instance
std::vector<HINSTANCE> hinsts;
for (int i = 0; i < batch; ++i)
{
    HINSTANCE hinst = NULL;
    ret = MarsNetCreateInst(hmod, &hinst);
    hinsts.push_back(hinst);
}
```

3. 加载输入数据，进行前向推理

```c++
//第四步，加载输入数据
char* input_file = "input.dat";
int in_dim = 8;
int batch = 1;
int chunk = 10000;
FrameType type = FrameType::SOLE;
int out_frame = 0;
std::vector<float> cpu_in;
std::vector<float> cpu_out(1024 * 1024 * 1024); // large enough
load_input(input_file, cpu_in, in_dim); // already batch input

//第五步，进行前向推理
ret = MarsNetBatchInference(hmod, cpu_in.data(), cpu_out.data(), &out_frame, batch, chunk, type, hinsts.data());
```

```c++
inline int load_input(const char* file, std::vector<float>& cpu_in, int in_dim)
{
    int ret = 0;

    if (!sg::is_file_exit(file))
    {
        printf("test.cpp | load_input warnning | file is not exit, file = %s \n", file);
        return -1;
    }

    std::vector<float> tmp;
    FILE *f = fopen(file, "rb");
    printf("loading input file %s\n", file);
    fseek(f, 0L, SEEK_END);
    size_t fsize = ftell(f) / sizeof(float);
    tmp.resize(fsize);
    fseek(f, 0L, SEEK_SET);
    fread(tmp.data(), sizeof(float), fsize, f);
    fclose(f);
    printf("fsize:%d\n", fsize);

    int input_file_dim = xdim;
    if (fsize % input_file_dim != 0 || fsize == 0)
    {
        printf("input size error, size:%d\n", fsize);
        return -1;
    }
    int frame = fsize / input_file_dim;

    cpu_in.resize(frame * xdim);
    cpu_in = tmp;

    return ret;
}
```

3. 前向推理完成，回收资源

```c++
for (int i = 0; i < batch; ++i)
{
    ret = MarsNetDestroyInst(hinsts[i]);
}

ret = MarsNetFini(hmod);
```
