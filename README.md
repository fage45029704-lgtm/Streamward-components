# 万流集组件仓库（Streamward Components）

万流集（Streamward）画布的**模型组件包**仓库。软件里「组件库 → 组件仓库…」默认读这里的
`registry.json`；装完就能在画布双击选择器的「模型组件」里直接用。

这个仓库**独立于画布源码仓库**（`Streamward`）：发组件只在这里提交、推送，
不会带进源码任何一个提交里。

## 目录

```
registry.json        # 清单（谁有什么、多大、sha256 多少）
packs/*.swc.json     # 组件包本体（文件名带版本：名字@1.1.0.swc.json）
```

## 装一个组件（软件里）

组件库窗口 →「组件仓库…」→ 找到组件 → 点「安装」。

- 装完之后画布左侧「模型组件」里就有这个组件了，重启/刷新都在（它已经是本机组件库的一份）
- 仓库里有新版时，顶栏会提示「N 个可更新」，列表项挂 `↑`，点「更新」**就地覆盖**——
  组件 id / 名字 / 画布上的引用都不变
- 手动下载也行：把 `.swc.json` 丢进组件库的 `投递/` 目录，软件启动或点「刷新」会自动收

## 发布一个组件（一条命令）

```bash
cd 万流集-源码/electron-app

# 1. 先看看会发什么（导出到临时目录，不碰仓库、不提交）
npm run publish:component -- --dir ../Streamward-components --name "video_minimax_h3_r2v 分镜台" --version 1.1.0 --dry-run

# 2. 确认无误，真发
npm run publish:component -- --dir ../Streamward-components --name "video_minimax_h3_r2v 分镜台" --version 1.1.0

# 一次发多个
npm run publish:component -- --dir ../Streamward-components --name "组件A" --name "组件B" --version 1.0.0

# 全发（慎用）
npm run publish:component -- --dir ../Streamward-components --all --version 1.0.0
```

它做的事：从本机组件库取出组件 → **脱敏 + 白名单重建**（和软件里「导出组件包」同一套）
→ 写进 `packs/名字@版本.swc.json` → 重建 `registry.json`（sha256 现算）→ `git commit` → `git push`。

参数：

| 参数 | 作用 |
|---|---|
| `--dir <path>` | 组件仓库本地路径（必填，或设 `SW_COMP_REPO`） |
| `--name <str>` | 组件名，**精确同名**优先；没有精确同名时按包含匹配，命中多个会拒绝并要求 `--yes` |
| `--all` | 发全部 |
| `--version <x.y.z>` | 写进包文件名，默认 `1.0.0` |
| `--message <str>` | commit 文案 |
| `--no-push` | 只提交不推送 |
| `--dry-run` | 只导出到临时目录，不改仓库、不提交 |
| `--from <path>` | 组件库目录（默认 `~/Library/Application Support/streamward/components`） |

## 只重建清单（手放了包之后）

```bash
npm run build:registry -- --dir ../Streamward-components
npm run build:registry -- --dir ../Streamward-components --check   # 只校验，不写
```

规则：

- 清单里的 `sha256` / `bytes` 一律**现算** —— 安装端就靠这个值判断文件有没有被换过
- 不合格的包（格式/版本/工作流缺 nodes）**不进清单**，并打印原因
- 同名组件有多个版本文件时，清单只收**最高版本**（界面上不该出现两行同名）；
  旧版本文件留在仓库里可以回滚，`git log` 也查得到

## 脱敏

导出时自动删除 `Rh-Comfy-Auth` / `Rh-Identify` 之类的账号签名，作者 userId 换成等长的 0，
并只保留白名单字段；**安装时还会再洗一遍**，所以别人手做的包也不会把签名带进你的库。

## 清单格式

```json
{
  "format": "streamward-registry",
  "formatVersion": 1,
  "name": "万流集组件仓库",
  "updatedAt": "2026-11-07T09:57:20.615Z",
  "count": 1,
  "packs": [
    { "id": "wf-xxxx", "name": "分镜台", "version": "1.2.0", "category": "短剧分镜台",
      "description": "一句话说明（只取第一句）", "file": "packs/分镜台@1.2.0.swc.json",
      "sha256": "64 位十六进制", "bytes": 83224, "nodes": 29, "params": 4 }
  ]
}
```
