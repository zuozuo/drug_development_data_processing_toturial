# 临床试验数据处理学习指南

> 欢迎来到临床试验数据处理学习中心！本目录包含新药研发数据处理的完整知识体系。

---

## 新药研发数据处理全景图

下图展示了从临床试验设计到药监局提交的完整数据流程，点击各环节可跳转到对应文档：

```
┌─────────────────────────────────────────────────────────────────────────────────────────────┐
│                              新药研发数据处理完整流程                                          │
└─────────────────────────────────────────────────────────────────────────────────────────────┘

 【第一阶段：试验设计】
 ══════════════════════════════════════════════════════════════════════════════════════════════

    ┌─────────────────┐         ┌─────────────────┐
    │    Protocol     │────────▶│      SAP        │
    │   (试验方案)     │         │  (统计分析计划)  │
    │                 │         │                 │
    │ "试验怎么做"     │         │ "数据怎么分析"   │
    └────────┬────────┘         └────────┬────────┘
             │                           │
             │     试验开始前定稿          │    数据库锁定前定稿
             │                           │
             ▼                           ▼

 【第二阶段：数据采集】
 ══════════════════════════════════════════════════════════════════════════════════════════════

    ┌─────────────────────────────────────────────────────────────────────────┐
    │                        临床研究中心（医院）                               │
    │  ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐              │
    │  │ 知情同意 │───▶│ 筛选访视 │───▶│ 治疗期  │───▶│ 随访期  │              │
    │  └─────────┘    └─────────┘    └─────────┘    └─────────┘              │
    │                                                                         │
    │                              ▼                                          │
    │                    ┌─────────────────┐                                  │
    │                    │      CRF        │                                  │
    │                    │  (病例报告表)    │                                  │
    │                    │  原始临床数据    │                                  │
    │                    └────────┬────────┘                                  │
    └─────────────────────────────┼───────────────────────────────────────────┘
                                  │
                                  │ 数据清理、核查
                                  │
                                  ▼

 【第三阶段：数据标准化（SDTM）】
 ══════════════════════════════════════════════════════════════════════════════════════════════

    ┌─────────────────────────────────────────────────────────────────────────┐
    │                         SDTM 标准化处理                                  │
    │                                                                         │
    │  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐       │
    │  │   DM    │  │   VS    │  │   LB    │  │   AE    │  │   EX    │  ...  │
    │  │ 人口学  │  │生命体征 │  │实验室   │  │不良事件 │  │ 用药   │       │
    │  └─────────┘  └─────────┘  └─────────┘  └─────────┘  └─────────┘       │
    │                                                                         │
    │                    ┌─────────────────┐                                  │
    │                    │     ts.xpt      │  ← 关键！Trial Summary           │
    │                    └─────────────────┘                                  │
    │                                                                         │
    │  配套文件：                                                              │
    │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                  │
    │  │  Define.xml  │  │    aCRF      │  │    SDRG      │                  │
    │  │  (数据定义)   │  │ (带注释CRF)  │  │ (审评员指南) │                  │
    │  └──────────────┘  └──────────────┘  └──────────────┘                  │
    └─────────────────────────────────────────────────────────────────────────┘
                                  │
                                  │ 数据派生
                                  ▼

 【第四阶段：分析数据准备（ADaM）】
 ══════════════════════════════════════════════════════════════════════════════════════════════

    ┌─────────────────────────────────────────────────────────────────────────┐
    │                         ADaM 分析数据                                    │
    │                                                                         │
    │  ┌─────────────────────────────────────────────────────────────────┐   │
    │  │                      ADSL (受试者级别)                           │   │
    │  │              一人一行，包含人群标志、治疗分组等                    │   │
    │  └─────────────────────────────────────────────────────────────────┘   │
    │                              │                                          │
    │          ┌──────────────────┼──────────────────┐                       │
    │          ▼                  ▼                  ▼                       │
    │  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                 │
    │  │    ADVS     │    │    ADAE     │    │    ADLB     │    ...          │
    │  │  生命体征   │    │  不良事件   │    │  实验室     │                 │
    │  │(BDS结构)    │    │(OCCDS结构)  │    │(BDS结构)    │                 │
    │  └─────────────┘    └─────────────┘    └─────────────┘                 │
    │                                                                         │
    │  派生变量：AVAL, BASE, CHG, PCHG, ABLFL, ANL01FL ...                    │
    │                                                                         │
    │  配套文件：                                                              │
    │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                  │
    │  │  Define.xml  │  │    ADRG      │  │  ARM (可选)  │                  │
    │  │  (数据定义)   │  │ (审评员指南) │  │(分析结果元数据)│                │
    │  └──────────────┘  └──────────────┘  └──────────────┘                  │
    └─────────────────────────────────────────────────────────────────────────┘
                                  │
                                  │ 统计分析
                                  ▼

 【第五阶段：统计分析与报告】
 ══════════════════════════════════════════════════════════════════════════════════════════════

    ┌─────────────────────────────────────────────────────────────────────────┐
    │                        统计分析与输出                                    │
    │                                                                         │
    │  ┌─────────────────┐                                                    │
    │  │   分析程序      │  SAS/R 程序                                        │
    │  │ (Analysis Code) │  生成统计结果                                      │
    │  └────────┬────────┘                                                    │
    │           │                                                             │
    │           ▼                                                             │
    │  ┌─────────────────────────────────────────────────────────────────┐   │
    │  │                    TLFs (统计输出)                               │   │
    │  │  ┌───────────┐    ┌───────────┐    ┌───────────┐                │   │
    │  │  │  Tables   │    │  Figures  │    │ Listings  │                │   │
    │  │  │   表格    │    │   图形    │    │   列表    │                │   │
    │  │  └───────────┘    └───────────┘    └───────────┘                │   │
    │  └─────────────────────────────────────────────────────────────────┘   │
    │                              │                                          │
    │                              ▼                                          │
    │  ┌─────────────────────────────────────────────────────────────────┐   │
    │  │                      CSR (临床研究报告)                          │   │
    │  │                    ICH E3 标准结构                               │   │
    │  │              整合所有结果，撰写完整报告                           │   │
    │  └─────────────────────────────────────────────────────────────────┘   │
    └─────────────────────────────────────────────────────────────────────────┘
                                  │
                                  │ 打包提交
                                  ▼

 【第六阶段：药监局提交】
 ══════════════════════════════════════════════════════════════════════════════════════════════

    ┌─────────────────────────────────────────────────────────────────────────┐
    │                     eCTD 电子提交包                                      │
    │                                                                         │
    │  ┌─────────────────────────────────────────────────────────────────┐   │
    │  │  m5/ (Module 5: 临床研究)                                       │   │
    │  │  │                                                              │   │
    │  │  ├── datasets/                                                  │   │
    │  │  │   ├── sdtm/     ← SDTM数据 + Define.xml + aCRF + STF        │   │
    │  │  │   ├── adam/     ← ADaM数据 + Define.xml + ARM + STF         │   │
    │  │  │   ├── misc/     ← SDRG + ADRG                               │   │
    │  │  │   └── bimo/     ← BIMO数据（现场核查用）                     │   │
    │  │  │                                                              │   │
    │  │  ├── programs/     ← 分析程序（强烈建议）                        │   │
    │  │  │                                                              │   │
    │  │  └── reports/      ← CSR + TLFs + Protocol + SAP               │   │
    │  └─────────────────────────────────────────────────────────────────┘   │
    │                                                                         │
    │                              │                                          │
    │                              ▼                                          │
    │                    ┌─────────────────┐                                  │
    │                    │   FDA/NMPA      │                                  │
    │                    │   药监局审评     │                                  │
    │                    └─────────────────┘                                  │
    └─────────────────────────────────────────────────────────────────────────┘
```

