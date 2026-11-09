# 随变壁纸导入 · AstroBox 插件发布仓库

AstroBox 官方插件源（`AstralSightStudios/AstroBox-NG-Plugin-Repo`）的作者发布仓库。
插件本体负责把 **336×480 的 PNG 壁纸**原样传到小米手环上的「随变壁纸」快应用，
由「随变」表盘读取并显示。

## 使用前提

1. 手机端安装 AstroBox，并从插件市场安装「随变壁纸导入」；
2. 手环端安装「随变壁纸」快应用（包名 `com.facephotos.quickapp`）与「随变」表盘；
3. 壁纸必须是 **336×480 PNG**。插件在选图时只读 IHDR 校验尺寸，不符会拒收并提示；
   可用裁剪工具 <https://facephoto.cc.cd/> 处理，插件界面底部有「复制链接」按钮。

插件只做导入与传输，不裁剪、不缩放图片；同时从图片里算 5 个主色（按壁纸明暗
做对比度重映射）随壁纸一起发给手环，供表盘「壁纸提取」色卡使用。

## 本仓库结构

```
index.txt              # 官方聚合脚本读它；内容一行：dist
dist/
├── manifest.json      # 插件清单（版本、入口、图标、权限、API Level）
├── plugin.wasm        # 插件本体（componentize-py 编译的 WASI 组件）
└── icon.png           # 256×256 插件图标
```

官方源 `index.txt` 中对应的 raw 基地址：

```
https://raw.githubusercontent.com/yamu81663-dot/suibian-wallpaper-plugin/refs/heads/main/
```

## 清单信息

| 项 | 值 |
|---|---|
| 名称 / 作者 | 随变壁纸导入 / 迟雪 |
| API Level / WASI Version | 4 / 3 |
| 入口 | `plugin.wasm` |
| 权限 | `device`、`dialog`、`interconnect`、`thirdpartyapp`、`register_interconnect_recv`、`clipboard.write` |

权限说明：`device` 发现已连接手表，`thirdpartyapp` 确认快应用已安装，
`interconnect` 负责下行分片传输，`register_interconnect_recv` 订阅回执，
`dialog` 弹出选图框，`clipboard.write` 用于「复制链接」按钮写入系统剪贴板。

## 更新版本

改根目录 `manifest.json` 的 `version`，重新构建并把 `dist/` 三个文件推上来即可；
官方源的 Action 每 4 小时重新抓取 `manifest.json` 生成 `index.json`。

## 已知限制

- 只接受 8bit 非隔行 PNG，且尺寸严格为 336×480。
- 手表 → 手机的回执通道受签名校验限制，插件不依赖回执，发送完成提示为
  「传输成功，等待手环端接收完成」；在手表端快应用看到新壁纸即为成功。
- 插件不负责接收或删除壁纸，这些由手环端快应用完成。
