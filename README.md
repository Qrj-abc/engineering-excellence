# Engineering Excellence 🛠️

> 生产级工程纪律，蒸馏自 20+ 本经典著作与权威标准。
> 不是又一份"代码风格清单"——而是一套**按风险分级、按需加载**的路由式工程技能（Agent Skill）。

写代码容易，写**能上生产**的代码难。这个技能把散落在几十本经典书籍里的工程智慧，压缩成一套可执行、可核查、不浪费上下文的纪律系统：它先判断你手上的任务有多危险（T1–T4），再只给你此刻真正需要的参考与检查清单。

---

## ✨ 它解决什么问题

传统技能包的通病是"一股脑全塞给你"——几十条规则堆在一个文件里，真正干活时 90% 用不上，还挤爆上下文。

`engineering-excellence` 的做法恰恰相反：

- **路由式设计**：`SKILL.md` 只负责分类和路由，不倾倒通用建议
- **按需加载**：默认只加载分类选中的 1–3 份参考，用不到的保持未读
- **风险分级**：按爆炸半径和可逆性给任务定级，级别决定哪些检查是强制的
- **证据闭环**：没跑测试、没记录验证缺口，就不算"完成"

---

## 🧭 核心设计

### 1. 风险分级 T1–T4（门禁是累积的）

| 级别 | 含义 | 强制检查 |
|---|---|---|
| T1 | 影响局部、立即可逆 | 无检查清单，轻量证据即可 |
| T2 | 影响单个功能/区域 | `change-review.md` 核心子集 |
| T3 | 影响大量用户或多组件 | `change-review.md` 全量 + `production-readiness.md` |
| T4 | 可能造成严重伤害 | T2/T3 全部 + 安全或事故检查清单 |

> ⚠️ 关键洞察：分级看**影响**，不看文件类型。改一行鉴权代码可能是 T4，一个藏在功能开关后的 2000 行功能可能只是 T2。
> 文档也不是天然"琐碎"的——API 文档、合规记录、用户可见文案，按"写错的代价"分级。

### 2. 范式选择：SDD / TDD / DDD / DADD

按任务**主导风险**选主范式：

| 主导风险 | 主范式 | 理由 |
|---|---|---|
| 契约/API/合规 | **SDD** 先写契约再写码 | 行为由协议固定 |
| 行为回归/难验证的重构 | **TDD** 先写失败测试 | 测试既是规格又是安全网 |
| 业务复杂度/领域语言 | **DDD** 限界上下文+统一语言 | 领域驱动设计，而非框架 |
| 无单一主导风险 | 标准流程 | 保护风险的最小规则集胜出 |

**DADD（文档驱动 AI 开发）不是第四主范式**，而是叠加在 AI 生成/多文件大改动上的执行层——写规格 → 生成 → 测试 → 迭代。

---

## 📂 仓库结构

```
engineering-excellence/
├── SKILL.md                    # 路由核心：分类 → 定级 → 选择加载
├── references/                 # 11 份领域参考（决策规则 + 触发规则）
│   ├── architecture-domain.md  # 架构与领域：边界、模型、企业模式
│   ├── core-codecraft.md       # 核心代码手艺：编写、重构、遗留代码
│   ├── data-distributed.md     # 数据与分布式：一致性、可靠性、重放
│   ├── delivery.md             # 持续交付与 DORA
│   ├── observability-sre.md    # 可观测性与 SRE：SLO、告警、值班
│   ├── security.md             # 安全工程：威胁建模、输入验证、密钥
│   ├── performance.md          # 性能工程：测量、容量、调优
│   ├── infrastructure.md       # 基础设施：IaC、CI/CD、容器
│   ├── team-process.md         # 团队流程：拓扑、决策、技术债
│   ├── incident-chaos.md       # 事故与韧性：响应、混沌工程
│   └── compliance-audit.md     # 合规与审计：GDPR、SOC、可审计性
└── checklists/                 # 4 份门禁检查清单
    ├── change-review.md        # 变更评审（T2+ 强制）
    ├── production-readiness.md # 生产就绪（T3+ 强制）
    ├── security-review.md      # 安全评审（触及安全面强制）
    └── incident-review.md      # 事故复盘（事故后强制，无责备）
```

