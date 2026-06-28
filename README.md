# 连接池 & 线程池配置计算器

在线地址：**[maimai-git.github.io/pool-calculator](https://maimai-git.github.io/pool-calculator/pool-calculator.html)**

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