---

## 按流程环节查阅文档

### 第一阶段：试验设计

| 环节 | 文档 | 说明 |
|-----|------|------|
| **Protocol** | [Protocol_Tutorial.md](./docs/Protocol_Tutorial.md) | 临床试验方案，定义试验怎么做 |
| **SAP** | [SAP_Tutorial.md](./docs/SAP_Tutorial.md) | 统计分析计划，定义数据怎么分析 |

### 第二阶段：数据采集

| 环节 | 文档 | 说明 |
|-----|------|------|
| **整体流程** | [Drug_Development_Data_Overview.md](./docs/Drug_Development_Data_Overview.md) | 新药研发全景概述 |

### 第三阶段：SDTM 标准化

| 环节 | 文档 | 说明 |
|-----|------|------|
| **SDTM 基础** | [SDTM_ADaM_Tutorial.md](./docs/SDTM_ADaM_Tutorial.md) | SDTM/ADaM 转换核心教程 |
| **SDTM 各域** | [SDTM_Domain_Guide.md](./docs/SDTM_Domain_Guide.md) | DM、VS、AE 等域的详细字段 |
| **aCRF** | [aCRF_Guide.md](./docs/aCRF_Guide.md) | 带注释病例报告表指南 |
| **Define.xml** | [Define_XML_Guide.md](./docs/Define_XML_Guide.md) | 数据定义文件指南 |
| **SDRG** | [Reviewers_Guide.md](./docs/Reviewers_Guide.md) | SDTM 审评员指南 |
| **STF** | [STF_Guide.md](./docs/STF_Guide.md) | Study Tagging File 指南 |

