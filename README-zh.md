[English](README.md) | [中文说明](README-zh.md)
# Binary Ninja MCP <img src="images/binja.png" height="24" style="margin-left: 5px; vertical-align: middle;">

本仓库包含一个 Binary Ninja 插件、MCP 服务器和桥接组件，可实现 Binary Ninja 与您喜欢的 LLM 客户端的无缝集成。

多功能，作为插件开启mcp server9009端口，桥接作为中间连接ai client和server 9009端口
## 功能特性

- Binary Ninja 与 MCP 客户端的实时无缝集成
- 借助 AI 助手提升逆向工程工作流
- 主要支持 Claude Desktop 作为 MCP 客户端，也可扩展集成其他客户端

## 示例

### 生成二进制分析报告

![Binary Analysis Report Generation](images/mcp-demo-report.png)

### 重命名函数

![Rename Function Demo](images/mcp-demo-rename.gif)

## 组件说明

本仓库包含两个主要组件：

1. Binary Ninja 插件，提供 MCP 服务器，通过 HTTP 接口暴露 Binary Ninja 能力。可与任何实现 MCP 协议的客户端配合使用。
2. 独立的 MCP 桥接组件，将您喜欢的 MCP 客户端与 Binary Ninja MCP 服务器连接。虽然主要集成路径为 Claude Desktop，但 MCP 服务器也可用于其他客户端。

## 支持的集成功能

| 功能 | 说明 |
|------|------|
| `get_binary_status` | 获取已加载二进制文件的当前状态。|
| `list_classes` | 列出程序中的所有命名空间/类名。|
| `list_data_items` | 列出已定义的数据标签及其值。|
| `list_exports` | 列出导出函数/符号。|
| `list_imports` | 列出程序中的导入符号。|
| `list_methods` | 列出程序中的所有函数名。|
| `list_namespaces` | 列出所有非全局命名空间。|
| `list_segments` | 列出所有内存段。|
| `rename_data` | 重命名指定地址的数据标签。|
| `rename_function` | 将函数从当前名称重命名为用户自定义名称。|
| `search_functions_by_name` | 按名称子串搜索函数。|
| `decompile_function` | 反编译指定函数并返回 C 代码。|
| `set_comment` | 在指定地址设置注释。|
| `set_function_comment` | 为函数设置注释。|
| `get_comment` | 获取指定地址的注释。|
| `get_function_comment` | 获取函数的注释。|
| `delete_comment` | 删除指定地址的注释。|
| `delete_function_comment` | 删除函数的注释。|
| `get_assembly_function` | 按名称或地址获取函数的汇编表示。|
| `function_at` | 获取指定地址所属的函数名。|
| `code_references` | 获取调用指定函数的函数名和地址。|
| `get_user_defined_type` | 获取用户自定义类型（结构体、枚举、typedef、联合体）的定义。|
| `rename_variable` | 重命名指定函数内的变量。|
| `retype_variable` | 重新定义指定函数内变量的类型。|
| `define_types` | 从 C 类型定义字符串添加类型定义。|
| `edit_function_signature` | 编辑指定函数的签名（类型字符串）。|

## 前置条件

- [Binary Ninja](https://binary.ninja/)
- Python 3.12 及以上
- [Claude Desktop](https://claude.ai/download)（或您喜欢的集成客户端）

## 安装方法

### Binary Ninja 插件

1.您可以通过 Binary Ninja 的插件管理器（`Plugins > Manage Plugins`）安装插件。

![Plugin Manager Listing](images/plugin-manager-listing.png)

2.如需手动配置插件，可将本仓库复制到 Binary Ninja 的插件文件夹中。




### Claude Desktop 桥接（可选）

仅当您希望使用 Claude Desktop 作为 MCP 客户端时需要此步骤。请先配置好虚拟环境：

```bash
git clone git@github.com:fosdickio/binary_ninja_mcp.git
cd binary_ninja_mcp

python3 -m venv .venv
source .venv/bin/activate   # macOS/Linux

pip install -r bridge/requirements.txt
安装所需包到虚拟环境
虚拟python路径=pwd+.venv/bin/python3
```

#### 自动化配置（Mac）

在 Mac 上，可通过以下命令自动完成设置：

```bash
./scripts/setup_claude_desktop.py
```

#### 手动配置

在其他操作系统或需手动配置 Claude Desktop 集成时：

1. 进入 `Settings > Developer > Edit Config`
2. 添加如下配置：
桥接脚本路径
（1）通过插件安装方式
"桥接脚本[插件安装也会存在/home/username/.binaryninja/repositories/community/plugins/fosdickio_binary_ninja_mcp/bridge/]"
(2)直接采用clone路径的桥接脚本路径
```json
{
  "mcpServers": {
    "binary_ninja_mcp": {
      "command": "虚拟环境的python路径",
      "args": ["桥接脚本路径+binja_mcp_bridge.py"
        
      ]
    }
  }
}
```

注意：请将 `/ABSOLUTE/PATH/TO` 替换为您的实际项目绝对路径。必须使用虚拟环境中的 Python 解释器以访问已安装依赖。

## 使用方法

### Claude Desktop

1. 打开 Binary Ninja 并安装 `Binary Ninja MCP` 插件
2. 重启 Binary Ninja 并打开一个二进制文件
3. 启动 MCP 服务器（`Plugins > MCP Server > Start MCP Server`）
4. 启动 Claude Desktop

打开 Claude Desktop 后，集成会自动生效。

![Claude Integration](images/claude-desktop-integration.png)

现在您可以直接在 Claude 中对当前打开的二进制文件进行提问。例如：

- "为当前二进制生成分析报告。"
- "将函数 X 重命名为 Y。"
- "列出当前二进制中的所有函数。"
- "当前加载的二进制状态如何？"

### 其他 MCP 客户端集成

通过实现相应的集成层，桥接组件可用于其他 MCP 客户端。

## 开发说明

项目结构如下：

```
binary_ninja_mcp/
├── bridge/                      # MCP 客户端集成
├── plugin/                      # Binary Ninja 插件
├── scripts/
│   └── setup_claude_desktop.py  # Claude Desktop 设置脚本
```
## 贡献

欢迎贡献代码，欢迎提交 Pull Request。 