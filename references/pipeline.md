# AD 多组学整合 Pipeline — 详细技术文档

> 参考 ALS Ziff et al. 2023 框架，适配 AD iPSC Microglia + Post-mortem Brain 研究模型

---

## 1. 分析框架对照（ALS Fig 2-6 → AD Fig 2-6）

| ALS 模块 | ALS 分析内容 | AD 适配分析 | 数据来源 |
|---|---|---|---|
| **Fig 2** | iPSMNs pan-ALS DGE | **Fig 2: iPSC Microglia DGE** | GSE163857 + GSE327711 + NeuroLINCS |
| **Fig 2A** | 火山图（meta-DESeq2） | 火山图 | 同上 |
| **Fig 2B** | Top DEG 热图（Z-score） | 热图 | 同上 |
| **Fig 2C** | iPSC vs 尸脊 FC 相关性 | iPSC vs Post-mortem FC 相关性 | 同上 + ROSMAP RNA-seq |
| **Fig 2D** | GSEA Hallmark pathways | GSEA AD pathways | 同上 |
| **Fig 2E** | PROGENy 信号通路 | PROGENy（p53/TNFa/NFkappaB） | 同上 |
| **Fig 2F** | DoRoTHEa TF 活性 | DoRoTHEa TF（SPI1/CEBPB/IRF1） | 同上 |
| **Fig 3** | ALS 遗传亚组（C9/SOD1/FUS/TARDBP） | **Fig 3: AD 遗传亚组** | APOE4/TREM2/fAD/sAD 数据集 |
| **Fig 4** | 尸检脊髓 p53/DDR 激活 | **Fig 4: 死后脑组织 p53/DDR** | ROSMAP + MSBB RNA-seq |
| **Fig 5** | ALS iPSMN 可变剪接 | **Fig 5: iPSC Microglia 可变剪接** | MAJIQ/SUPPA2 剪接分析 |
| **Fig 6** | ALS iPSMN 体细胞突变 & 融合 | **Fig 6: iPSC Microglia 突变 & 融合** | GATK-RNAvar + STAR-Fusion |
| **Supp** | RRHO / PCA / GSEA Hallmark | **FigS1-S3** | 同上 |

---

## 2. 完整 Pipeline 流程