### 第四阶段：ADaM 分析数据

| 环节 | 文档 | 说明 |
|-----|------|------|
| **ADaM 完整指南** | [ADaM_Complete_Guide.md](./docs/ADaM_Complete_Guide.md) | ADSL、BDS、OCCDS 详解 |
| **Define.xml** | [Define_XML_Guide.md](./docs/Define_XML_Guide.md) | ADaM 数据定义文件 |
| **ADRG** | [Reviewers_Guide.md](./docs/Reviewers_Guide.md) | ADaM 审评员指南 |
| **ARM** | [ARM_Guide.md](./docs/ARM_Guide.md) | 分析结果元数据（可选） |

### 第五阶段：统计分析与报告

| 环节 | 文档 | 说明 |
|-----|------|------|
| **分析程序** | [Analysis_Programs_Guide.md](./docs/Analysis_Programs_Guide.md) | SAS/R 程序提交指南 |
| **CSR 与 TLFs** | [CSR_TLFs_Guide.md](./docs/CSR_TLFs_Guide.md) | 临床研究报告与统计输出 |

### 第六阶段：药监局提交

| 环节 | 文档 | 说明 |
|-----|------|------|
| **提交包总览** | [Submission_Package_Guide.md](./docs/Submission_Package_Guide.md) | 提交包完整结构与清单 |
| **eCTD 格式** | [eCTD_Guide.md](./docs/eCTD_Guide.md) | 电子通用技术文档格式 |
| **BIMO 数据** | [BIMO_Data_Guide.md](./docs/BIMO_Data_Guide.md) | 现场核查支持数据 |
| **STF** | [STF_Guide.md](./docs/STF_Guide.md) | eCTD 节点元数据标签 |

---

## 完整文档列表

### 核心教程（按推荐阅读顺序）

| 序号 | 文档 | 主题 | 阅读时间 |
|:---:|------|------|:-------:|
| 1 | [Drug_Development_Data_Overview.md](./docs/Drug_Development_Data_Overview.md) | 新药研发全景概述 | 30分钟 |
| 2 | [Protocol_Tutorial.md](./docs/Protocol_Tutorial.md) | 临床试验方案（Protocol） | 45分钟 |
| 3 | [SAP_Tutorial.md](./docs/SAP_Tutorial.md) | 统计分析计划（SAP） | 45分钟 |
| 4 | [SDTM_ADaM_Tutorial.md](./docs/SDTM_ADaM_Tutorial.md) | SDTM/ADaM 数据转换 | 60分钟 |
| 5 | [SDTM_Domain_Guide.md](./docs/SDTM_Domain_Guide.md) | SDTM 各域详解 | 45分钟 |
| 6 | [ADaM_Complete_Guide.md](./docs/ADaM_Complete_Guide.md) | ADaM 分析数据模型 | 90分钟 |
| 7 | [CSR_TLFs_Guide.md](./docs/CSR_TLFs_Guide.md) | 临床研究报告与 TLFs | 30分钟 |
| 8 | [Submission_Package_Guide.md](./docs/Submission_Package_Guide.md) | 提交包完整指南 | 30分钟 |

### 专题文档

