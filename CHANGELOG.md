# 更新日志

## \[1.2.3] - 2026-10-18

### 变更

* 版本号统一为 1.2.3：此前 `manifest.json` / README 写的是 1.2.2，而源码与 DLL 内嵌仍是 1.2.0
* README：中途加入推荐改指 LateJoinSimple，并注明 LateJoinNow 与 LateJoinSimple **不可同装**
* README：已知冲突补充 **LateJoinPlus / RepoLateJoin**（它们自带人数上限功能，会与本模组互相覆盖）
* README：修正配置文件名（`dyxc666.MorePlayersSimple.cfg`）与一处错字
* README / manifest：补充 **GitHub 仓库链接**（manifest 的 `website_url` 也指向仓库）

### 说明

* 本版**无功能改动**，仅为版本号与文档修正


## \[1.2.2] - 2026-08-06

### 修复

* 修复语音子房间（`\_voice\_`）的 `MaxPlayers` 未被同步的问题，现在语音房间上限会随公开房间上限（20）一起设置，日志中标注 `\[VOICE]` 以便确认
* 注意：此修改仅将客户端参数设大，如果Photon Voice 为免费套餐，服务端可能仍限制约 5-6 人同时说话，此为官方限制，非模组 bug

## \[1.2.1] - 2026-06-17

### 文本描述更新

\-修改了一些描述错误的README和manifest.json。

## \[1.2.0] - 2026-06-17

### 回退

* 回退至极简版本，仅保留房间人数上限修改功能
* 移除所有可能导致bug的实验性功能（TTL修改、回调接口、Disconnect补丁等）

### 变更

* 版本号跳至 1.2.0，表明这是一次重大简化
* 从此版本起，功能性更新和重大bug修复将以单独补丁形式发布，主模组保持稳定

## \[1.1.6] - 2026-06-17

### 修复

* 修复玩家退出后关卡切换无限卡加载的问题（百分之百复现）。根本原因：有人退出后，房间 `MaxPlayers` 仍为开房时设置的高上限值，但实际在线人数已减少。Photon 底层在加载新场景时一直等待不存在的玩家，导致全员卡死。
* 修复方案：新增 `DynamicMaxPlayers` 配置项，玩家退出时自动将房间上限调整为当前实际在线人数。

### 新增

* 自动检测 LateJoinNow：如果同时安装了 LateJoinNow，自动关闭 `DynamicMaxPlayers`，避免两个模组对房间上限的修改互相冲突。用户无需手动配置。

### 变更

* 私人房间最大玩家数范围调整为 1-6，默认值恢复为 6

## \[1.1.5] - 2026-06-07

### 变更

* 修正 `OpCreateRoom` 和 `OpJoinOrCreateRoom` 的 `PlayerTtl` / `EmptyRoomTtl` 从 0 改为 -1（永不过期），支持 LateJoinNow 中途加入

## \[1.1.4] - 2026-05-25

### 适配

* 适配游戏 v0.4.3

## \[1.1.3] - 2026-05-20

### 移除

* 移除 `OnConnectedToMasterPatch`，消除 `maxPlayersPhoton` 字段缺失的警告。经 dnSpy 逆向确认，游戏 `OnConnectedToMaster` 中已不再使用该字段，Patch 已无实际作用。

## \[1.1.2] - 2026-05-15

### 文档

* 修正文本说明。

## \[1.1.1] - 2026-05-13

### 新增

* 公开房间和私人房间的最大玩家数分开配置。

  * `MaxPlayersPublic`：公开房间最大玩家数（1-20，默认 20）。
  * `MaxPlayersPrivate`：私人房间最大玩家数（1-20，默认 6）。
* 创建房间时根据房间类型自动选择对应配置值。
* 日志标明 `PUBLIC` 或 `PRIVATE`，方便调试。

### 修复

* 修复 `NetworkConnect.instance` 编译错误。

## \[1.0.15] - 2026-05-12

### 新增

* 新增 `OnConnectedToMasterPatch`，覆盖快速加入路径的默认 6 人上限。
* `HostLobbyPatch` 增加 `MaxMembers` 字段备选设置逻辑。

## \[1.0.14] - 2026-05-12

### 适配

* 适配游戏 v0.4.3

## \[1.0.13] - 2026-05-10

### 文档

* 更新readme。

## \[1.0.12] - 2026-05-09

### 文档

* 更新封面图片。

## \[1.0.11] - 2026-05-08

### 文档

* 修正文本说明。



## \[1.0.10] - 2026-05-08

### 文档

* 修正文本说明。

## \[1.0.9] - 2026-05-07

### 修复

* 适配游戏 v0.4.0（Cosmetics Update）。
* 修复房间列表 UI 显示的玩家上限：在 `MenuPageServerList.CreateServerElement` 增加补丁，修正自己创建的房间的显示上限。
* 通过 `SelfCreatedRoomName` 精确匹配自己的房间，不影响其他房间的显示。

## \[1.0.8] - 2026-05-05

### 精简

* 移除 `NetworkConnect.OnConnectedToMaster` 补丁，消除与底层 Photon 补丁的潜在冲突。
* 仅保留核心的 Photon 底层补丁和 Steam 大厅补丁，提高稳定性。

### 保留

* `OpCreateRoom` / `OpJoinOrCreateRoom` 补丁：覆盖所有房间创建路径。
* `SteamManager.HostLobby` 补丁：保障 Steam 好友直接加入。

## \[1.0.7] - 2026-05-04

### 修复

* 修复 Steam 好友无法通过好友列表加入超过 6 人房间的问题。
* 新增对 `NetworkConnect.OnConnectedToMaster` 的补丁，覆盖快速加入路径中的硬编码人数上限。
* 新增对 `SteamManager.HostLobby` 的补丁，同步修改 Steam 大厅成员上限。

## \[1.0.6] - 2026-05-03

### 文档

* 调整 README 冲突说明，提醒与修改房间加入逻辑的模组均可能冲突。

## \[1.0.5] - 2026-05-02

### 文档

* 修正文本说明。

## \[1.0.4] - 2026-05-02

### 文档

* README 添加 QQ 群信息。

## \[1.0.3] - 2026-04-24

### 文档

* 修正 manifest.json 中的表述。

## \[1.0.2] - 2026-04-24

### 文档

* 修正 README 中的表述。

## \[1.0.1] - 2026-04-17

### 修复

* 修正先前版本在房间创建后才修改 MaxPlayers 导致无效的问题。
* 改用 Harmony 前缀拦截 Photon 底层 `OpCreateRoom` 和 `OpJoinOrCreateRoom`，在 RoomOptions 阶段正确设置上限。

## \[1.0.0] - 2026-04-16

* 初始版本发布。
* 支持自定义房间最大玩家数，突破游戏默认 6 人上限。

