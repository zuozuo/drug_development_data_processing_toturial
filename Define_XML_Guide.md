# Define.xml 完全指南：数据的"说明书"

> 本文档面向完全不懂医药行业的读者，用最通俗的语言和实际例子，帮你彻底搞懂 Define.xml 是什么、为什么重要、里面有什么内容。

---

## 目录

1. [一个故事：为什么需要 Define.xml？](#1-一个故事为什么需要-definexml)
2. [Define.xml 一句话解释](#2-definexml-一句话解释)
3. [Define.xml 包含什么内容？](#3-definexml-包含什么内容)
4. [实际案例：看懂 Define.xml](#4-实际案例看懂-definexml)
5. [可追溯性：Define.xml 的核心价值](#5-可追溯性definexml-的核心价值)
6. [SDTM Define.xml vs ADaM Define.xml](#6-sdtm-definexml-vs-adam-definexml)
7. [常见问题解答](#7-常见问题解答)
8. [总结](#8-总结)

---

## 1. 一个故事：为什么需要 Define.xml？

### 想象一个场景

你是药监局的审评员，收到了一家制药公司提交的数据文件：

```
收到的文件：
├── adsl.xpt    （1000 行数据）
├── advs.xpt    （50000 行数据）
├── adae.xpt    （8000 行数据）
└── ...
```

打开 `adsl.xpt`，你看到：

| USUBJID | TRT01P | SAFFL | ITTFL | AGEGR1 | DCREASCD |
|---------|--------|-------|-------|--------|----------|
| XXX-001 | Drug A | Y | Y | 50-64 | COMPLETED |
| XXX-002 | Placebo | Y | N | ≥65 | AE |
| XXX-003 | Drug A | N | N | <50 | WITHDRAWAL |

**你的问题：**
- `SAFFL` 是什么意思？取值 Y/N 代表什么？
- `DCREASCD` 的取值 "AE" 是什么意思？
- `AGEGR1` 的分组规则是什么？为什么是 50-64 而不是 50-60？
- `TRT01P` 和 `TRT01A` 有什么区别？

**没有说明书，你根本看不懂这些数据！**

### Define.xml 就是这份"说明书"

```
Define.xml 告诉你：
─────────────────────────────────────────────────────────────

SAFFL = Safety Population Flag（安全性分析人群标志）
├── Y = 纳入安全性分析（至少接受过一次研究药物）
└── N = 不纳入安全性分析

DCREASCD = Reason for Discontinuation（停药原因代码）
├── COMPLETED = 完成试验
├── AE = 因不良事件退出
├── WITHDRAWAL = 受试者主动退出
└── ...

AGEGR1 = Age Group 1（年龄分组）
├── 派生规则：
│   IF AGE < 50 THEN "<50"
│   IF 50 ≤ AGE < 65 THEN "50-64"
│   IF AGE ≥ 65 THEN "≥65"
└── 来源：根据 SAP 3.2.1 节定义
```

---

## 2. Define.xml 一句话解释

> **Define.xml = 数据的说明书，告诉审评员每个数据是什么意思、从哪来、怎么算出来的。**

### 类比理解

| 类比场景 | 数据文件 | Define.xml |
|---------|----------|------------|
| 宜家家具 | 木板、螺丝、配件 | 安装说明书 |
| 手机 | 手机本体 | 用户手册 |
| 药品 | 药片 | 说明书（用法、成分、禁忌） |
| 考试 | 答题卡 | 评分标准 |

### 为什么用 XML 格式？

```
XML 格式的优点
──────────────
1. 机器可读：可以用程序自动解析和验证
2. 结构化：层次清晰，易于查找
3. 标准化：全球统一格式，FDA/NMPA 都认可
4. 可扩展：可以添加自定义内容
```

---

## 3. Define.xml 包含什么内容？

### 整体结构图

```
┌─────────────────────────────────────────────────────────────────┐
│                      Define.xml 完整结构                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  📚 1. 数据集定义（ItemGroupDef）                                │
│  ──────────────────────────────                                 │
│  定义每个数据集（表）：名称、用途、结构                            │
│  例：ADSL 是什么？有多少行？存储什么内容？                         │
│                                                                 │
│  📝 2. 变量定义（ItemDef）                                       │
│  ──────────────────────────                                     │
│  定义每个变量（列）：名称、类型、长度、含义                        │
│  例：SAFFL 是字符型，长度1，含义是"安全性分析人群标志"             │
│                                                                 │
│  📋 3. 代码表/受控术语（CodeList）                               │
│  ─────────────────────────────                                  │
│  定义变量的有效取值及其含义                                       │
│  例：SEX 只能取 M（男）或 F（女）                                 │
│                                                                 │
│  🔧 4. 派生方法（MethodDef）                                     │
│  ───────────────────────                                        │
│  定义派生变量的计算规则                                           │
│  例：CHG = AVAL - BASE                                          │
│                                                                 │
│  📎 5. 注释和文档链接（CommentDef / DocumentRef）                 │
│  ─────────────────────────────────────────                      │
│  额外说明和外部文档引用                                           │
│  例：参见 SAP 第 3.2.1 节                                        │
│                                                                 │
│  🌲 6. 值级元数据（ValueListDef）                                │
│  ──────────────────────────                                     │
│  针对不同参数的特定定义（主要用于 BDS 类数据集）                   │
│  例：PARAMCD=SYSBP 时，AVAL 的单位是 mmHg                        │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 3.1 数据集定义（告诉你有哪些表）

```
数据集定义示例
──────────────

数据集：ADSL
├── 名称：Subject-Level Analysis Dataset
├── 结构：每个受试者一行
├── 用途：存储受试者级别的基本信息和分析标志
├── 变量数量：45 个
├── 记录数量：200 条
├── 关键变量：USUBJID（主键）
└── 来源：DM, EX, DS 等 SDTM 域

数据集：ADVS
├── 名称：Vital Signs Analysis Dataset
├── 结构：每次检查一行
├── 用途：生命体征分析数据
├── 变量数量：52 个
├── 记录数量：15000 条
├── 关键变量：USUBJID, PARAMCD, AVISIT
└── 来源：VS 域 + ADSL
```

### 3.2 变量定义（告诉你每列是什么）

```
变量定义示例
──────────────

变量：USUBJID
├── 标签：Unique Subject Identifier
├── 类型：字符型
├── 长度：20
├── 角色：标识符（Identifier）
├── 是否必需：是
├── 来源：DM.USUBJID
└── 备注：全球唯一的受试者标识

变量：CHG
├── 标签：Change from Baseline
├── 类型：数值型
├── 长度：8
├── 角色：分析变量（Analysis Variable）
├── 是否必需：条件性（非基线记录必需）
├── 派生方法：CHG = AVAL - BASE
└── 备注：仅在 ABLFL ≠ Y 时计算
```

### 3.3 代码表（告诉你有效取值）

```
代码表示例
──────────────

代码表：SEX（性别）
├── M = Male（男性）
└── F = Female（女性）

代码表：DCREASCD（停药原因代码）
├── COMPLETED = Completed（完成试验）
├── AE = Adverse Event（因不良事件）
├── WITHDRAWAL = Subject Withdrew（受试者退出）
├── PROTOCOL VIOLATION = Protocol Violation（违反方案）
├── LOST TO FOLLOW UP = Lost to Follow-up（失访）
└── DEATH = Death（死亡）

代码表：AVISIT（分析访视）
├── Baseline = 基线
├── Week 1 = 第 1 周
├── Week 4 = 第 4 周
├── Week 8 = 第 8 周
├── Week 12 = 第 12 周
└── End of Treatment = 治疗结束
```

### 3.4 派生方法（告诉你怎么算的）

```
派生方法示例
──────────────

方法：CHG 的计算
├── 公式：CHG = AVAL - BASE
├── 说明：变化值等于当前分析值减去基线值
└── 适用条件：非基线记录（ABLFL ≠ Y）

方法：PCHG 的计算
├── 公式：PCHG = (CHG / BASE) × 100
├── 说明：变化百分比
└── 适用条件：BASE ≠ 0 且非基线记录

方法：BASE 的确定
├── 规则：取 ABLFL = Y 的记录的 AVAL 值
├── 说明：基线值是治疗前最后一次非空检查值
└── 来源：同一 USUBJID + PARAMCD 下 ABLFL=Y 的记录

方法：AGEGR1 的派生
├── 规则：
│   IF AGE < 50 THEN AGEGR1 = "<50"
│   IF 50 ≤ AGE < 65 THEN AGEGR1 = "50-64"
│   IF AGE ≥ 65 THEN AGEGR1 = "≥65"
└── 来源：根据 SAP 第 3.2.1 节定义的年龄分组
```

---

## 4. 实际案例：看懂 Define.xml

### 4.1 场景：审评员想知道"血压变化"怎么算的

**步骤 1：找到数据集定义**

审评员打开 Define.xml，在数据集列表中找到 ADVS：

```
ADVS - Analysis Dataset for Vital Signs
─────────────────────────────────────────
描述：生命体征分析数据集
结构：每条检查记录一行（One Record per Subject per Parameter per Analysis Visit）
用途：分析生命体征变化
记录数：15,280 条
来源域：SDTM.VS
```

**步骤 2：找到变量定义**

在 ADVS 的变量列表中，找到 CHG（变化值）：

```
CHG - Change from Baseline
─────────────────────────────
标签：Change from Baseline
类型：数值型
长度：8
必需性：条件性必需（非基线记录）
派生方法：MT.CHG.ADVS
```

**步骤 3：查看派生方法**

点击派生方法链接，看到：

```
MT.CHG.ADVS - CHG Derivation for ADVS
──────────────────────────────────────
计算公式：CHG = AVAL - BASE

详细说明：
1. AVAL 是当前记录的分析值
2. BASE 是基线值（从 ABLFL=Y 的记录获取）
3. 只有非基线记录才计算 CHG
4. 基线记录的 CHG 为空

示例：
患者001，收缩压
├── 基线：AVAL=152, BASE=152, CHG=.（空）
├── 第1周：AVAL=148, BASE=152, CHG=-4
└── 第4周：AVAL=138, BASE=152, CHG=-14
```

**步骤 4：验证数据**

审评员打开 ADVS 数据，按照上述规则验证：

| USUBJID | PARAMCD | AVISIT | AVAL | BASE | CHG | ABLFL |
|---------|---------|--------|------|------|-----|-------|
| 001 | SYSBP | Baseline | 152 | 152 | . | Y |
| 001 | SYSBP | Week 1 | 148 | 152 | -4 | |
| 001 | SYSBP | Week 4 | 138 | 152 | -14 | |

**验证结果：计算正确！**

### 4.2 Define.xml 的 XML 结构示例

```xml
<!-- 数据集定义 -->
<ItemGroupDef OID="IG.ADVS" Name="ADVS" Repeating="Yes"
              Purpose="Analysis" Structure="One record per subject per parameter per analysis visit">
    <Description>
        <TranslatedText xml:lang="en">Analysis Dataset for Vital Signs</TranslatedText>
    </Description>
    <ItemRef ItemOID="IT.USUBJID" Mandatory="Yes"/>
    <ItemRef ItemOID="IT.PARAMCD" Mandatory="Yes"/>
    <ItemRef ItemOID="IT.AVAL" Mandatory="No"/>
    <ItemRef ItemOID="IT.BASE" Mandatory="No"/>
    <ItemRef ItemOID="IT.CHG" Mandatory="No" MethodOID="MT.CHG.ADVS"/>
</ItemGroupDef>

<!-- 变量定义 -->
<ItemDef OID="IT.CHG" Name="CHG" DataType="float" Length="8">
    <Description>
        <TranslatedText xml:lang="en">Change from Baseline</TranslatedText>
    </Description>
</ItemDef>

<!-- 派生方法定义 -->
<MethodDef OID="MT.CHG.ADVS" Name="CHG Derivation" Type="Computation">
    <Description>
        <TranslatedText xml:lang="en">
            CHG = AVAL - BASE
            Change from baseline is calculated as the analysis value (AVAL)
            minus the baseline value (BASE). This derivation is only performed
            for non-baseline records (ABLFL ne 'Y').
        </TranslatedText>
    </Description>
</MethodDef>

<!-- 代码表定义 -->
<CodeList OID="CL.SEX" Name="Sex" DataType="text">
    <CodeListItem CodedValue="M">
        <Decode><TranslatedText xml:lang="en">Male</TranslatedText></Decode>
    </CodeListItem>
    <CodeListItem CodedValue="F">
        <Decode><TranslatedText xml:lang="en">Female</TranslatedText></Decode>
    </CodeListItem>
</CodeList>
```

---

## 5. 可追溯性：Define.xml 的核心价值

### 什么是可追溯性？

> **可追溯性 = 从最终结论追溯到原始数据的能力**

在临床试验中，这意味着：

```
CSR（临床研究报告）中的结论：
    "治疗组收缩压平均降低 14.2 mmHg，显著优于安慰剂组（p<0.001）"
                │
                │ 这个结论从哪来？
                ▼
    ┌─────────────────────────────────────────┐
    │  TLF（表格）：Table 14.2.1              │
    │  主要疗效分析 - 收缩压变化             │
    │                                        │
    │  治疗组: n=100, Mean CHG = -14.2       │
    │  安慰剂: n=100, Mean CHG = -2.1        │
    │  p-value = <0.001                      │
    └──────────────────┬──────────────────────┘
                       │ 这个表格的数据从哪来？
                       ▼
    ┌─────────────────────────────────────────┐
    │  ADaM（分析数据）：ADVS                 │
    │  PARAMCD = SYSBP, AVISIT = Week 12     │
    │  ANL01FL = Y（纳入主要分析）            │
    │                                        │
    │  Define.xml 告诉你：                   │
    │  • CHG = AVAL - BASE                   │
    │  • BASE = 基线值                       │
    │  • ANL01FL 的定义规则                  │
    └──────────────────┬──────────────────────┘
                       │ 分析数据从哪来？
                       ▼
    ┌─────────────────────────────────────────┐
    │  SDTM（标准数据）：VS                   │
    │  VSTESTCD = SYSBP                      │
    │                                        │
    │  Define.xml 告诉你：                   │
    │  • VSORRES = 原始检查值                │
    │  • VSORRESU = mmHg                     │
    └──────────────────┬──────────────────────┘
                       │ 标准数据从哪来？
                       ▼
    ┌─────────────────────────────────────────┐
    │  CRF（病例报告表）                      │
    │  生命体征检查表                         │
    │  收缩压: _____ mmHg                    │
    │                                        │
    │  aCRF 告诉你：                         │
    │  • 这个字段对应 VS.VSORRES             │
    └─────────────────────────────────────────┘
```

### 可追溯性链条

```
┌─────────────────────────────────────────────────────────────────────┐
│                        数据可追溯性链条                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   CRF 原始采集                                                       │
│       │                                                             │
│       │  aCRF（带注释的CRF）说明对应关系                              │
│       ▼                                                             │
│   SDTM 标准存储                                                      │
│       │                                                             │
│       │  SDTM Define.xml 解释变量含义                                │
│       ▼                                                             │
│   ADaM 分析数据                                                      │
│       │                                                             │
│       │  ADaM Define.xml 解释派生逻辑                                │
│       ▼                                                             │
│   TLFs 统计输出                                                      │
│       │                                                             │
│       │  分析程序说明生成逻辑                                        │
│       ▼                                                             │
│   CSR 临床报告                                                       │
│                                                                     │
│   ════════════════════════════════════════════════════════════     │
│                                                                     │
│   审评员可以：                                                       │
│   1. 看 CSR → 发现某个结论存疑                                       │
│   2. 看 TLF → 找到对应的统计表格                                     │
│   3. 看 ADaM + Define → 理解数据是怎么派生的                         │
│   4. 看 SDTM + Define → 验证原始数据                                 │
│   5. 看 aCRF → 追溯到原始采集表格                                    │
│                                                                     │
│   任何一个环节都可以验证和审计！                                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 6. SDTM Define.xml vs ADaM Define.xml

### 两个 Define.xml 的区别

提交包中需要两个 Define.xml 文件：

```
提交包结构
├── sdtm/
│   ├── dm.xpt
│   ├── vs.xpt
│   ├── ae.xpt
│   ├── define.xml        ← SDTM Define.xml
│   └── define.pdf
│
└── adam/
    ├── adsl.xpt
    ├── advs.xpt
    ├── adae.xpt
    ├── define.xml        ← ADaM Define.xml
    └── define.pdf
```

### 内容对比

| 方面 | SDTM Define.xml | ADaM Define.xml |
|------|-----------------|-----------------|
| **描述的数据** | SDTM 域（DM, VS, AE...） | ADaM 数据集（ADSL, ADVS...） |
| **变量来源** | 主要是 CRF 采集 | 主要是 SDTM 派生 |
| **派生方法** | 较少（主要是转换规则） | 较多（计算公式、业务规则） |
| **代码表** | CDISC 标准受控术语 | 可能有自定义代码表 |
| **重点内容** | 数据标准化规则 | 分析派生逻辑 |

### SDTM Define.xml 重点

```
SDTM Define.xml 主要说明：
──────────────────────────

1. 变量对应关系
   CRF "收缩压" → SDTM VS.VSORRES（VSTESTCD=SYSBP）

2. 代码转换规则
   CRF "男" → SDTM "M"
   CRF "第1周" → SDTM "WEEK 1"

3. 日期格式转换
   CRF "2024年1月15日" → SDTM "2024-01-15"

4. 数据结构
   每个患者在 DM 域有 1 行
   每个患者在 VS 域有多行（每次检查一行）
```

### ADaM Define.xml 重点

```
ADaM Define.xml 主要说明：
──────────────────────────

1. 派生变量计算
   CHG = AVAL - BASE
   PCHG = (CHG / BASE) × 100

2. 分析人群定义
   SAFFL = Y 表示纳入安全性分析
   规则：至少接受过一次研究药物

3. 基线确定规则
   ABLFL = Y 表示基线记录
   规则：治疗前最后一次非空检查

4. 分析标志定义
   ANL01FL = Y 表示纳入主要分析
   规则：非筛选失败 + 有基线 + 有主要时间点数据
```

---

## 7. 常见问题解答

### Q1: Define.xml 是谁来写的？

```
Define.xml 的编写职责
─────────────────────

SDTM Define.xml：
├── 主要负责：SDTM 程序员 / 数据管理
├── 审核：统计师、质量控制
└── 时间：SDTM 数据集完成后

ADaM Define.xml：
├── 主要负责：SAS 程序员
├── 审核：统计师、质量控制
└── 时间：ADaM 数据集完成后

通常使用专业工具生成（如 Pinnacle 21 Define.xml Builder）
```

### Q2: Define.xml 和 Define.pdf 有什么区别？

```
Define.xml vs Define.pdf
────────────────────────

Define.xml（必须提交）：
├── 格式：XML（机器可读）
├── 用途：程序自动解析、合规验证
└── 审评员用法：通过 XML 查看器或转换后查看

Define.pdf（可选但推荐）：
├── 格式：PDF（人类可读）
├── 用途：审评员直接阅读
└── 来源：由 Define.xml 转换生成

关系：Define.pdf 是 Define.xml 的"人类友好版本"
```

### Q3: Define.xml 版本有什么区别？

```
Define.xml 版本历史
───────────────────

Define-XML 1.0（已淘汰）：
└── 基础版本，功能有限

Define-XML 2.0（逐步淘汰）：
└── 增加了方法定义、注释、文档链接

Define-XML 2.1（当前推荐）：
├── 增强的值级元数据
├── 更好的可追溯性支持
└── FDA 当前要求版本
```

### Q4: 如何验证 Define.xml 是否合规？

```
Define.xml 验证工具
───────────────────

Pinnacle 21 Community/Enterprise：
├── 检查 XML 语法正确性
├── 验证是否符合 CDISC 标准
├── 检查与实际数据集的一致性
└── 生成验证报告

常见问题：
├── 变量在 Define 中定义但数据集中没有
├── 代码表取值与实际数据不匹配
├── 派生方法描述不清晰
└── 缺少必需的元数据
```

### Q5: Define.xml 中最容易出错的地方？

```
常见错误
────────

1. 变量长度不匹配
   Define 定义长度=8，实际数据长度=10
   → 导致验证失败

2. 代码表不完整
   实际数据中出现了未定义的取值
   → 审评员无法理解

3. 派生方法描述不清
   只写"CHG = AVAL - BASE"，没解释什么情况下计算
   → 审评员有疑问

4. 来源信息缺失
   没说明变量从哪个 SDTM 域来
   → 无法追溯

5. 数据集描述不准确
   说"每患者一行"但实际有多行
   → 误导审评员
```

---

## 8. 总结

### 一张图总结 Define.xml

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                      Define.xml 全景图                              │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   ┌─────────────────────────────────────────────────────────────┐  │
│   │                    Define.xml 是什么？                       │  │
│   │                                                             │  │
│   │              数据的"说明书"/"字典"/"元数据"                  │  │
│   │                                                             │  │
│   │  告诉审评员：                                               │  │
│   │  • 每个数据集是什么？                                       │  │
│   │  • 每个变量什么意思？                                       │  │
│   │  • 代码取值代表什么？                                       │  │
│   │  • 派生变量怎么算的？                                       │  │
│   └─────────────────────────────────────────────────────────────┘  │
│                              │                                      │
│          ┌───────────────────┼───────────────────┐                  │
│          │                   │                   │                  │
│          ▼                   ▼                   ▼                  │
│   ┌─────────────┐    ┌─────────────┐    ┌─────────────┐           │
│   │  SDTM       │    │  ADaM       │    │  共同作用   │           │
│   │  Define.xml │    │  Define.xml │    │             │           │
│   ├─────────────┤    ├─────────────┤    ├─────────────┤           │
│   │ • DM, VS,   │    │ • ADSL,     │    │ 建立完整的  │           │
│   │   AE...域   │    │   ADVS...   │    │ 可追溯性    │           │
│   │ • CRF对应   │    │ • 派生逻辑  │    │             │           │
│   │ • 代码转换  │    │ • 分析规则  │    │ CRF → SDTM  │           │
│   │             │    │             │    │     → ADaM  │           │
│   │             │    │             │    │     → TLFs  │           │
│   │             │    │             │    │     → CSR   │           │
│   └─────────────┘    └─────────────┘    └─────────────┘           │
│                                                                     │
│   ════════════════════════════════════════════════════════════     │
│                                                                     │
│   📌 关键要点：                                                     │
│                                                                     │
│   1. Define.xml 是电子提交包的必需文件                              │
│   2. SDTM 和 ADaM 各需要一个 Define.xml                             │
│   3. 推荐使用 Define-XML 2.1 版本                                   │
│   4. 建立从 CSR 到 CRF 的完整可追溯性                               │
│   5. 使用工具（如 Pinnacle 21）验证合规性                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 关键要点回顾

| 要点 | 说明 |
|------|------|
| **是什么** | 数据的 XML 格式说明书 |
| **为什么需要** | 让审评员能看懂数据 |
| **包含什么** | 数据集定义、变量定义、代码表、派生方法 |
| **有几个** | 两个：SDTM Define.xml + ADaM Define.xml |
| **核心价值** | 建立可追溯性，从结论追溯到原始数据 |
| **版本要求** | 推荐 Define-XML 2.1 |

### 一句话总结

> **Define.xml 是临床试验数据的"说明书"，告诉审评员每个数据是什么意思、从哪来、怎么算的，是建立数据可追溯性的关键文件。**

---

## 扩展阅读

- [SDTM/ADaM 入门教程](./SDTM_ADaM_Tutorial.md) - 了解数据处理流程
- [SDTM 域指南](./SDTM_Domain_Guide.md) - 了解 SDTM 各域详细内容
- [药监局提交包指南](./Submission_Package_Guide.md) - 了解完整提交要求
- [ADaM 完全指南](./ADaM_Complete_Guide.md) - 了解 ADaM 数据模型

---

*本文档由 AI 辅助生成，如有疑问请咨询专业人士。*