| 文档 | 主题 | 说明 |
|------|------|------|
| [Define_XML_Guide.md](./docs/Define_XML_Guide.md) | Define.xml | 数据定义文件详解 |
| [eCTD_Guide.md](./docs/eCTD_Guide.md) | eCTD | 电子通用技术文档 |
| [Reviewers_Guide.md](./docs/Reviewers_Guide.md) | SDRG/ADRG | 审评员指南编写 |
| [aCRF_Guide.md](./docs/aCRF_Guide.md) | aCRF | 带注释病例报告表 |
| [STF_Guide.md](./docs/STF_Guide.md) | STF | Study Tagging File |
| [BIMO_Data_Guide.md](./docs/BIMO_Data_Guide.md) | BIMO | 生物研究监督数据 |
| [ARM_Guide.md](./docs/ARM_Guide.md) | ARM | 分析结果元数据 |
| [Analysis_Programs_Guide.md](./docs/Analysis_Programs_Guide.md) | 分析程序 | SAS/R 程序提交 |

---

## 按角色推荐阅读

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            你是什么角色？                                    │
└─────────────────────────────────────────────────────────────────────────────┘
        │
        ├─── 项目经理/临床运营
        │         │
        │         └──▶ 推荐：概述 → Protocol → SAP → 提交包
        │              [1] → [2] → [3] → [8]
        │
        ├─── 数据管理
        │         │
        │         └──▶ 推荐：概述 → Protocol → SDTM教程 → SDTM域 → aCRF
        │              [1] → [2] → [4] → [5] → aCRF指南
        │
        ├─── SDTM 程序员
        │         │
        │         └──▶ 推荐：SDTM教程 → SDTM域 → Define.xml → aCRF → STF
        │              [4] → [5] → Define指南 → aCRF指南 → STF指南
        │
        ├─── ADaM 程序员
        │         │
        │         └──▶ 推荐：SAP → SDTM教程 → ADaM指南 → Define.xml → 分析程序
        │              [3] → [4] → [6] → Define指南 → 分析程序指南
        │
        ├─── 统计师
        │         │
        │         └──▶ 推荐：Protocol → SAP → ADaM指南 → CSR/TLFs → ARM
        │              [2] → [3] → [6] → [7] → ARM指南
        │
        ├─── 医学写作
        │         │
        │         └──▶ 推荐：概述 → Protocol → CSR/TLFs → 提交包
        │              [1] → [2] → [7] → [8]
        │
        └─── 质量管理/法规事务
                  │
                  └──▶ 推荐：全部通读，重点关注提交包和 eCTD
                       所有文档 + Submission_Package_Guide + eCTD_Guide
