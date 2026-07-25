# 公开 MCP 工具说明

服务入口：

```text
https://aetherland-chronicle.onrender.com/mcp
```

## aetherland_help

读取公开玩法、开局身份和行动参数。

不需要参数。

## aetherland_new_game

创建新的独立存档。

参数：

- `name`：角色名，最多 20 字。
- `playstyle_id`：开局身份编号。

返回：

- `session_id`：继续游戏所需的存档凭证。
- `state`：公开初始状态。

## aetherland_get_state

读取状态，不推进时间。

参数：

- `session_id`：创建游戏时获得的存档凭证。
- `compact`：可选。设为 `true` 时减少返回内容。

状态中会列出当前可用的地图、地点、人物和传送楼层，不需要猜测内部 ID。

## aetherland_act

执行一个行动并自动保存。

通用参数：

- `session_id`：必填。
- `action`：必填。
- `target`：部分行动需要。
- `choice`：处理事件时需要，从 1 开始。
- `floor`：层间传送时需要。

公开行动：

| action | 用途 | 额外参数 |
|---|---|---|
| `explore` | 探索当前区域 | 无 |
| `choose_event` | 处理等待选择的事件 | `choice` |
| `move` | 前往相邻地图 | `target` |
| `visit` | 访问地图内地点 | `target` |
| `talk` | 与附近人物交谈 | `target` |
| `rest` | 安全区休息或野外露营 | 无 |
| `challenge_boss` | 挑战当前楼层守关者 | 无 |
| `teleport` | 在安全据点进行层间传送 | `floor` |
| `use_item` | 使用消耗品 | `target` |
| `equip` | 装备物品 | `target` |
| `unequip` | 卸下指定装备位置 | `target` |
| `craft` | 制作物品 | `target` |

## 事件选择

探索可能返回 `pending_event`，其中包含公开事件文本和选项编号。

存在待选择事件时，其他行动会暂停。请使用：

```text
action = choose_event
choice = 对应编号
```

隐藏事件概率、完整事件池、掉落表、成就条件和未来转职条件不会通过 MCP 返回。

