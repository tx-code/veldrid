# Veldrid 维护路线图（imos-dev）

本文档用于跟踪我们在 `imos-dev` 分支上长期维护 Veldrid 的计划，以及各阶段需要完成的关键工作与验证步骤。请在每次阶段推进后更新状态。

## 总体目标
- 基于现有 Veldrid 代码，吸收 ppy/veldrid 中对 Vulkan / OpenGL 后端的优化，使其适配最新 .NET 生态。
- 逐步切换到底层使用 Silk.NET 绑定，统一原生调用入口，降低自维护成本。
- 维护稳定的 Vulkan 主后端，并保留 OpenGL / OpenGL ES 作为备选，确保跨平台应用可用。
- 精简仓库结构，只保留实际需要的模块与演示，完善自动化构建与回归验证。

## 阶段规划

### 阶段 1：基础环境升级（进行中）
- **目标**：使核心项目面向 .NET 8 / C# 12，补全包信息，消除行尾差异。
- **任务**
  - 更新 `src/Veldrid/Veldrid.csproj`：改为 `TargetFramework=net8.0`，提升 `LangVersion`，添加 NuGet 元数据。
  - 调整 `Directory.Build.props`，移除已废弃的依赖版本常量，更新版权 / 作者 / 仓库信息。
  - 清理 `.editorconfig` 与 `.gitattributes`，完成 `git add --renormalize .`（已完成）。
- **验证**：`dotnet build src/Veldrid.sln`；对 NeoDemo / Triangle 等示例至少做编译或运行冒烟测试。

### 阶段 2：Vulkan 后端增强
- **目标**：吸收 ppy 分支中与 Vulkan 相关的关键修复与特性。
- **任务**
  - 引入 Swapchain 撕裂支持（`AllowTearing`）、刷新队列逻辑和 fence 管理优化。
  - 同步 `BackendInfoVulkan`、`VkGraphicsDevice` 中的资源生命周期 / barrier / debug 调整。
  - 整理 `VkSwapchain`, `VkCommandList`, `VkDeviceMemoryManager` 等涉及的提交，选择 cherry-pick 或手工复刻。
- **验证**：Vulkan Demo 连续渲染、窗口 resize、开启/关闭垂直同步；开启 Validation Layers 检查是否仍有警告。

### 阶段 3：OpenGL / OpenGL ES 精简
- **目标**：对齐 ppy 的 OpenGL 清理，减少额外分配与潜在 bug。
- **任务**
  - 吸收 `opengl-cleanup` 系列提交中对 CommandList / Pipeline 状态的改动。
  - 检查与 Vulkan 共享路径（例如资源创建、同步）是否需要统一行为。
- **验证**：OpenGL Demo 正常运行；关注多平台（Windows/Linux/macOS）相关差异。

### 阶段 4：底层绑定切换到 Silk.NET
- **目标**：逐步替换 Vulkan / OpenGL 入口点为 Silk.NET 提供的 API。
- **任务**
  - 引入 Silk.NET 依赖，建立新的封装层 `_gd.Api` 或等效对象。
  - 确认资源类（`VkBuffer`,`VkTexture` 等）调用链兼容 Silk.NET 句柄与结构体。
  - 移除 `NativeLibraryLoader`、旧版 `Vk` 包，重构平台层的加载逻辑。
- **验证**：贯穿 Vulkan / OpenGL 路径的回归测试；确保新依赖能在 CI / 打包环境中正常恢复。

### 阶段 5：演示与测试体系
- **目标**：保留必要的示例 / 自动化测试，替代 ppy 删除的 NeoDemo / Tests。
- **任务**
  - 维护一个最小 Vulkan + OpenGL 示例，用于每次提交的冒烟验证。
  - 视情况引入轻量单元测试或集成测试，覆盖资源创建、Swapchain 重建、同步等关键场景。
- **验证**：在本地和 CI 上可重复执行，输出清晰的成功/失败标志。

## 任务跟踪（可持续更新）
- [x] 统一行尾和编码（提交 `Normalize line endings`）。
- [x] 调整 csproj / props 至 .NET 8 & C# 12。
- [ ] 拓展 Vulkan Swapchain 撕裂支持。
- [ ] 引入 Silk.NET 并完成 Vulkan 封装迁移。
- [ ] 建立最小化的 Vulkan / OpenGL 冒烟 Demo。

> 注：如需新增阶段或调整优先级，请在文档中注明时间与负责人，保持路线图与实际工作同步。
