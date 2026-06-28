# 连接池 & 线程池配置计算器

在线地址：**[maimai-git.github.io/pool-calculator](https://maimai-git.github.io/pool-calculator/pool-calculator.html)**
<p align="center">
  <img src="屏幕截图_28-6-2026_23463_maimai-git.github.io.jpeg" width="600" alt="计算器截图">
</p>
---

## 这是什么

输入数据库类型、应用框架、CPU/内存、业务类型，自动计算出：

- 数据库连接池大小（基于 HikariCP 公式 + Little's Law）
- DB max_connections（MySQL / PostgreSQL）
- 应用线程数 / Worker 数（Tomcat / Hyperf / Go / Node.js）
- acceptCount / backlog
- 连接数安全校验

## 支持维度

| 维度 | 选项 |
|------|------|
| 数据库 | MySQL / PostgreSQL |
| 应用框架 | Tomcat(Spring Boot) / Hyperf(Swoole) / Go(database/sql) / Node.js |
| 业务类型 | 轻量 CRUD / 混合报表 / 计算密集 / 大量外部调用 |
| 硬件 | 应用 CPU+内存、数据库 CPU+内存、磁盘类型、实例数 |

## 核心公式

- `pool_size = CPU核数 × 2 + 磁盘数（SSD=0, HDD=1）`
- `max_connections = max(min(实践上限, 内存硬上限), 应用需求+5)`
- `maxThreads = pool_size × io_wait_ratio`（Tomcat）
- PostgreSQL `max_connections` 上限 250（无 PgBouncer 时）

## 本地使用

```bash
open pool-calculator.html
```

纯前端，无需安装、无后端依赖。

---

## 调研参考

- [HikariCP Pool Sizing (Brett Wooldridge)](https://deepwiki.com/brettwooldridge/HikariCP/4.2-pool-sizing-and-performance-tuning) — 官方公式 `connections = CPU×2 + disk_count`
- [Connection Pool Sizing with Little's Law](https://www.michal-drozd.com/en/blog/connection-pool-littles-law/) — `pool = QPS × avg_query_duration`
- [PostgreSQL max_connections Performance Impacts (Cybertec)](https://www.cybertec-postgresql.com/en/max_connections-performance-impacts/) — 实测：512 空闲连接 TPS 下降 15%，建议 ≤300
- [pg-tuning-guide](https://github.com/edisedis777/pg-tuning-guide) — PgSQL 参数公式 `max_connections = (vCPU × 3) × 2`
- [Percona PgSQL max_connections Advisory](https://docs.percona.com/percona-monitoring-and-management/3/advisors/checks/configuration-pg-high-max-connections.html) — 建议 max_connections ≤300，超出必上 PgBouncer
- [Go database/sql Production Best Practices (Leapcell)](https://leapcell.io/blog/resource-pooling-in-go-explained) — MaxOpenConns ≤ 80% × DB max, ConnMaxLifetime < DB timeout
- [Tomcat 高性能调优指南 (阿里云开发者)](https://developer.aliyun.com/article/1681498) — 4 核 I/O 密集型 maxThreads 200-300
- [Swoole/Hyperf worker_num 生产配置 (PHP 中文网)](https://www.php.cn/faq/2493393.html) — `worker_num = swoole_cpu_num() × 2`（I/O 密集）