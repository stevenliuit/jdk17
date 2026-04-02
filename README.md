# OpenJDK 17 自动编译项目

[![Build OpenJDK 17](https://github.com/stevenliuit/jdk17/actions/workflows/build-jdk17.yml/badge.svg)](https://github.com/stevenliuit/jdk17/actions/workflows/build-jdk17.yml)
[![Auto Sync](https://github.com/stevenliuit/jdk17/actions/workflows/auto-sync.yml/badge.svg)](https://github.com/stevenliuit/jdk17/actions/workflows/auto-sync.yml)
[![GitHub release](https://img.shields.io/github/release/stevenliuit/jdk17.svg)](https://github.com/stevenliuit/jdk17/releases)
[![License](https://img.shields.io/badge/license-GPLv2%20with%20Classpath%20Exception-blue.svg)](https://openjdk.org/legal/gplv2+ce.html)

## 📖 项目简介

这是 OpenJDK 17 的自动构建项目，实现了：

- ✅ **自动同步**：每天自动同步 OpenJDK 官方仓库的所有分支和标签
- ✅ **自动编译**：同步后自动触发构建，支持 Linux、Windows、macOS 多平台
- ✅ **自动发布**：构建完成后自动创建 GitHub Release
- ✅ **手动构建**：支持手动触发构建，可选择分支和构建类型

## 🚀 快速开始

### 下载预编译的 JDK

前往 [Releases](https://github.com/stevenliuit/jdk17/releases) 页面下载最新构建的 JDK：

| 平台 | 架构 | 文件 |
|------|------|------|
| Linux | x64 | `OpenJDK17-x64-linux.tar.gz` |
| Linux | aarch64 | `OpenJDK17-aarch64-linux.tar.gz` |
| Windows | x64 | `OpenJDK17-x64-windows.zip` |
| macOS | x64 | `OpenJDK17-x64-macos.tar.gz` |
| macOS | aarch64 | `OpenJDK17-aarch64-macos.tar.gz` |

### 手动触发构建

1. 进入 [Actions](https://github.com/stevenliuit/jdk17/actions/workflows/build-jdk17.yml) 页面
2. 点击 "Run workflow"
3. 选择参数：
   - `branch`: 要构建的分支（默认：master）
   - `build_type`: 构建类型（release/fastdebug/debug）
4. 点击 "Run workflow" 开始构建

## 📅 自动同步机制

### 定时同步

- **每天 UTC 0:00**（北京时间上午 8:00）：自动同步上游所有分支和标签
- **每周 UTC 0:00**（周日）：全量同步，确保不遗漏任何更新

### 同步内容

- ✅ master 分支
- ✅ 所有开发分支（jdk-17*, jdk17* 等）
- ✅ 所有标签（tags）

### 同步触发构建

每次同步完成后，会自动触发 JDK 17 的编译构建工作流。

## 🔧 构建配置

### 支持的平台

| 平台 | 架构 | 编译器 | Boot JDK |
|------|------|--------|----------|
| Ubuntu 20.04 | x64 | GCC | JDK 16 |
| Ubuntu 20.04 | aarch64 | GCC | JDK 16 |
| Windows 2022 | x64 | MSVC | JDK 16 |
| macOS 12 | x64 | Clang | JDK 16 |
| macOS 14 | aarch64 | Clang | JDK 16 |

### 构建类型

- **release**: 生产版本（默认）
- **fastdebug**: 快速调试版本
- **debug**: 完整调试版本

### 构建参数

```bash
# 配置示例
bash configure \
  --with-boot-jdk="$BOOT_JDK" \
  --enable-ccache \
  --with-jvm-variants=server \
  --with-debug-level=release \
  --with-version-build="$BUILD_NUMBER"
```

## 📦 构建产物

每个成功构建会生成：

1. **JDK 压缩包**：`OpenJDK17-{arch}-{platform}.tar.gz` 或 `.zip`
2. **SHA256 校验和**：`.sha256` 文件
3. **GitHub Release**：自动创建发布页面

### 使用构建产物

**Linux/macOS:**
```bash
# 下载并解压
tar -xzf OpenJDK17-x64-linux.tar.gz

# 设置环境变量
export JAVA_HOME=$PWD/jdk
export PATH=$JAVA_HOME/bin:$PATH

# 验证
java -version
```

**Windows:**
```powershell
# 下载并解压
Expand-Archive -Path OpenJDK17-x64-windows.zip -DestinationPath C:\jdk17

# 设置环境变量
$env:JAVA_HOME = "C:\jdk17\jdk"
$env:Path += ";C:\jdk17\jdk\bin"

# 验证
java -version
```

## 🔄 工作流程

### 构建流程

```
┌─────────────────┐
│  定时触发/手动  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  同步上游仓库   │
│  - 所有分支     │
│  - 所有标签     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  触发构建任务   │
└────────┬────────┘
         │
         ├──────► Linux x64 构建
         ├──────► Linux aarch64 构建
         ├──────► Windows x64 构建
         ├──────► macOS x64 构建
         └──────► macOS aarch64 构建
                  │
                  ▼
         ┌─────────────────┐
         │  上传 artifacts │
         └────────┬────────┘
                  │
                  ▼
         ┌─────────────────┐
         │  创建 Release   │
         └─────────────────┘
```

### 同步流程

```
┌─────────────────┐
│   每天 UTC 0:00 │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Fetch Upstream │
│  - 所有分支     │
│  - 所有标签     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Merge/Push     │
│  - master       │
│  - dev branches │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Trigger Build  │
└─────────────────┘
```

## 🛠️ 高级配置

### 自定义构建

修改 `.github/workflows/build-jdk17.yml` 文件可以：

- 添加新的构建目标
- 修改编译选项
- 调整构建参数
- 添加额外的测试步骤

### 使用 Secrets

在仓库设置中添加以下 Secrets：

- `PERSONAL_TOKEN`: GitHub Personal Access Token（用于推送）
- `NOTIFICATION_WEBHOOK`: 通知 Webhook URL（可选）

### 本地构建

```bash
# 克隆仓库
git clone https://github.com/stevenliuit/jdk17.git
cd jdk17

# 安装依赖
bash configure --with-boot-jdk=$JAVA_HOME

# 编译
make images

# 测试
./build/*/jdk/bin/java -version
```

## 📊 构建状态

| 平台 | 状态 |
|------|------|
| Linux x64 | [![Linux x64](https://github.com/stevenliuit/jdk17/actions/workflows/build-jdk17.yml/badge.svg?branch=master&event=push)](https://github.com/stevenliuit/jdk17/actions) |
| Linux aarch64 | [![Linux aarch64](https://github.com/stevenliuit/jdk17/actions/workflows/build-jdk17.yml/badge.svg?branch=master&event=push)](https://github.com/stevenliuit/jdk17/actions) |
| Windows x64 | [![Windows x64](https://github.com/stevenliuit/jdk17/actions/workflows/build-jdk17.yml/badge.svg?branch=master&event=push)](https://github.com/stevenliuit/jdk17/actions) |
| macOS x64 | [![macOS x64](https://github.com/stevenliuit/jdk17/actions/workflows/build-jdk17.yml/badge.svg?branch=master&event=push)](https://github.com/stevenliuit/jdk17/actions) |
| macOS aarch64 | [![macOS aarch64](https://github.com/stevenliuit/jdk17/actions/workflows/build-jdk17.yml/badge.svg?branch=master&event=push)](https://github.com/stevenliuit/jdk17/actions) |

## 📝 许可证

OpenJDK 17 使用 **GPLv2 with Classpath Exception** 许可证。

- [OpenJDK 许可证说明](https://openjdk.org/legal/gplv2+ce.html)
- [GPLv2 许可证全文](https://www.gnu.org/licenses/old-licenses/gpl-2.0.html)

## 🔗 相关链接

- [OpenJDK 17 官方网站](https://openjdk.org/projects/jdk/17/)
- [OpenJDK 17 GitHub](https://github.com/openjdk/jdk17)
- [OpenJDK 17 文档](https://openjdk.org/groups/build/)
- [JDK 17 JEPs](https://openjdk.org/projects/jdk/17/jeps)

## 🤝 贡献

欢迎贡献！请查看 [Contributing Guidelines](CONTRIBUTING.md)。

### 报告问题

如果遇到问题，请 [创建 Issue](https://github.com/stevenliuit/jdk17/issues/new) 并提供：

- 操作系统和版本
- JDK 版本（`java -version` 输出）
- 问题描述和复现步骤

## ❓ 常见问题

### Q: 为什么构建这么慢？

A: JDK 编译需要编译大量源代码，通常需要 1-2 小时。可以使用 ccache 加速后续构建。

### Q: 如何选择构建类型？

A:
- `release`: 生产环境使用，性能最优
- `fastdebug`: 调试使用，包含部分调试信息
- `debug`: 完整调试信息，体积最大

### Q: 构建失败怎么办？

A:
1. 查看 Actions 日志
2. 检查配置日志 `config.log`
3. 确认 Boot JDK 版本正确
4. 尝试清理后重新构建：`make clean`

### Q: 如何更新到最新版本？

A: 仓库会每天自动同步上游更新，无需手动操作。也可以手动触发同步工作流。

## 📧 联系方式

- **Issues**: [GitHub Issues](https://github.com/stevenliuit/jdk17/issues)
- **Discussions**: [GitHub Discussions](https://github.com/stevenliuit/jdk17/discussions)

---

**Star ⭐ 本项目以获取最新构建通知！**