# 万流集组件仓库（Streamward Components）

万流集（Streamward）画布的**模型组件包**仓库。软件里「组件库 → 组件仓库…」默认读这里的
`registry.json`；装完就能在画布双击选择器的「模型组件」里直接用。

## 目录

```
registry.json        # 清单（谁有什么、多大、sha256 多少）
packs/*.swc.json     # 组件包本体
```

## 装一个组件（软件里）

组件库窗口 →「组件仓库…」→ 找到组件 →「安装」（立刻进库）或「下载到本地」（存成文件，
放到组件库的投递目录，刷新/重启后自动加载）。

## 发布一个组件

1. 软件里：组件库窗口 → 选中组件 →「导出组件包…」，存成 `xxx.swc.json`
2. 放进 `packs/`
3. 生成清单并提交：

   ```bash
   cd 万流集-源码/electron-app
   npm run build:registry -- --dir /path/to/Streamward-components
   ```

`build-registry` 会**现算** sha256 与体积 —— 安装端就靠这个值判断文件有没有被换过。
不合格的包（格式/版本/工作流缺 nodes）会跳过并打印原因，不会进清单。

## 脱敏

导出时自动删除 `Rh-Comfy-Auth` / `Rh-Identify` 之类的账号签名，作者 userId 换成等长的 0；
**安装时还会再洗一遍**，所以别人手做的包也不会把签名带进你的库。

## 清单格式

```json
{
  "format": "streamward-registry",
  "formatVersion": 1,
  "name": "万流集组件仓库",
  "packs": [
    { "id": "wf-xxxx", "name": "分镜台", "version": "1.2.0", "category": "短剧分镜台",
      "description": "一句话说明", "file": "packs/分镜台.swc.json",
      "sha256": "64 位十六进制", "bytes": 83224, "nodes": 29, "params": 4 }
  ]
}
```
