# l4d2-plugins-tool

Left 4 Dead 2 SourceMod 插件安装 / 更新工具。

从 GitHub 仓库与 AlliedModders 论坛同步插件，支持源码编译、Require 检测与自动补齐，并可安装或更新 SourceMod / MetaMod。

> English: A SourceMod plugin sync tool for L4D2. Pull plugins from GitHub and AlliedModders, compile sources when needed, resolve Require dependencies, and install/update SourceMod / MetaMod.

## Features

- 交互菜单（推荐）与命令行两种用法
- 多仓库同步：安装、更新已装插件；默认不覆盖 `configs` / `data`
- 同名插件跨仓库对比：内容一致用优先源；已安装且不一致时再询问（对照本地源码默认选项）
- SourceMod / MetaMod 只认 AlliedModders 官方包；`left4dhooks` 只认 SilvDev；`L4D1_2-Plugins` 已有插件优先该仓
- 基础环境：SourceMod、MetaMod、left4dhooks、extensions 与常用 include 等
- Require 检测：缺失依赖可自动下载 / 编译
- 实用修复、8 人战役清单一键安装或删除（保留配置）
- 仓库缓存默认 1 天更新一次；菜单可手动强制刷新，降低 GitHub 限流风险

## Requirements

| 项目 | 说明 |
| --- | --- |
| 系统 | Windows（菜单与 bat 按 Windows 编写） |
| 运行时 | [Python 3.10+](https://www.python.org/) |
| 游戏目录 | `left4dead2`（含或可自动创建 `addons`） |
| 网络 | 访问 GitHub；部分依赖需访问 AlliedModders（可用本机代理） |
| 可选 | SourceMod 自带的 `spcomp.exe`（仅编译 `.sp` 时需要） |

## Quick start

1. 克隆本仓库，或下载源码到任意目录：

   ```bat
   git clone https://github.com/CHN-WolF/l4d2-plugins-tool.git
   ```

2. 编辑同目录 `config.txt`，把 `game_dir` 改成你的 `left4dead2` 路径（写成上一级 `Left 4 Dead 2` 也可以，程序会自动进入 `left4dead2`）。
3. 双击 `l4d2-plugins-tool.bat`，或执行：

   ```bat
   python sync_plugins.py --menu
   ```

4. 按数字选择功能；次级列表可用方向键 / 空格 / 回车操作，**Esc 返回**。输入 `0` 退出主菜单。

首次建议：菜单 **5 / 6** 安装 SourceMod、MetaMod → **7** 安装/更新其余基础环境 → **4** 或 **8** 安装插件。

## Configuration

仓库自带 `config.txt`，一般只需改 `game_dir`：

```ini
game_dir = D:\Steam\steamapps\common\Left 4 Dead 2\left4dead2

repo = fbef0102|https://github.com/fbef0102/L4D1_2-Plugins.git|master
repo = Sourcemod|https://github.com/fbef0102/Sourcemod-Plugins.git|main
```

| 项 | 格式 | 说明 |
| --- | --- | --- |
| `game_dir` | 路径 | 游戏 `left4dead2` 目录 |
| `repo` | `简称\|git地址\|分支` | 一行一个仓库；行首 `#` 或末尾 `\|off` 表示未启用 |

未启用的仓库仍可在菜单「选择启用仓库」中勾选后单独使用；「全部」只包含已启用仓库。也可直接用菜单 **16** 打开配置文件。

## Interactive menu

| 编号 | 功能 |
| ---: | --- |
| 1 | 列出有更新的插件（不询问仓库冲突） |
| 2 | 列出仓库中的全部插件 |
| 3 | 更新已安装插件（默认不覆盖配置；冲突时对照本地源码默认选项） |
| 4 | 从选定仓库勾选安装插件 |
| 5 | 更新 SourceMod（官方包；可选覆盖 configs / plugins，先备份） |
| 6 | 更新 MetaMod（官方包） |
| 7 | 安装/更新基础环境（不含 SourceMod / MetaMod；也不含可选 SM 插件） |
| 8 | 从 Sourcemod-Plugins 勾选安装 |
| 9 | 检测 Require 并自动下载 |
| 10 / 11 | 实用修复：安装 / 删除（保留配置） |
| 12 / 13 | 8 人战役：安装 / 删除（保留配置） |
| 14 | 手动更新仓库缓存（忽略 1 天 TTL） |
| 15 | 选择启用仓库并写入 `config.txt` |
| 16 | 打开 `config.txt` |
| 0 | 退出 |

没有 `addons` 时，使用 5–7 会自动创建目录。

## Command line

```bat
python sync_plugins.py --menu
python sync_plugins.py sync
python sync_plugins.py list --show-all
python sync_plugins.py search l4d_drop
python sync_plugins.py install <plugin_name>
python sync_plugins.py check --missing-only
python sync_plugins.py env
python sync_plugins.py env --missing-only
python sync_plugins.py env --present-only
python sync_plugins.py env --only metamod
python sync_plugins.py env --only sourcemod
python sync_plugins.py env --only sourcemod --package sourcemod-1.11.0-git6968-windows.zip
python sync_plugins.py env --only sourcemod --overwrite-configs --overwrite-plugins
python sync_plugins.py env --with-core
python sync_plugins.py fixes
python sync_plugins.py --dry-run fixes
python sync_plugins.py fixes --remove
python sync_plugins.py 8coop
python sync_plugins.py --dry-run 8coop
python sync_plugins.py 8coop --remove
python sync_plugins.py refresh-cache
```

常用全局参数：

| 参数 | 说明 |
| --- | --- |
| `--dry-run` | 只预览，不写入 |
| `--update-configs` | 覆盖已存在的 `configs` / `data` |
| `--no-fetch-deps` | 发现缺失 Require 时不自动下载 |
| `--repo <key\|all>` | 指定仓库简称，或 `all` |

完整参数见：

```bat
python sync_plugins.py -h
```

## Behavior notes

- **备份**：覆盖前备份到本工具目录下的 `backups\`。
- **生效**：服务器运行中需换图或重启后新插件才生效。
- **编译**：仅对本次实际新增/更新的 `.sp` 编译；已有配套 `.smx` 时不重编译；`*_test.sp` 跳过编译。
- **Include / Gamedata**：编译缺 include 时会从对应仓库补齐；运行所需 gamedata 尽量从同仓库或论坛附件安装。
- **文件名**：论坛 / 仓库附件保留原名，不会改成插件标题。
- **失败日志**：追加写入同目录 `log.txt`。
- **Cloudflare**：从 AlliedModders 下载时若弹出验证，在论坛首页完成一次「验证您是真人」即可；之后本批安装在后台下载。
- **缓存**：仓库缓存在 `.cache\`；默认 1 天内不重复拉取，可用菜单 **14** 或 `refresh-cache` 强制更新。

## Project layout

```text
l4d2-plugins-tool/
├── sync_plugins.py          # 主程序
├── l4d2-plugins-tool.bat    # 双击进入菜单（也可传命令行参数）
├── config.txt               # 配置（改 game_dir 即可）
├── README.md
└── .gitignore
```

## Disclaimer

本工具仅用于管理你自己服务器上的 SourceMod 插件。请遵守各插件作者的许可证与发布条款；第三方插件版权归原作者所有。
