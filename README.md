# 鸣潮（Wuthering Waves）版本节奏分析

用 SQL 分析《鸣潮》从 1.0 公测到 3.7 共 22 个版本的更新节奏。

## 项目内容

- `wuwa-versions.sql` — 版本数据（22 个版本，字段 `ver` 版本号、`release_date` 上线日期）
- `wuwa-analysis.md` — 分析报告（Markdown）
- `wuwa-analysis.html` — 分析报告（网页版，浏览器打开即可看）

## 核心结论

| 指标 | 数值 | 说明 |
|---|---|---|
| 平均间隔 | 约 41 天 | 平均约 6 周出一个版本 |
| 最长间隔 | 49 天 | 1.4 → 2.0（跨年 + 大版本前蓄力） |
| 最短间隔 | 32 天 | 3.4 → 3.5（稳定期小版本） |

分大版本看，更新节奏**早期慢、后来提速并趋稳**：

| 大版本 | 平均间隔 | 间隔个数 |
|---|---|---|
| 1.x | 44.8 天 | 5 |
| 2.x | 39.7 天 | 9 |
| 3.x | 39.9 天 | 9 |

## 怎么跑

1. 打开 [sqliteonline.com](https://sqliteonline.com)（或任意 SQLite 环境）
2. 粘贴 `wuwa-versions.sql` 建表
3. 运行下面的查询即可复现

```sql
SELECT
  a.ver AS from_ver,
  b.ver AS to_ver,
  julianday(b.release_date) - julianday(a.release_date) AS gap_days
FROM version a
JOIN version b
  ON b.ver = (SELECT MIN(ver) FROM version WHERE ver > a.ver)
ORDER BY a.release_date;
```

## 用到的 SQL 知识

自连接、相关子查询、`julianday()` 日期相减、`CAST` 取主版本号、`GROUP BY` + `AVG/COUNT`、`ROUND` 四舍五入。

## 数据说明

- 数据来源：鸣潮官方公告 + 灰机 wiki
- 统计区间：2024-05-23（1.0 公测）至 2026-09-30（3.7 上线）
