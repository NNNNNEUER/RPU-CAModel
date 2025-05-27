# HiCModel

## 介绍
CModel框架，包含port, interface, module, clock的实现, 以及一个example


## 使用说明

### 编译
```
sudo apt install g++ cmake make libgtest-dev
mkdir build && cd build
cmake ..
make -j

```

### 运行
```
./runUnitTest
./runUnitTest --gtest_filter=MacArray*
```

### 环境变量
1. PRINT_LEVEL

PRINT_LEVEL与Print<log_level>搭配使用。在调用Print<>函数时，需要指定log_level，比如Print<1>。当PRINT_LEVEL大于等于log_level, 则输出信息。
```shell
export PRINT_LEVEL=1
```

Print<>函数在BasicModule中定义, 在Module内部的函数可以直接调用Print<>。
```cpp
// basic_module.h
template<uint32_t print_level>
void Print(const char* fmt, ...) {
    if (this->Context()->GetPrintLevel() >= print_level) {
        va_list args;
        va_start(args, fmt);
        vprintf(fmt, args);
        va_end(args);
    }
}

// xxx.cpp
Print<1>("cycle %ld: mac_array write m1_idx %d, n1_idx %d\n", this->Context()->GetCycle(), packet->m1_idx, packet->n1_idx);
```

BasicContext中保存PRINT_LEVEL, 实例化时获取环境变量:
```cpp
class BasicContext{
public:
    BasicContext(const uint32_t coreId, const std::string &outputPath) {
        mCoreId = coreId;
        mCycle = 0;
        mOutputPath = outputPath;
        mPrintLevel = GetEnv("PRINT_LEVEL", 0);
        printf("PRINT_LEVEL: %d\n", mPrintLevel);
    }
}
```

TopModule的BasicContext需要手动声明, 才能让Print<>函数生效
```cpp
template<typename T, int M0, int N0, int K0>
class TestMacArray : public BasicModule {
public:
    explicit TestMacArray(const std::string &name, BasicObject* parent) : BasicModule(name, parent) {
        auto ctxt = new BasicContext(0, ".");
        this->SetContext(ctxt);
    ...
}
```
# RPU-CAModel