```
AD Multi-Omics Integrated Pipeline v3
Model: iPSC Microglia Cohorts + Post-mortem Brain Tissues Cohorts

================================================================================
STAGE 1: DATA ACQUISITION
================================================================================

  A. iPSC Microglia Datasets
     - GSE163857 (AD vs CTRL, bulk RNA)
     - GSE327711 (APOE4 vs CTRL)
     - NeuroLINCS Microglia (PLCG2/TREM2 variants)
     - Cell/Haas 2023 (snRNA-seq, states)
     - Nature/Timmer 2023 (brain organoid exposure)

  B. Post-mortem Brain Datasets
     - ROSMAP RNA-seq (syn3388564, DLPFC/AC/PCG)
     - MSBB Study (syn3159438, multi-region)
     - Mathys snRNA-seq (GSE138852, BA9/BA44)
     - Green snRNA-seq (syn31512863, 2.3M nuclei)
     - Wong 2024 (6-region atlas)

  C. Multi-Omics Layers (Scheidemantel 2026 framework)
     - Epigenomics: Klein ChIP-seq (syn4896408)
     - Proteomics: Johnson TMT (syn17015098)
     - Metabolomics: Batra et al. (syn26007830)
     - snRNA-seq: Green et al. (syn31512863)
     - Phenotype: ROSMAP (radc.rush.edu)

================================================================================
STAGE 2: QC & PREPROCESSING
================================================================================

  iPSC Microglia:                          Post-mortem Brain:
  - FastQC (quality)                        - FastQC + MultiQC
  - TrimGalore (adapter)                   - STAR (2-pass)
  - STAR alignment                          - RSEM/featureCounts
  - featureCounts/Salmon                    - Batch correction (ComBat-seq/sva)
  - PCA (batch detect)                      - Remove: low expressed genes

================================================================================
STAGE 3: DIFFERENTIAL EXPRESSION ANALYSIS
================================================================================

  DESeq2 (iPSC): condition + sex + dataset batch
  design: ~ condition + sex + dataset
  Run separately per dataset -> meta-analyze with Fisher p

  limma-voom (Post-mortem, large N):
  design: ~ condition + age + sex + pmi + batch

  Output:
  [Fig 2: Pan-AD DGE]   -> Volcano + Heatmap + GSEA + PROGENy + DoRoTHEa
  [Fig 3: Subgroup DGE] -> 4-volcano + corr + pathway comparison
  [Fig 4: PM Brain DGE] -> Volcano + GSEA + PROGENy + DoRoTHEa

================================================================================
STAGE 4: CROSS-SYSTEM INTEGRATION
================================================================================

  A. iPSC vs Post-mortem Concordance
     - RRHO (Rank-Rank Hypergeometric Overlap) heatmaps
     - Pearson/Spearman correlation of log2FC
     - Overlap DEG sets (Fisher's exact test)

  B. MOFA+ Multi-Omics Integration (Scheidemantel framework)
     - Input: RNA + proteomics + epigenomics + metabolomics
     - Factor analysis -> latent factors (AD-related)
     - Factor 8 = key disease unifier (p53/DDR/Microglia)

  C. Cell Type Deconvolution
     - MuSiC / CIBERSORTx (bulk -> cell fractions)
     - Validate with snRNA-seq reference (Mathys/Green)

================================================================================
STAGE 5: ALTERNATIVE SPLICING
================================================================================

  SUPPA2 / MAJIQ v2.4
  - deltaPSI > 0.1, P < 0.05 (per-event)
  - Event types: SE (Exon Skipping), RI (Intron Retention),
    A5SS, A3SS, MXE, AF, AL
  - Batch correction: dataset + sex
  Output: Fig 5A (splicing volcano) + 5B (pie) + 5C (violin)

================================================================================
STAGE 6: GENOMIC INSTABILITY
================================================================================

  nf-core/rnavar (GATK-based) + STAR-Fusion
  - SNV / Insertion / Deletion calling
  - Adjusted for: read depth, age, library type
  - GLM: mutation burden ~ condition + covariables
  Output: Fig 6A (violin) + 6B (forest) + 6C-D (fusions)

================================================================================
STAGE 7: COMPREHENSIVE REPORT
================================================================================

  R Markdown / Quarto -> Publication-ready figures
  - All figures with full captions + statistics
  - Interactive Shiny app for gene-level queries
  - Source data tracking (database annotations per figure)
```

---

## 3. 关键 R 代码模板

### 3.1 DESeq2 分析
```r
library(DESeq2)
dds <- DESeqDataSetFromMatrix(
  countData = counts_matrix,
  colData = col_data,
  design = ~ condition + sex + dataset
)
dds <- DESeq(dds)
res <- results(dds, contrast = c("condition", "AD", "CTRL"),
               lfcThreshold = 0.58, alpha = 0.05)
res <- lfcShrink(dds, coef = "condition_AD_vs_CTRL", type = "apeglm")
```

### 3.2 limma-voom（Post-mortem 大样本）
```r
library(limma)
library(edgeR)
dge <- DGEList(counts = counts_matrix)
dge <- calcNormFactors(dge, method = "TMM")
design <- model.matrix(~ condition + age + sex + pmi + batch, data = col_data)
v <- voom(dge, design, plot = FALSE)
fit <- lmFit(v, design)
fit <- eBayes(fit)
res_pm <- topTable(fit, coef = "conditionAD", n = Inf)
```

### 3.3 GSEA（fgsea）
```r
library(fgsea)
library(msigdbr)
# 获取 MSigDB Hallmark 基因集
msig <- msigdbr(species = "Homo sapiens", category = "H")
pathways <- split(msig$gene_symbol, msig$gs_name)
# 按 -log10(padj) * sign(log2FC) 排序
stats <- res$log2FoldChange
names(stats) <- rownames(res)
stats <- sort(stats, decreasing = TRUE)
gsea_res <- fgsea(pathways = pathways, stats = stats,
                  minSize = 15, maxSize = 500, nperm = 10000)
```

