# AstrBot 插件开发指南 🌠

## 环境准备

更新 `metadata.yaml` 文件，填写插件的元数据信息。

> [!WARNING]
> 请务必修改此文件，AstrBot 识别插件元数据依赖于 `metadata.yaml` 文件。

### 插件展示名（可选）

可以修改(或添加) `metadata.yaml` 文件中的 `display_name` 字段，作为插件在插件市场等场景中的展示名，以方便用户阅读。

### 声明支持平台（Optional）

你可以在 `metadata.yaml` 中新增 `support_platforms` 字段（`list[str]`），声明插件支持的平台适配器。WebUI 插件页会展示该字段。

```yaml
support_platforms:
  - telegram
  - discord
```

`support_platforms` 中的值需要使用 `ADAPTER_NAME_2_TYPE` 的 key，目前支持：

- `aiocqhttp`
- `qq_official`
- `telegram`
- `wecom`
- `lark`
- `dingtalk`
- `discord`
- `slack`
- `kook`
- `vocechat`
- `weixin_official_account`
- `satori`
- `misskey`
- `line`

### 声明 AstrBot 版本范围（Optional）

你可以在 `metadata.yaml` 中新增 `astrbot_version` 字段，声明插件要求的 AstrBot 版本范围。格式与 `pyproject.toml` 依赖版本约束一致（PEP 440），且不要加 `v` 前缀。

```yaml
astrbot_version: ">=4.16,<5"
```

可选示例：

- `>=4.17.0`
- `>=4.16,<5`
- `~=4.17`

如果你只想声明最低版本，可以直接写：

- `>=4.17.0`

当当前 AstrBot 版本不满足该范围时，插件会被阻止加载并提示版本不兼容。
在 WebUI 安装插件时，你可以选择“无视警告，继续安装”来跳过这个检查。

### 调试插件

AstrBot 采用在运行时注入插件的机制。因此，在调试插件时，需要启动 AstrBot 本体。

您可以使用 AstrBot 的热重载功能简化开发流程。

插件的代码修改后，可以在 AstrBot WebUI 的插件管理处找到自己的插件，点击右上角 `...` 按钮，选择 `重载插件`。

如果插件因为代码错误等原因加载失败，你也可以在管理面板的错误提示中点击 **“尝试一键重载修复”** 来重新加载。

### 插件依赖管理

目前 AstrBot 对插件的依赖管理使用 `pip` 自带的 `requirements.txt` 文件。如果你的插件需要依赖第三方库，请务必在插件目录下创建 `requirements.txt` 文件并写入所使用的依赖库，以防止用户在安装你的插件时出现依赖未找到(Module Not Found)的问题。

> `requirements.txt` 的完整格式可以参考 [pip 官方文档](https://pip.pypa.io/en/stable/reference/requirements-file-format/)。

## 开发原则

开发插件请遵守以下原则，这也是良好的编程习惯。

- 功能需经过测试。
- 需包含良好的注释。
- 持久化数据请存储于 `data` 目录下，而非插件自身目录，防止更新/重装插件时数据被覆盖。
- 良好的错误处理机制，不要让插件因一个错误而崩溃。
- 在进行提交前，请使用ruff工具格式化您的代码。
- 不要使用 `requests` 库来进行网络请求，可以使用 `aiohttp`, `httpx` 等异步网络请求库。
