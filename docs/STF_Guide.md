# STF（Study Tagging Files）完全指南

> 本文档面向初学者，详细介绍 eCTD 提交中 Study Tagging Files 的概念、作用和实践要点。

---

## 目录

1. [什么是 STF](#1-什么是-stf)
2. [为什么需要 STF](#2-为什么需要-stf)
3. [STF 的结构与内容](#3-stf-的结构与内容)
4. [STF 文件详解](#4-stf-文件详解)
5. [STF 与 eCTD 的关系](#5-stf-与-ectd-的关系)
6. [常见问题与最佳实践](#6-常见问题与最佳实践)
7. [合规要点](#7-合规要点)

---

## 1. 什么是 STF

### 1.1 基本概念

**STF（Study Tagging File）** 是一种 XML 格式的元数据文件，用于在 eCTD（电子通用技术文档）提交中"标记"研究数据的属性信息。

> **小白类比**：如果把提交包比作一本书，STF 就是书的"目录标签"——告诉读者每个章节是什么类型的内容、属于哪个研究、数据是什么性质的。

### 1.2 STF 的命名由来

```
Study Tagging File
  │      │     │
  │      │     └── File：文件
  │      └── Tagging：标记/打标签
  └── Study：研究

= 研究标记文件 = 给研究数据打标签的文件
```

### 1.3 STF 的位置

```
eCTD 提交包中 STF 的位置
│
├── m5/datasets/
│   ├── sdtm/
│   │   ├── *.xpt（SDTM 数据）
│   │   └── 📄 stf-sdtm.xml      ← SDTM 数据的 STF
│   │
│   ├── adam/
│   │   ├── *.xpt（ADaM 数据）
│   │   └── 📄 stf-adam.xml      ← ADaM 数据的 STF
│   │
│   └── stf/（或直接放在各目录下）
│       └── 📄 study-tagging-file.xml
│
└── 说明：STF 文件通常放在对应数据目录中
```

---

## 2. 为什么需要 STF

### 2.1 FDA 系统的需求

FDA 使用自动化系统处理大量提交：

```
没有 STF 的情况：
┌─────────────────────────────────────────┐
│ FDA 审评员：这个文件夹里的数据是什么？  │
│           是 SDTM 还是 ADaM？            │
│           属于哪个研究？                 │
│           是关键性研究还是支持性研究？    │
└─────────────────────────────────────────┘

有了 STF 的情况：
┌─────────────────────────────────────────┐
│ STF 告诉系统：                          │
│ ✓ 数据类型：SDTM Tabulation             │
│ ✓ 研究 ID：ABC-001                      │
│ ✓ 研究类型：Pivotal（关键性）           │
│ ✓ 适应症：高血压                        │
└─────────────────────────────────────────┘
```

### 2.2 STF 解决的问题

| 问题 | STF 提供的答案 |
|-----|---------------|
| 这是什么数据？ | tabulation（SDTM）还是 analysis（ADaM） |
| 属于哪个研究？ | 研究 ID 和研究标题 |
| 研究的重要性？ | pivotal（关键性）还是 supportive（支持性） |
| 研究什么适应症？ | indication（适应症描述） |

### 2.3 必须提供 STF 的原因

```
⚠️ FDA 技术拒收标准（TRC）明确要求：
├── 每个研究数据目录必须包含 STF
├── 缺少 STF 可能导致网关验证失败
└── STF 内容错误可能导致提交被退回
```

---

## 3. STF 的结构与内容

### 3.1 STF 的核心信息

```
STF 文件包含的关键信息
│
├── 📋 研究标识信息
│   ├── studyID（研究编号）
│   ├── studyTitle（研究标题）
│   └── sponsor（申办方）
│
├── 📊 数据类型信息
│   ├── dataType：tabulation（表格化数据/SDTM）
│   │            analysis（分析数据/ADaM）
│   └── dataStandard：SDTM v1.7, ADaMIG v1.1 等
│
├── 🏷️ 研究属性
│   ├── studyType：pivotal / supportive
│   ├── indication（适应症）
│   └── phase（试验阶段）
│
└── 📁 文件清单
    └── 该目录下包含的数据文件列表
```

### 3.2 简化的 STF 结构示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<studyTaggingFile>

  <!-- 研究基本信息 -->
  <studyIdentifier>ABC-001</studyIdentifier>
  <studyTitle>A Phase 3 Study of Drug X in Hypertension</studyTitle>

  <!-- 研究属性 -->
  <studyType>PIVOTAL</studyType>
  <indication>高血压</indication>
  <phase>3</phase>

  <!-- 数据类型 -->
  <dataType>TABULATION</dataType>
  <dataStandard>
    <name>SDTM</name>
    <version>1.7</version>
  </dataStandard>

  <!-- 文件清单 -->
  <fileInventory>
    <file name="dm.xpt" type="dataset"/>
    <file name="ae.xpt" type="dataset"/>
    <file name="define.xml" type="definition"/>
  </fileInventory>

</studyTaggingFile>
```

---

## 4. STF 文件详解

### 4.1 关键元素说明

| 元素 | 说明 | 可选值/示例 |
|-----|------|------------|
| **studyIdentifier** | 研究唯一标识 | "ABC-001" |
| **studyTitle** | 研究完整标题 | "A Phase 3..." |
| **studyType** | 研究类型 | PIVOTAL / SUPPORTIVE |
| **dataType** | 数据类型 | TABULATION / ANALYSIS |
| **dataStandard** | 数据标准 | SDTM 1.7 / ADaM 1.1 |
| **indication** | 适应症 | "Hypertension" |

### 4.2 studyType 的含义

```
研究类型（studyType）
│
├── PIVOTAL（关键性研究）
│   ├── 定义：支持主要疗效和安全性结论的核心研究
│   ├── 特点：通常是大规模、随机、对照、双盲
│   └── 作用：FDA 审批决策的主要依据
│
└── SUPPORTIVE（支持性研究）
    ├── 定义：为关键性研究提供补充证据
    ├── 特点：可能是剂量探索、特殊人群、机制研究
    └── 作用：支持关键性研究的结论
```

### 4.3 dataType 的含义

```
数据类型（dataType）
│
├── TABULATION（表格化数据）
│   └── 对应 SDTM 数据
│       └── 标准化的原始临床数据
│
└── ANALYSIS（分析数据）
    └── 对应 ADaM 数据
        └── 派生的分析就绪数据
```

---

## 5. STF 与 eCTD 的关系

### 5.1 eCTD 结构中的 STF

```
eCTD 模块结构与 STF
│
├── Module 1：区域行政信息（无 STF）
├── Module 2：CTD 摘要（无 STF）
├── Module 3：质量（无 STF）
├── Module 4：非临床（无 STF）
│
└── Module 5：临床研究报告 ← STF 主要在这里
    │
    ├── 5.3.5.1 所有研究报告
    │   └── [study-id]/
    │       ├── datasets/
    │       │   ├── sdtm/
    │       │   │   └── 📄 stf.xml ← SDTM STF
    │       │   └── adam/
    │       │       └── 📄 stf.xml ← ADaM STF
    │       └── reports/
    │           └── [CSR 及附录]
    │
    └── 其他临床摘要节点
```

### 5.2 STF 与其他元数据的关系

```
元数据文件家族
│
├── 📄 STF（Study Tagging File）
│   └── 作用：标记研究/数据目录的属性
│
├── 📄 Define.xml
│   └── 作用：定义数据集/变量的含义
│
├── 📄 index.xml（eCTD backbone）
│   └── 作用：eCTD 整体目录结构
│
└── 📄 ARM（Analysis Results Metadata）
    └── 作用：分析结果的元数据（可选）

关系图：
┌─────────────┐
│  index.xml  │ ← eCTD 总目录
└──────┬──────┘
       │ 指向
       ▼
┌─────────────┐
│    STF      │ ← 标记研究数据属性
└──────┬──────┘
       │ 关联
       ▼
┌─────────────┐
│ Define.xml  │ ← 定义数据内容
└─────────────┘
```

---

## 6. 常见问题与最佳实践

### 6.1 常见问题

#### Q1: 每个研究需要几个 STF？

```
典型情况：每个研究 2 个 STF
├── 1 个用于 SDTM 数据（dataType=TABULATION）
└── 1 个用于 ADaM 数据（dataType=ANALYSIS）
```

#### Q2: STF 文件命名有要求吗？

| 命名方式 | 示例 |
|---------|------|
| 通用名称 | stf.xml |
| 描述性名称 | study-tagging-file.xml |
| 类型区分 | stf-sdtm.xml, stf-adam.xml |

> FDA 无强制命名规则，但建议使用清晰、一致的命名。

#### Q3: STF 的 studyID 必须与 SDTM ts.xpt 一致吗？

**必须一致！**

```
数据一致性要求
├── STF.studyIdentifier
├── SDTM ts.xpt 中的 STUDYID
├── ADaM ADSL 中的 STUDYID
└── 以上三者必须完全相同
```

### 6.2 最佳实践

```
✅ STF 最佳实践清单
├── 1. studyID 与所有数据集保持一致
├── 2. studyTitle 与 Protocol 标题一致
├── 3. studyType 准确反映研究定位
├── 4. dataStandard 版本与 Define.xml 一致
├── 5. 文件清单与实际文件完全匹配
├── 6. 使用 FDA 官方工具验证 STF
└── 7. 每次提交前检查 STF 更新
```

---

## 7. 合规要点

### 7.1 FDA 技术拒收标准（TRC）

```
⚠️ STF 相关的技术拒收项
├── 缺少 STF 文件
├── STF 格式错误（XML 语法错误）
├── STF 中的 studyID 与数据不一致
└── dataType 与实际数据类型不匹配
```

### 7.2 提交前检查清单

在提交前，确认以下内容：

- [ ] SDTM 数据目录有对应的 STF
- [ ] ADaM 数据目录有对应的 STF
- [ ] STF 的 XML 格式正确（可用工具验证）
- [ ] studyID 与 ts.xpt/ADSL 一致
- [ ] studyType 正确（PIVOTAL/SUPPORTIVE）
- [ ] dataType 正确（TABULATION/ANALYSIS）
- [ ] 文件清单与目录内容匹配

### 7.3 常用验证工具

| 工具 | 用途 |
|-----|------|
| FDA eValidator | 验证 STF 格式合规性 |
| Pinnacle 21 | 综合验证包括 STF 检查 |
| 各 eCTD 发布工具 | 通常内置 STF 验证 |

---

## 附录：快速记忆

```
STF = Study Tagging File（研究标记文件）

作用：给 eCTD 中的研究数据"打标签"

核心内容：
├── 研究 ID（studyIdentifier）
├── 研究类型（PIVOTAL/SUPPORTIVE）
├── 数据类型（TABULATION=SDTM / ANALYSIS=ADaM）
└── 数据标准版本

位置：
├── m5/datasets/sdtm/stf.xml
└── m5/datasets/adam/stf.xml

注意：
├── 必须提供（TRC 要求）
├── studyID 必须一致
└── 格式必须正确
```

---

## 相关文档

| 想了解... | 阅读文档 |
|----------|---------|
| 提交包整体结构 | [Submission_Package_Guide.md](./Submission_Package_Guide.md) |
| eCTD 电子提交格式 | [eCTD_Guide.md](./eCTD_Guide.md) |
| SDTM 数据标准 | [SDTM_ADaM_Tutorial.md](./SDTM_ADaM_Tutorial.md) |
| Define.xml 数据定义 | [Define_XML_Guide.md](./Define_XML_Guide.md) |

---

> **参考资源**
> - [FDA Study Data Technical Conformance Guide](https://www.fda.gov/regulatory-information/search-fda-guidance-documents/study-data-technical-conformance-guide)
> - [FDA eCTD Resources](https://www.fda.gov/drugs/electronic-regulatory-submission-and-review/electronic-common-technical-document-ectd)

---

*本文档供学习参考，如有疑问请咨询专业人士。*
