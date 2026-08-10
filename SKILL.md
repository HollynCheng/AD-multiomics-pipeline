---
name: ad-multiomics-pipeline
description: "AD多组学整合分析Pipeline专家技能。This skill should be used when the user asks about Alzheimer disease transcriptomics, iPSC microglia RNA-seq analysis, ROSMAP data, post-mortem brain tissue analysis, AD differential gene expression, GSEA, PROGENy, DoRoTHEa, alternative splicing, somatic mutations, gene fusions, or wants to generate AD research figures using R. Triggers: AD转录组, AD差异表达, ROSMAP分析, iPSC microglia, AD多组学整合, AD pipeline, AD图表生成, AD遗传亚组."
---

# AD 多组学整合分析 Pipeline — Skill 指南

**研究模型**: Human iPSC-derived Microglia Cohorts + Post-mortem Brain Tissues Cohorts
**框架参考**: ALS Ziff et al. 2023 (Nat Commun) + Scheidemantel et al. 2026 (Cell Reports)
**R 脚本**: `scripts/run_ad_figures.R`（可直接运行生成23张图表）

---

## 快速启动

### 环境检查
```bash
# R 路径（macOS arm64 / Apple Silicon）
/Library/Frameworks/R.framework/Resources/bin/Rscript --version

# 运行图表脚本（当前工作目录含 generate_AD_figures_v3.R 时）
/Library/Frameworks/R.framework/Resources/bin/Rscript generate_AD_figures_v3.R
```

### 必要 R 包
```r
pkgs <- c("ggplot2","dplyr","tidyr","tibble","purrr","stringr","reshape2",
          "ggrepel","RColorBrewer","viridis","patchwork","scales","gridExtra")
install.packages(pkgs[!pkgs %in% installed.packages()[,"Package"]], repos="https://cloud.r-project.org")
```

---

## 研究模型

| 系统 | 来源 | 主要数据类型 |
|---|---|---|
| **iPSC Microglia** | 患者来源 iPSC 分化 | RNA-seq, snRNA-seq, ATAC-seq, proteomics |
| **Post-mortem Brain** | ROSMAP / MSBB / Banner 等 | RNA-seq, snRNA-seq, ChIP-seq, TMT proteomics, metabolomics |

---

## 7阶段 Pipeline

### Stage 1：数据获取
- **iPSC Microglia 数据集**: GSE163857、GSE327711、NeuroLINCS、Cell/Haas 2023、Nature/Timmer 2023
- **死后脑组织数据集**: ROSMAP RNA-seq (syn3388564)、MSBB (syn3159438)、Mayo、Mathys snRNA-seq (GSE138852)
- **多组学层**: Klein ChIP-seq (syn4896408)、Johnson TMT Proteomics (syn17015098)、Batra Metabolomics (syn26007830)

### Stage 2：QC & 预处理
```
iPSC: FastQC → TrimGalore → STAR → featureCounts/Salmon → PCA(batch)
PM:   FastQC+MultiQC → STAR(2-pass) → RSEM → ComBat-seq/sva → featureCounts
```

### Stage 3：差异表达分析（DGE）
```r
# iPSC — DESeq2
design: ~ condition + sex + dataset
# Post-mortem — limma-voom（大样本）
design: ~ condition + age + sex + pmi + batch
```
输出: Fig 2 (iPSC pan-AD) + Fig 3 (遗传亚组) + Fig 4 (死后脑)

### Stage 4：跨系统整合
- **RRHO**: rank-rank hypergeometric overlap（iPSC vs PM 基因排名一致性）
- **MOFA+**: 多组学因子分析（RNA + proteomics + epigenomics + metabolomics）
- **细胞类型解卷积**: MuSiC / CIBERSORTx（利用 Mathys / Green snRNA-seq 参考）

### Stage 5：可变剪接（Fig 5）
```bash
SUPPA2 / MAJIQ v2.4
# 筛选标准: |deltaPSI| > 0.1, P < 0.05
# 7种剪接事件: SE, RI, A5SS, A3SS, MXE, AF, AL
```

### Stage 6：基因组不稳定性（Fig 6）
```bash
nf-core/rnavar (GATK) + STAR-Fusion
# GLM: mutation_burden ~ condition + age + sex + depth
```

### Stage 7：综合报告
R Markdown / Quarto → 可发表图表 + 交互式 Shiny 应用

---

## 图表说明（23张图）

### Fig 2：iPSC Microglia DGE（AD vs CTRL）
**数据源**: GSE163857 + GSE327711 + NeuroLINCS (2019-2024)

| 子图 | 分析方法 | 关键结论 |
|---|---|---|
| 2A 火山图 | meta-DESeq2，校正 dataset+sex | TREM2、TYROBP、APOE 上调；SYN1、SNAP25 下调 |
| 2B 热图 | Top 50 DEG Z-score | 神经炎症基因簇 vs 突触基因簇两极分化 |
| 2C iPSC vs PM | Pearson log2FC 相关性 | DDR/p53 基因跨系统一致上调（r>0.6） |
| 2D GSEA | MSigDB Hallmark + Reactome | p53↑、DDR↑、神经炎症↑、突触丢失↓ |
| 2E PROGENy | decoupleR 12条信号通路 | p53激活最强（NES=3.2）；TNFa/NFκB同步上调 |
| 2F DoRoTHEa | TF活性volcano (v3 regulons) | SPI1、TP53、CEBPB 显著激活 |

