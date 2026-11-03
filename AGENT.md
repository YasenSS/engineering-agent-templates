# 项目说明

`linkerbot-transport-cpp` 是供 Linkerbot SDK 使用的 C++17 CAN / CAN FD 通信库，使用 CMake 构建、Catch2 测试。

本项目负责物理连接、共享总线、接收路由和发送调度。设备命令、协议解析和手部控制由配套 hand SDK 实现。

执行开发任务前，应阅读本文件和 [RULE.md](RULE.md)，并遵循目标仓库的 `CONTRIBUTING.md`。任务提示词见 [PROMPTS.md](PROMPTS.md)；使用时应明确将相关文件提供给 AI，不依赖工具自动加载。

## 目录结构

| 目录 | 职责 |
| --- | --- |
| `include/linkerbot/` | 公开头文件及通用通信组件 |
| `src/` | Session、传输后端和动态库加载实现 |
| `compat/` | 旧版头文件名称的兼容入口 |
| `tests/` | 离线测试和测试用动态库 |
| `examples/` | 使用示例 |
| `cmake/` | 安装、导出和打包配置 |
| `docs/zh/` | 后端配置、接口与构建指南 |

## 常用命令

以下命令在 transport 仓库根目录执行，适用于 Linux 常规构建。

```bash
# 配置：启用测试和示例
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release \
  -DLINKERBOT_COMM_BUILD_TESTS=ON \
  -DLINKERBOT_COMM_BUILD_EXAMPLES=ON

# 构建与测试
cmake --build build --parallel 4
ctest --test-dir build --output-on-failure --no-tests=error

# 运行离线示例
./build/comm-example

# 按需安装到独立目录
cmake --install build --prefix ./dist
```

测试依赖 Catch2 3。缺少依赖时，默认配置会下载指定版本；离线构建须预先安装 Catch2 或提供 `Catch2_DIR`，并设置 `-DLINKERBOT_ALLOW_DEPENDENCY_DOWNLOADS=OFF`。

Windows 构建方式以目标仓库的 `docs/zh/windows-build.md` 为准。编译器、平台和质量检查工具版本沿用仓库配置。

## 开发约定

- 优先复用现有接口、组件和测试框架，遵循 `.clang-format`、`.clang-tidy` 及周边代码风格。
- 一次修改围绕明确目标，保留用户已有修改，避免无关重构、批量格式化或依赖升级。
- 修改源文件或生成配置，不直接编辑构建产物、自动生成文件和厂商二进制文件。
- 维护 CMake 包名 `linkerbot-comm` 和链接目标 `linkerbot::comm` 的兼容性；公开接口变化必须说明影响。
- 涉及连接关闭、资源所有权、线程和回调的修改，检查异常、重入及退出路径，并按实际缺口补充回归测试。
- 文档描述必须与实现一致，区分入队、后端接受和设备执行；离线验证不得表述为实机验收。

## 完成标准

- 修改范围与任务一致，相关接口、示例及文档同步更新。
- 按修改影响完成相关检查；构建或安装配置变化需验证独立消费者。
- 失败检查已处理，或已明确记录原因、影响及未解决事项；不得通过删除有效测试或关闭检测隐藏失败。
- 交付说明包含修改内容、修改原因、验证结果及剩余限制。未经执行的检查不得标记为通过。
