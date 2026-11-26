# today-tool-mcp

一个极简的 [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) 服务端示例，基于官方 Python SDK 的 `FastMCP` 构建。它向 MCP 客户端暴露一个工具：返回今天的日期。适合用来学习 MCP Server 的开发、调试与接入流程。

## 功能

| 工具名 | 说明 | 参数 | 返回值 |
| --- | --- | --- | --- |
| `get_today` | 返回当前年月日 | 无 | 字符串，格式为 `YYYY-MM-DD`，例如 `2026-11-26` |

工具通过 Python 标准库 `datetime.now()` 获取本地时间，并按 `%Y-%m-%d` 格式化输出。

## 项目结构

```
today-tool-mcp/
├── server.py          # MCP 服务主体，定义并注册工具
├── chatmcp.yaml       # 应用元信息与 MCP 客户端接入配置示例
├── requirements.txt   # Python 依赖声明
└── README.md          # 本说明文件
```

- **server.py**：创建 `FastMCP("today-tool-mcp")` 实例，用 `@mcp.tool()` 装饰器注册 `get_today`，最后通过 `mcp.run()` 启动服务（默认使用 stdio 传输）。
- **chatmcp.yaml**：声明了名称、描述、作者、许可证、标签，以及本地启动命令和一段可直接复制的 `mcpServers` 客户端配置。

## 环境要求

- Python 3.8 及以上版本
- pip 包管理工具

## 安装

```bash
# 克隆或进入项目目录
cd today-tool-mcp

# 安装依赖
pip install -r requirements.txt
```

核心依赖为 `mcp>=1.28.1`，它提供了 `mcp.server.fastmcp.FastMCP`。

## 运行

```bash
python server.py
```

服务通过标准输入输出（stdio）与客户端通信，因此直接在终端运行时不会看到交互式输出，需要由 MCP 客户端来调用。

## 在 MCP 客户端中接入

在客户端的 MCP 配置文件中加入以下内容（即 `chatmcp.yaml` 中的 `config` 片段）：

```json
{
  "mcpServers": {
    "today-tool-mcp": {
      "command": "python",
      "args": ["server.py"]
    }
  }
}
```

注意：

- `args` 中的 `server.py` 需指向本文件的实际路径；若客户端工作目录不在项目根目录，请填写绝对路径。
- 如果系统中 `python` 命令指向的不是安装了 `mcp` 依赖的解释器，请将其替换为对应的解释器路径（例如虚拟环境中的 `python`）。
- 配置完成后重启客户端，即可在工具列表中看到 `get_today`。

## 调用示例

客户端连接后，调用工具 `get_today`（无参数），将得到类似结果：

```
2026-11-26
```

## 扩展建议

本项目是一个最小可运行骨架，可在此基础上：

- 增加更多工具，如时间、时区换算、日期计算等；
- 将 `datetime.now()` 替换为显式时区（如 `ZoneInfo("Asia/Shanghai")`）以避免环境时区差异；
- 为工具补充更详细的 docstring，因为 `FastMCP` 会将文档字符串作为工具描述暴露给大模型。

## 许可证

MIT