---

## 🚀 如何安装与使用

### 作为 Codex Skill 安装

把本仓库克隆或复制到你的技能目录（通常是 `~/.codex/skills/`），例如：

```bash
git clone https://github.com/Qrj-abc/engineering-excellence.git ~/.codex/skills/engineering-excellence
```

重启 Codex 后，技能自动生效。

### 触发方式

直接说需求即可，例如：

- "帮我写这个模块，按生产标准来"
- "给这段代码做一次代码评审"
- "这个改动要上生产，检查一下就绪度"
- "做一个安全评审"
- "复盘一下刚才的事故"

技能内部会自动：**分类 → 定级 → 只加载需要的参考和检查清单 → 执行 → 用证据收尾**。

### 完成标准

T2+ 的任务，只有在以下证据齐备时才算完成：

- ✅ 一句话变更意图 + 保留行为已说明
- ✅ 测试通过，或验证缺口已记录原因
- ✅ 检查清单每一项都有答案（不适用的标注 `N/A: 原因`）
- ✅ 重构前后测试全绿
- ✅ 风险与未知项已列出
- ✅ 触及区域不比来时更糟（不制造新的破窗）

> 🔒 **权限边界**：这个技能本身不授予提交、推送、合并或发布权——那些永远等你发话。

---

## 🎯 适用场景

| 场景 | 用到的参考/检查 |
|---|---|
| 新模块/服务/架构设计 | `architecture-domain.md` + `change-review.md` |
| 数据模型/分布式/事件流 | `data-distributed.md` + `observability-sre.md` |
| 生产加固（超时/重试/熔断） | `data-distributed.md` + `production-readiness.md` |
| 发布/部署/回滚/功能开关 | `delivery.md` + `production-readiness.md` |
| 监控/日志/追踪/SLO | `observability-sre.md` + `incident-chaos.md` |
| 安全/鉴权/密钥/威胁建模 | `security.md` + `security-review.md` |
| 延迟/吞吐/容量 | `performance.md` + `observability-sre.md` |
| 基础设施/IaC/CI-CD | `infrastructure.md` + `delivery.md` |
| 团队流程/评审文化 | `team-process.md` + `change-review.md` |
| 事故/断服/韧性 | `incident-chaos.md` + `incident-review.md` |
| 合规/法规/审计 | `compliance-audit.md` + `security-review.md` |

---

## 📚 内容来源

规则蒸馏自 20+ 本著作与权威标准，包括：

- **代码与架构**：*A Philosophy of Software Design*、*Clean Architecture*、*Clean Code*、*Code Complete*
- **领域建模**：*Domain-Driven Design*、*Implementing DDD*、*DDD Distilled*、*Patterns of Enterprise Application Architecture*
- **重构与遗留**：*Refactoring*、*Refactoring.Guru*、*Working Effectively with Legacy Code*
- **数据与生产**：*Designing Data-Intensive Applications*、*Release It!*
- **工程心智**：*The Pragmatic Programmer*
- **交付与运维**：*Continuous Delivery*、*Accelerate*、DORA 报告、Google SRE 丛书、*Observability Engineering*
- **安全与合规**：OWASP ASVS / SAMM、NIST SSDF、SLSA、GDPR / SOC 2 / PCI DSS 语境
- **性能**：*Systems Performance*、*High Performance MySQL*
- **团队**：*Team Topologies*、ADR 实践、Fowler 技术债模型

---

## ⚠️ 非目标（Non-Goals）

- ❌ 不是某种语言/框架的样式指南——团队约定叠加其上
- ❌ 不是安全或合规官的替代品——T4 级安全/合规结论上交给人类
- ❌ 不是过度工程的借口——保护风险所需的最小规则集，永远优先

---

*本仓库由「ciembor/agent-rules-books」等开源规则集与工程实践提炼而成，并针对 AI Agent 协作场景做了路由式重构。*
