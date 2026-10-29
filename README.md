> **v1.2.5**：适配 2026-10-29 游戏大更新 ——
> ① 引擎从 Unity 2022.3 升到 **Unity 6（6000.3.17f1）**，本模组已用新版程序集重新编译；
> ② Steam 大厅逻辑从 `SteamManager` 迁移到 `SteamLobbyManager`，本模组的 `HostLobby` 补丁已跟着改。
>
> ⚠️ 同时期其它未适配 Unity 6 的模组（尤其 REPOLib 及依赖它的模组）会加载失败或报错，与本模组无关。

> ⚠️ **兼容性说明**：本版本使用 **Unity 6（6000.3.17f1）** 的游戏程序集编译，
> **仅支持 2026-10-29 之后的游戏版本**。若你的游戏仍是 Unity 2022 版本，请使用更早的模组版本（1.2.3）。
# More Players Simple
# 更多玩家精简版

突破 REPO 公共房间默认 6 人联机上限，支持 1-20 人自定义。

版本：1.2.3

## ⚠️ 关于超过 6 人后语音失效

本模组已将语音子房间的 `MaxPlayers` 设为 20（可在控制台日志中确认 `[VOICE]: MaxPlayers = 20`）。超过 6 人后语音仍失效的原因是 **Photon Voice 免费套餐在服务端硬性限制同时说话人数**，此为官方限制，非模组 bug。建议使用 QQ / Discord 等外部语音软件。

## ⚠️ 重要说明

### 本模组本身不提供中途加入功能。如需中途加入，推荐搭配 [LateJoinSimple](https://thunderstore.io/c/repo/p/cn_xc/LateJoinSimple/)（本作者维护的版本，基于 Zichen 的 LateJoinNow 修复）。

> 注意：**LateJoinNow 与 LateJoinSimple 不能同时安装** —— 两者 GUID 与房间属性键都不同，同装会出现重复 RPC 与缓存清理，导致卡加载 / 地图异常。

## ⚠️ 已知冲突
与以下模组同时使用可能导致人数上限失效，建议二选一：
- **EmpressLateJoin** 及其它修改房间加入逻辑的模组
- **LateJoinPlus / RepoLateJoin**：它们自带人数上限功能，会与本模组互相覆盖（双方都不一定提示），请只保留一边生效
- 说明：本作者的 [LateJoinSimple](https://thunderstore.io/c/repo/p/cn_xc/LateJoinSimple/) 不含人数上限功能，可放心一起用


## ✨ 功能
- 公开房间最大玩家数可自定义（1-20，默认 20）
- 私人房间最大玩家数可自定义（1-6，默认 6）
- 仅修改房间最大玩家数，不影响游戏玩法、平衡或任何其他机制

## 安装
- 确保已安装 [BepInExPack](https://thunderstore.io/c/repo/p/BepInEx/BepInExPack/)
- 将 `MorePlayersSimple.dll` 放入 `BepInEx/plugins` 文件夹
- 启动游戏，模组会自动生成配置文件

## ⚙️ 配置
配置文件位置：`BepInEx/config/dyxc666.MorePlayersSimple.cfg`

| 配置项 | 默认值 | 范围 | 说明 |
|---|---|---|---|
| MaxPlayersPublic | 20 | 1-20 | 公开房间最大玩家数 |
| MaxPlayersPrivate | 6 | 1-6 | 私人房间最大玩家数 |

## ❓ 常见问题

**Q：为什么我设了 20 人，别人还是进不来？**
A：请确认你是房主，并且是在创建新房间时生效。加入别人的房间时，上限由房主决定。

**Q：支持中途修改吗？**
A：不支持。房间创建后上限无法更改，需重新开房。

## 问题交流
QQ群：573485890

源码与问题反馈：[GitHub](https://github.com/stevenlou2006-source/MorePlayersSimple)

## 致谢
Zichen的LateJoinNow
灵感来自 MorePlayersImproved 和 RoboUnion。
---

## 许可证

本项目以 **GNU General Public License v3.0** 发布。

Copyright (C) 2026 cn_xc

完整条款见仓库根目录 [LICENSE](LICENSE)。
