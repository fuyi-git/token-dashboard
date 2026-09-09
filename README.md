# workbuddy-token-dashboard

把 WorkBuddy 的真实 token 消耗变成一张**单文件离线看板**。作为一个 WorkBuddy Skill 分发：别人加载后说一句"看看我的 token 用量"，Agent 就会扫描本机会话日志，生成同款看板并打开。

## 功能

- **KPI**：合计 Token / 日均 / 会话 / 轮次 / 输入输出比 / 缓存命中率 / 预估金额（元）
- **日活热力图**：20 分钟粒度，色阶对数插值，悬停看明细
- **0–24 时模型山脊图**：各模型按时段的消耗分布（对数轴），可点击高亮
- **每日模型用量比例**：堆叠柱 + 模型卡片，点击高亮单模型
- **单次请求大小分布**：每模型 50% / 90% 分位与分布形状
- **三级下钻**：工作空间 → 会话 → 单次请求（散点 + 逐条明细表）
- 深浅色主题（记忆偏好）、1 / 3 / 7 / 30 天窗口 + 自定义日期区间
- 数据为**请求级真实 usage**（非估算），输出单文件 HTML，离线可看、无外部依赖

## 安装（作为 Skill）

把本仓库内容放到 WorkBuddy 用户级技能目录：

```
~/.workbuddy/skills/token-dashboard/
├── SKILL.md
└── scripts/
    └── gen_dashboard.py
```

- Windows：`C:\Users\<你>\.workbuddy\skills\token-dashboard\`
- macOS / Linux：`~/.workbuddy/skills/token-dashboard/`

```bash
git clone <本仓库地址> ~/.workbuddy/skills/token-dashboard
```

重启 WorkBuddy 会话即可加载。之后直接说"看看我的 token 用量"即可触发。

## 手动运行（不装 Skill 也行）

Python 3.8+，仅标准库，无需装任何包：

```bash
python scripts/gen_dashboard.py                  # 输出 ./token-dashboard.html
python scripts/gen_dashboard.py --out 看板.html   # 自定义输出路径
```

- Windows（Git Bash / CMD / PowerShell 均可）
- macOS / Linux 同上

## 数据说明

- 数据源：`~/.workbuddy/projects/**/*.jsonl` 中每条记录的 `providerData.rawUsage / usage` 字段（请求级真实 usage）；子代理日志自动归属父会话。
- 预估金额基于 `scripts/gen_dashboard.py` 头部的 `PRICE` 表（元/百万 token，[输入, 输出, 缓存命中]），按每条请求的**实际缓存命中**计价：读命中走缓存价、缓存写入（Claude 系）按 1.25× 输入价、其余输入走输入价——不同用户套餐价格不同，可自行修改该表。
- 所有数据均来自本机日志，不外发、不上传。

## 目录结构

```
workbuddy-token-dashboard/
├── SKILL.md                  # Skill 定义（Agent 加载后按此执行）
├── README.md
└── scripts/
    └── gen_dashboard.py      # 看板生成脚本（跨平台，仅标准库）
```