### 3.4 PROGENy + DoRoTHEa（decoupleR）
```r
library(decoupleR)
# PROGENy 信号通路活性
net <- get_progeny(organism = "human", top = 500)
acts <- run_wmean(mat = expr_matrix, net = net, .source = "source",
                  .target = "target", .mor = "weight", times = 100)
# DoRoTHEa TF 活性
net_tf <- get_dorothea(organism = "human", levels = c("A", "B"))
acts_tf <- run_ulm(mat = expr_matrix, net = net_tf, .source = "source",
                   .target = "target", .mor = "mor")
```

### 3.5 RRHO 排名一致性分析
```r
library(RRHO2)
# iPSC DGE rank list
iPSC_rank <- data.frame(
  gene = res_ipsc$gene,
  stat = -log10(res_ipsc$padj) * sign(res_ipsc$log2FC)
)
PM_rank <- data.frame(
  gene = res_pm$gene,
  stat = -log10(res_pm$adj.P.Val) * sign(res_pm$logFC)
)
RRHO2_obj <- RRHO2_initialize(iPSC_rank, PM_rank, labels = c("iPSC", "PM"))
RRHO2_heatmap(RRHO2_obj)
```

---

## 4. 真实数据替换指南

当从模拟数据切换到真实 ROSMAP 数据时，需替换以下关键对象：

```r
# === 输入数据替换 ===
# 1. iPSC Microglia DGE 结果
res_ipsc <- read.csv("path/to/iPSC_DESeq2_results.csv", row.names = 1)
# 必需列: gene, log2FoldChange, padj, baseMean

# 2. Post-mortem Brain DGE 结果
res_pm <- read.csv("path/to/ROSMAP_limma_results.csv", row.names = 1)
# 必需列: gene, logFC, adj.P.Val, t

# 3. 通路基因集（从 MSigDB 获取真实集合）
library(msigdbr)
AD_UP_GENES <- read.table("AD_up_genes.txt", header = FALSE)$V1
P53_DDR_GENES <- msigdbr(species = "Homo sapiens") |>
  filter(gs_name == "HALLMARK_P53_PATHWAY") |>
  pull(gene_symbol)

# 4. 替换样本元数据
meta_ipsc <- read.csv("iPSC_metadata.csv")
# 必需列: sample_id, condition (AD/CTRL), sex, dataset
meta_pm <- read.csv("ROSMAP_metadata.csv")
# 必需列: sample_id, condition, age_death, pmi, sex, Braak_stage
```

---

## 5. 数据库访问指南

### Synapse 数据访问
1. 注册 Synapse 账户：https://www.synapse.org/
2. 申请 ROSMAP 数据访问权限：radc.rush.edu
3. 使用 Python synapse 客户端下载：
```python
import synapseclient
syn = synapseclient.Synapse()
syn.login()
# 下载 ROSMAP RNA-seq 数据
entity = syn.get("syn3388564")
```

### GEO 数据下载
```bash
# 使用 GEOquery R 包
library(GEOquery)
gse <- getGEO("GSE163857", GSEMatrix = TRUE)
exprs(gse[[1]])  # 提取表达矩阵
```

---

## 6. 参考文献

1. **Ziff et al. 2023** — *Integrated transcriptome landscape of ALS identifies genome instability linked to TDP-43 pathology*, Nat Commun. https://doi.org/10.1038/s41467-023-37325-y
2. **Scheidemantel et al. 2026** — *Integration of aged brain multi-omics reveals cross-system mechanisms underlying Alzheimer's disease heterogeneity*, Cell Reports
3. **Mathys et al. 2019** — *Single-cell transcriptomic analysis of Alzheimer's disease*, Nature
4. **Green et al. 2023** — *Cellular communities reveal distinct cell-type-specific gene networks in Alzheimer's disease*, Nature Neuroscience
5. **Johnson et al. 2020** — *Large-scale proteomic analysis of Alzheimer's disease brain and cerebrospinal fluid reveals early changes in energy metabolism associated with microglia and astrocyte activity*, Nature Medicine
