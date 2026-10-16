# AGENTS.md：给程序和 AI 助手的读取说明

本仓库是英雄联盟国服「海克斯大乱斗」（ARAM: Mayhem）的统计数据，只读即可，不需要安装任何程序。
协议 CC BY-NC 4.0：使用时注明来源 `github.com/czgreat/aramhex-data`，不能用于商业用途。

## 最快的读法

1. 读 `latest.json`：`path` 是最新常规数据的目录（现在是 `patches/16.20/normal`），`files` 是这份数据的文件清单和 SHA-256，`seq` 是更新序号（每次数据更新加 1，并打 tag `data-v<seq>`，可以用 `@data-v<seq>` 取固定的一份）。
2. 读 `<path>/heroes.json` 和 `<path>/hero_augments.json`。
3. 要历史版本或活动规则的数据，读 `index.json` 的 `datasets`，按 `patch` 和 `mode` 挑。

原始地址（仓库公开后可直接下载）：
`https://raw.githubusercontent.com/czgreat/aramhex-data/main/latest.json`

## 文件格式

- 所有 JSON 都是 UTF-8，键是字符串。英雄 ID、海克斯 ID 和腾讯 / Riot 官方 ID 一致。
- `heroes.json`：`{英雄ID: {name, title, games, wins, win_rate, pick_rate, rank}}`。
- `hero_augments.json`：`{英雄ID: {海克斯ID: {games, wins, win_rate, low_sample}}}`。
- `augments.json`：`{海克斯ID: {name, rarity}}`，rarity 为 silver / gold / prismatic。
- `bands/<casual|mid|high|top>/`：同样两张表，只统计对应分数段（娱乐 / 中分 / 高分 / 顶尖）玩家的出场。
- `meta.json`：`matches.collected` 采到的对局，`matches.valid` 有效对局（参与统计），`window` 样本时间，`snapshot_at` 数据截至，`mode` / `mode_label` / `mode_rule` 规则，`limitations` 局限，`files` 各文件 SHA-256。
- `index.json` 的 `datasets[]`：每份数据一条，含 `patch`、`mode`、`path`、`snapshot_at`、`matches_detail`、`files`。

## 使用时要注意

- `mode` 为 `normal` 的是常规规则；其他（如 `first-prismatic` 首次必棱彩）是活动规则，胜率和常规不可直接比较。
- 胜率是「对局结束时持有这个海克斯的玩家」的胜率，是相关关系，不是因果。
- 样本少（`low_sample` 为 true，或 `games` 很小）时胜率不可靠；建议做平滑，例如 `(wins + 基准胜率 × k) / (games + k)`，k 取 300 左右。
- 分数段不是官方段位，是按同局胜负关系推算的分数划分的。
- 只统计采集到的对局，不是全服数据；各大区合计，没有按区加权（覆盖哪些区见 meta.json 的 regions）。
- 每次下载后可以用 `files` 里的 SHA-256 校验文件是否完整。
