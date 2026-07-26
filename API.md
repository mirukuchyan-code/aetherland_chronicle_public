# 公开 MCP 工具说明

服务入口：

```text
https://aetherland-chronicle.onrender.com/mcp
```

## aetherland_help

读取公开玩法、开局身份和行动参数，不需要参数。

## aetherland_new_game

创建新的独立存档。

- `name`：角色名，最多 20 字。
- `playstyle_id`：开局身份编号。

返回的 `session_id` 是继续游戏所需的存档凭证，请妥善保存。

## aetherland_get_state

读取状态，不推进时间。

- `session_id`：创建游戏时获得的存档凭证。
- `compact`：可选；设为 `true` 时减少返回内容。

状态中的 `choices` 会列出当前地图、地点、人物、任务、商品、委托和配方，无需猜测内部 ID。

与地区探索有关的公开状态：

- `location.region_inspiration`：当前楼层的地区与生活气质。
- `location.staple_food`：当地主要食材。
- `choices.shops[].stock`：商品用途、类别、价格及 `floor_exclusive` 楼层限定标记。
- `active_buffs`：料理、饮品与补给的剩余生效时间。
- `cookbook`：《天穹塔食谱册》分层进度、已品尝料理与地区称号；未解锁菜名保持隐藏。

## aetherland_act

执行一个行动并自动保存。

通用参数：

- `session_id`：必填。
- `action`：必填。
- `target`：部分行动需要。
- `choice`：事件或谈判选择需要，从 1 开始。
- `floor`：层间传送时需要。
- `quantity`：买卖数量，可选，默认 1。

| action | 用途 | 额外参数或目标来源 |
|---|---|---|
| `explore` | 探索当前区域 | 无 |
| `choose_event` | 处理等待选择的事件 | `choice` |
| `move` | 前往相邻地图 | `target`，见 `choices.adjacent_maps` |
| `visit` | 访问地图内地点 | `target`，见 `choices.locations` |
| `talk` | 与附近人物交谈 | `target`，见 `choices.nearby_npcs` |
| `deliver` | 交付任务或谈判材料 | `target`，见 `choices.deliveries` |
| `negotiate` | 与可谈判人物交涉 | `target`、`choice`，见 `choices.negotiations` |
| `commission` | 委托铁匠打造装备 | `target`，见 `choices.commissions` |
| `buy` | 在当前商店购买物品 | `target`、`quantity`，见 `choices.shops` |
| `sell` | 向当前商店出售物品 | `target`、`quantity` |
| `guild_accept` | 接受公会委托 | `target`，见 `choices.guild_quests` |
| `guild_turn_in` | 交付已完成的公会委托 | `target`，见 `choices.guild_active` |
| `forge` | 使用当前锻造台 | `target`，见 `choices.forge_recipes` |
| `rest` | 安全区休息或野外露营 | 无 |
| `challenge_boss` | 挑战当前楼层守关者 | 无 |
| `teleport` | 在安全据点进行层间传送 | `floor` |
| `use_item` | 使用消耗品 | `target` |
| `equip` | 装备物品 | `target` |
| `unequip` | 卸下指定装备位置 | `target` |
| `craft` | 制作基础物品 | `target` |

## 事件选择

探索可能返回 `pending_event`，其中包含公开事件文本和选项编号。存在待选择事件时，其他行动会暂停：

```text
action = choose_event
choice = 对应编号
```

隐藏事件概率、完整事件池、掉落表、成就条件、Boss 条件和未来转职条件不会通过 MCP 返回。

地方料理在 `use_item` 后登记。首次品尝即使生命和体力已满也可以正常记录；重复食用则需要料理仍能恢复状态或刷新有效增益。