```

---

## 快速查阅索引

### 按概念查找

| 我想了解... | 去看哪个文档 |
|------------|-------------|
| 新药研发整体流程 | [Drug_Development_Data_Overview.md](./docs/Drug_Development_Data_Overview.md) |
| Protocol 是什么 | [Protocol_Tutorial.md](./docs/Protocol_Tutorial.md) |
| SAP 是什么 | [SAP_Tutorial.md](./docs/SAP_Tutorial.md) |
| CRF 怎么变成 SDTM | [SDTM_ADaM_Tutorial.md](./docs/SDTM_ADaM_Tutorial.md) |
| SDTM 各域有哪些字段 | [SDTM_Domain_Guide.md](./docs/SDTM_Domain_Guide.md) |
| ADaM 派生变量怎么算 | [ADaM_Complete_Guide.md](./docs/ADaM_Complete_Guide.md) |
| 什么是 Define.xml | [Define_XML_Guide.md](./docs/Define_XML_Guide.md) |
| 什么是 aCRF | [aCRF_Guide.md](./docs/aCRF_Guide.md) |
| 什么是 STF | [STF_Guide.md](./docs/STF_Guide.md) |
| 什么是 BIMO | [BIMO_Data_Guide.md](./docs/BIMO_Data_Guide.md) |
| 什么是 ARM | [ARM_Guide.md](./docs/ARM_Guide.md) |
| 分析程序怎么提交 | [Analysis_Programs_Guide.md](./docs/Analysis_Programs_Guide.md) |
| CSR 怎么写 | [CSR_TLFs_Guide.md](./docs/CSR_TLFs_Guide.md) |
| 审评员指南包含什么 | [Reviewers_Guide.md](./docs/Reviewers_Guide.md) |
| 提交包有哪些内容 | [Submission_Package_Guide.md](./docs/Submission_Package_Guide.md) |
| eCTD 是什么格式 | [eCTD_Guide.md](./docs/eCTD_Guide.md) |

### 按术语查找

| 缩写 | 全称 | 中文 | 文档 |
|------|------|------|------|
| Protocol | Clinical Trial Protocol | 临床试验方案 | [Protocol_Tutorial.md](./docs/Protocol_Tutorial.md) |
| SAP | Statistical Analysis Plan | 统计分析计划 | [SAP_Tutorial.md](./docs/SAP_Tutorial.md) |
| CRF | Case Report Form | 病例报告表 | [Drug_Development_Data_Overview.md](./docs/Drug_Development_Data_Overview.md) |
| aCRF | annotated CRF | 带注释病例报告表 | [aCRF_Guide.md](./docs/aCRF_Guide.md) |
| SDTM | Study Data Tabulation Model | 研究数据制表模型 | [SDTM_ADaM_Tutorial.md](./docs/SDTM_ADaM_Tutorial.md) |
| ADaM | Analysis Data Model | 分析数据模型 | [ADaM_Complete_Guide.md](./docs/ADaM_Complete_Guide.md) |
| ADSL | Subject-Level Analysis Dataset | 受试者级别分析数据集 | [ADaM_Complete_Guide.md](./docs/ADaM_Complete_Guide.md) |
| Define.xml | Data Definition File | 数据定义文件 | [Define_XML_Guide.md](./docs/Define_XML_Guide.md) |
| STF | Study Tagging File | 研究标记文件 | [STF_Guide.md](./docs/STF_Guide.md) |
| ARM | Analysis Results Metadata | 分析结果元数据 | [ARM_Guide.md](./docs/ARM_Guide.md) |
| BIMO | Bioresearch Monitoring | 生物研究监督 | [BIMO_Data_Guide.md](./docs/BIMO_Data_Guide.md) |
| TLFs | Tables, Listings, Figures | 表格、列表、图形 | [CSR_TLFs_Guide.md](./docs/CSR_TLFs_Guide.md) |
| CSR | Clinical Study Report | 临床研究报告 | [CSR_TLFs_Guide.md](./docs/CSR_TLFs_Guide.md) |
| eCTD | Electronic Common Technical Document | 电子通用技术文档 | [eCTD_Guide.md](./docs/eCTD_Guide.md) |
| SDRG | Study Data Reviewer's Guide | SDTM 审评员指南 | [Reviewers_Guide.md](./docs/Reviewers_Guide.md) |
| ADRG | Analysis Data Reviewer's Guide | ADaM 审评员指南 | [Reviewers_Guide.md](./docs/Reviewers_Guide.md) |

---

## 学习建议

### 完全新手（7天学习计划）

| 天数 | 学习内容 | 文档 |
|:---:|---------|------|
| Day 1 | 建立整体认知 | [Drug_Development_Data_Overview.md](./docs/Drug_Development_Data_Overview.md) |
| Day 2 | 了解试验设计 | [Protocol_Tutorial.md](./docs/Protocol_Tutorial.md) |
| Day 3 | 了解统计规划 | [SAP_Tutorial.md](./docs/SAP_Tutorial.md) |
| Day 4 | 学习数据转换 | [SDTM_ADaM_Tutorial.md](./docs/SDTM_ADaM_Tutorial.md) |
| Day 5 | 深入 SDTM | [SDTM_Domain_Guide.md](./docs/SDTM_Domain_Guide.md) |
| Day 6 | 深入 ADaM | [ADaM_Complete_Guide.md](./docs/ADaM_Complete_Guide.md) |
| Day 7 | 了解提交要求 | [Submission_Package_Guide.md](./docs/Submission_Package_Guide.md) |

### 学习技巧

1. **带着问题读**：每篇文档开头都有"核心问题"，带着问题阅读更有效
2. **看图先于看字**：文档中有大量流程图，先看图建立框架
3. **动手实践**：如果有真实数据，边读边对照数据看
4. **做笔记**：记录不懂的术语，读完后回头查阅

---

## 更新日志

| 日期 | 更新内容 |
|------|---------|
| 2024-12-10 | 重构 README，添加完整流程图和文档索引 |
| 2024-12-10 | 新增 ARM、分析程序、BIMO、STF、aCRF 指南 |
| 2024-12-09 | 新增 ADaM 分析数据模型完全指南 |
| 2024-12-09 | 新增 Protocol 临床试验方案教程 |
| 2024-XX-XX | 初始版本 |

---

> 如有问题或建议，欢迎反馈！
# drug_development_data_processing_toturial
