## 概要

C++ 目前来说没有官方的包管理方案，好在微软出了一个叫 vcpkg 的工具，试用了一下感觉还不错。 现在记录一下简单的使用方式，防止后面忘记了。

---


## 项目

1、创建项目目录
```bash
mkdir cpps && cd cpps
```

2、添加代码 main.cpp
```c++

#include <fmt/core.h>

int main()
{
    fmt::print("hello vcpkg!\n");
    return 0;
}
```

3、添加 cmake 配置文件 CMakeLists.txt
```cmake
cmake_minimum_required(VERSION 3.10)

project(cpps)

find_package(fmt CONFIG REQUIRED)

add_executable(cpps main.cpp)

target_link_libraries(cpps PRIVATE fmt::fmt)
```

4、编译
```bash
mkdir build && cd build
cmake -DCMAKE_TOOLCHAIN_FILE=${VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake ..

-- The C compiler identification is GNU 11.3.1
-- The CXX compiler identification is GNU 11.3.1
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done (0.3s)
-- Generating done (0.0s)
-- Build files have been written to: /tmp/cpps/build

cmake --build .
[ 50%] Building CXX object CMakeFiles/cpps.dir/main.cpp.o
[100%] Linking CXX executable cpps
[100%] Built target cpps
```

5、运行
```bash
./cpps 
hello vcpkg!
```

---

## 问题-1

虽然上面的处理方式已经可以正常的编译运行的，但是还是一个问题；那就是没有版本控制，一般我们在创建项目的时候会用 vcpkg 把依赖也声明下。

```bash
mkdir cpps && cd cpps

vcpkg new --application
vcpkg add port fmt
```

---

## 问题-2

增加预设文件 CMakePresets.json

```json
{
  "version": 2,
  "configurePresets": [
    {
      "name": "default",
      "generator": "Unix Makefiles",
      "binaryDir": "build",
      "cacheVariables": {
        "CMAKE_TOOLCHAIN_FILE": "/usr/local/vcpkg/scripts/buildsystems/vcpkg.cmake"
      }
    }
  ]
}
```
到这里 vscode 就能识别了，我们只需要在页面上点一点就能完成编译运行了，但是我还是喜欢命令行的方式。
```bash
cmake --preset=default && cmake --build build
```

---

## 项目结构

``` bash
tree cpps
cpps
├── CMakeLists.txt
├── CMakePresets.json
├── main.cpp
├── vcpkg-configuration.json
└── vcpkg.json

0 directories, 5 files
```