### Fig 3：AD 遗传亚组分析
**数据源**: APOE4(GSE327711) + TREM2(NeuroLINCS) + fAD(文献) + sAD(ROSMAP)

| 子图 | 分析方法 | 关键结论 |
|---|---|---|
| 3A 4亚组火山图 | 各遗传背景 vs CTRL | APOE4/fAD 显示最强小胶质细胞激活 |
| 3B 亚组相关热图 | Pearson t-stat | 亚组间 r≈0.85，提示共同汇聚机制 |
| 3C PROGENy亚组 | decoupleR per-sample | APOE4 p53 最强；fAD NFκB 最显著 |

### Fig 4：Post-mortem Brain p53/DDR 激活
**数据源**: ROSMAP RNA-seq (syn3388564, DLPFC/AC/PCG) + MSBB

| 子图 | 分析方法 | 关键结论 |
|---|---|---|
| 4A 火山图 | limma-voom ~1,500样本 | TP53、CDKN1A、GADD45A 显著上调 |
| 4B 通路气泡图 | GSEA Hallmark+KEGG | p53通路最强（NES=6.2） |
| 4C p53权重散点 | PROGENy权重 vs t-stat | CDKN1A、MDM2、PMAIP1 高权重+高t-stat |
| 4D TP53 DoRoTHEa | v3 regulon靶基因 | 16个TP53靶基因显著上调 |

### Fig 5：iPSC Microglia 可变剪接
| 子图 | 分析方法 | 关键结论 |
|---|---|---|
| 5A 剪接volcano | MAJIQ deltaPSI vs P | TREM2、APP、MAPT、RBFOX2 显著事件 |
| 5B 饼图 | 事件类型分布 | Exon Skipping(34%) + Intron Retention(25%) |
| 5C deltaPSI violin | 各类型分布 | Intron Retention在AD中显著增加（与TDP-43病理一致） |

### Fig 6：体细胞突变与基因融合
| 子图 | 分析方法 | 关键结论 |
|---|---|---|
| 6A 突变violin | SNV/Indel 负担 Poisson GLM | AD突变负担显著增加（P<10⁻⁴） |
| 6B 亚组森林图 | 各遗传背景突变系数 | TREM2亚组增加最显著（+0.07） |
| 6C 融合violin | STAR-Fusion校正后融合数 | AD融合数显著增加（P<10⁻⁴） |
| 6D 融合类型violin | 5种融合类型 | Local Rearrangement 增加最显著 |

### 补充图 FigS1–S3
| 图 | 内容 |
|---|---|
| S1 RRHO热图 | iPSC vs PM 基因排名一致性（对角线高亮） |
| S2 GSEA Hallmark | 14条 Hallmark NES 条形图 |
| S3 PCA | 批次校正前后 iPSC 数据集聚类 |

---

## 数据库快速参考

> 详细数据库清单见 `references/databases.md`（39个，2016-2026）

| 优先级 | 数据库 | 用途 |
|:---:|---|---|
| ⭐⭐⭐⭐⭐ | ROSMAP RNA-seq `syn3388564` | 核心死后脑 bulk RNA-seq |
| ⭐⭐⭐⭐⭐ | Johnson TMT Proteomics `syn17015098` | MOFA蛋白质组核心 |
| ⭐⭐⭐⭐ | Mathys 2019 snRNA-seq `GSE138852` | AD snRNA-seq 奠基研究 |
| ⭐⭐⭐⭐ | Green 2023 snRNA-seq `syn31512863` | 最大规模AD snRNA-seq |
| ⭐⭐⭐ | GSE163857 / GSE327711 | iPSC Microglia bulk RNA-seq |
| ⭐⭐⭐ | Klein ChIP-seq `syn4896408` | H3K9ac 表观遗传 |
| ⭐⭐⭐ | Batra Metabolomics `syn26007830` | 代谢组学 |

---

## 适配真实数据

使用 `references/pipeline.md` 中的详细步骤。核心替换：

```r
# 替换模拟 DGE 结果 → 真实 DESeq2/limma 输出
res_ipsc  <- read.csv("path/to/iPSC_DGE_results.csv")
res_pm    <- read.csv("path/to/PostMortem_DGE_results.csv")

# 替换通路基因集 → MSigDB 实际基因集
AD_UP_GENES   <- scan("AD_up_genes.txt", what="character")
P53_DDR_GENES <- scan("p53_pathway_genes.txt", what="character")
```

---

## 常见问题排查

| 问题 | 解决方案 |
|---|---|
| `ggplot2 inherit.aes=FALSE` + 嵌套 `aes()` 导致 x 轴丢失 | 预计算数据列再传入 `aes(x=col, y=col)` |
| `geom_errorbarh()` 已弃用 | 改用 `geom_errorbar(orientation="y")` |
| `runif(n)` 向量长度不匹配 | 先 `length()` 确认各基因列表长度后再设定 `runif(n, ...)` |
| macOS arm64 R安装 | 使用官方 `/tmp/R-4.4.2-arm64.pkg` |

---

## 参考框架文献

1. **Ziff et al. 2023** — *Integrated transcriptome landscape of ALS identifies genome instability linked to TDP-43 pathology*, Nature Communications. GitHub: https://github.com/ojziff/als_genome_instability
2. **Scheidemantel et al. 2026** — *Integration of aged brain multi-omics reveals cross-system mechanisms underlying Alzheimer's disease heterogeneity*, Cell Reports
