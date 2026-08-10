# AD 多组学数据库清单（2016–2026，共 39 个）

> 按数据类型分类，标注年份、样本量、Synapse/GEO 访问号

---

## 一、iPSC 来源小胶质细胞（直接对应研究模型）

| # | 数据库 | 数据类型 | 细胞/脑区 | 样本量 | 年份 | 来源 | 访问链接 |
|---|---|---|---|---|---|---|---|
| 1 | **Cell/Haas et al. 2023** | RNA-seq / snRNA-seq | iPSC-derived microglia | AD vs CTRL | 2023 | Cell | DOI: 10.1016/j.cell.2023.09.001 |
| 2 | **Nature/Timmer et al. 2023** | RNA-seq | iPSC microglia + brain organoids | AD risk variants | 2023 | Nature | DOI: 10.1038/s41590-023-01558-2 |
| 3 | **GSE163857** | RNA-seq (bulk) | iPSC-derived microglia | AD vs CTRL | 2020 | GEO | https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE163857 |
| 4 | **GSE254205** | RNA-seq | iPSC microglia-like cells | Treatment response | 2024 | GEO | https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE254205 |
| 5 | **GSE327711** | RNA-seq | iPSC-derived microglia | APOE4 vs CTRL | 2024 | GEO | https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE327711 |
| 6 | **NeuroLINCS iPSC Microglia** | RNA-seq / proteomics | iPSC-derived microglia | ALS/CTRL（方法借鉴） | 2022 | LINCS | https://lincsproject.org |

---

## 二、死后脑组织 Bulk RNA-seq

| # | 数据库 | 数据类型 | 脑区 | 样本量 | 年份 | 访问 |
|---|---|---|---|---|---|---|
| 7 | **ROSMAP RNA-seq (Tasaki et al.)** | RNA-seq | DLPFC, AC, PCG | ~1,500 样本 | 2020 | Synapse: syn3388564 |
| 8 | **MSBB Study** | RNA-seq / proteomics / epigenomics | 多脑区（IFG, STG, PHG, TP, FP） | ~1,500 样本 | 2017-2023 | Synapse: syn3159438 |
| 9 | **Mayo Brain Bank RNA-seq** | RNA-seq | 颞叶皮层、小脑 | ~300 样本 | 2017 | Synapse: AMP-AD Portal |
| 10 | **Banner Sun Health Research** | RNA-seq | 颞叶皮层、前额叶皮层 | ~200 样本 | 2013-2017 | Synapse: AMP-AD Portal |
| 11 | **GSE48350** | RNA-seq | 颞叶皮层 | AD vs CTRL | 2016 | GEO: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE48350 |
| 12 | **AMP-AD Targets Consortium RNA-seq** | RNA-seq | 多脑区 | ~2,000 样本 | 2019-2022 | Synapse: adknowledgeportal.synapse.org |

---

## 三、单细胞/单核 RNA-seq（snRNA-seq / scRNA-seq）

| # | 数据库 | 数据类型 | 脑区 | 样本量 | 年份 | 访问 |
|---|---|---|---|---|---|---|
| 13 | **Mathys et al. 2019** ⭐ | snRNA-seq | 前额叶皮层 (BA9/BA44) | 48人 / 80,660核 | 2019 | GEO: GSE138852 |
| 14 | **Green et al. 2023** ⭐ | snRNA-seq | 前额叶皮层 | 427人 / 2.3M核 | 2023 | Synapse: syn31512863 |
| 15 | **Wong et al. 2024 (6-region atlas)** | snRNA-seq | 6个脑区 | 多区域 | 2024 | Nature: DOI 10.1038/s41586-024-07606-7 |
| 16 | **ADAD snRNA-seq (Nettles et al. 2023)** | snRNA-seq | 前额叶皮层 | 常染色体显性AD | 2023 | Synapse: syn31512863 |
| 17 | **ssREAD Database** | sc/snRNA-seq 汇总 | 多脑区 | 67项研究 / 730万核 | 2020-2024 | https://bmblx.bmi.osumc.edu/ssread |
| 18 | **scREAD Database** | scRNA-seq 汇总 | 多脑区 | 多项研究 | 2020 | https://pmc.ncbi.nlm.nih.gov/articles/PMC7674513 |
| 19 | **SCAD-Brain** | scRNA-seq 汇总 | 多脑区 | 多项研究 | 2023 | Frontiers: DOI 10.3389/fnagi.2023.1157792 |

---

## 四、表观遗传学（ChIP-seq / ATAC-seq / DNA 甲基化）

| # | 数据库 | 数据类型 | 脑区 | 样本量 | 年份 | 访问 |
|---|---|---|---|---|---|---|
| 20 | **Klein et al. ChIP-seq H3K9ac** ⭐ | ChIP-seq | 前额叶皮层 | AD vs CTRL | 2020 | Synapse: syn4896408 |
| 21 | **Marzi et al. 2018** | ChIP-seq H3K9ac/H3K27ac | 海马体 | AD vs CTRL | 2018 | GEO: GSE159699 |
| 22 | **GSE130746** | ChIP-seq (multi-histone) | 前额叶皮层 | AD vs CTRL | 2020 | GEO: GSE130746 |
| 23 | **Nhawvar et al. 2023** | CTCF ChIP-seq | 前额叶皮层 (BA46) | AD vs CTRL | 2023 | GEO: GSE196413 |
| 24 | **ROSMAP DNA Methylation (EWAS)** | DNA 甲基化 array | DLPFC | ~1,000 样本 | 2016-2022 | Synapse: syn31512863 |
| 25 | **MSBB ATAC-seq** | ATAC-seq | 多个脑区 | AD vs CTRL | 2021 | Synapse: AMP-AD Portal |

---

## 五、蛋白质组学（TMT / DIA）

| # | 数据库 | 数据类型 | 脑区 | 样本量 | 年份 | 访问 |
|---|---|---|---|---|---|---|
| 26 | **Johnson et al. 2020 TMT** ⭐ | TMT proteomics | DLPFC | ~1,000 样本 | 2020 | Synapse: syn17015098 |
| 27 | **Bai et al. 2020 (brain proteome)** | LC-MS/MS | 前额叶皮层 | 成人脑 proteome | 2020 | Nature Data: DOI 10.1038/s41597-020-00650-8 |
| 28 | **Wingo et al. 2024** | TMT proteomics | 多脑区 | ~3,000 样本 | 2024 | Wiley AD: DOI 10.1002/alz.14360 |
| 29 | **AMP-AD TMT Proteomics (Mayo/MSBB/ROS)** | TMT proteomics | 多脑区 | ~2,000 样本 | 2019-2022 | Synapse: AMP-AD Portal |
| 30 | **Synaptic Proteomics (Kumar et al. 2025)** | LC-MS/MS | 突触体 | AD vs CTRL | 2025 | Nature: DOI 10.1038/s41380-025-03095-w |

---

## 六、代谢组学

| # | 数据库 | 数据类型 | 脑区 | 样本量 | 年份 | 访问 |
|---|---|---|---|---|---|---|
| 31 | **Batra et al. 2020** ⭐ | Metabolomics (LC-MS) | 前额叶皮层、海马体 | AD vs CTRL | 2020 | Synapse: syn26007830 |
| 32 | **Vila-Costa et al. 2023** | Targeted metabolomics | 脑组织 + 血液 | AD vs CTRL | 2023 | DOI: 10.1016/j.neurobiolaging.2023.03.009 |
| 33 | **Brain Metabolome Atlas (NMR)** | Untargeted metabolomics | 颞叶皮层 | ~500 样本 | 2025 | Nature Trans Psychiatry: DOI 10.1038/s41398-025-03293-8 |

---

## 七、参考基因组 / 功能基因组

| # | 数据库 | 数据类型 | 样本量 | 年份 | 访问 |
|---|---|---|---|---|---|
| 34 | **Human Reference GRCh38** | Reference genome | - | 2024 | https://gencodegenes.org |
| 35 | **GTEx v8** | eQTL | ~17,000 样本 | 2020 | https://gtexportal.org |
| 36 | **PsychENCODE** | Multi-omics | ~2,000 样本 | 2018-2020 | https://psychencode.org |
| 37 | **BrainSpan** | RNA-seq (development) | 发育轨迹 | 2010-2018 | https://brainspan.org |

---

## 八、临床表型数据

| # | 数据库 | 数据类型 | 样本量 | 年份 | 访问 |
|---|---|---|---|---|---|
| 38 | **ROSMAP Phenotype Data** | Clinical / pathology | ~3,000 参与者 | 1994-2024 | https://www.radc.rush.edu |
| 39 | **NIAGADS (NIA Genetics)** | GWAS summary stats | ~100,000 AD cases | 2019-2024 | https://dss.niagads.org |

---

## 数据库优先级矩阵

| 目标图表 | 推荐数据组合 | 优先级 |
|---|---|:---:|
| **Fig 2A 火山图** | GSE163857 + GSE327711 + NeuroLINCS | ⭐⭐⭐ |
| **Fig 2C iPSC vs PM** | GSE163857 + ROSMAP RNA-seq (syn3388564) | ⭐⭐⭐⭐⭐ |
| **Fig 2D/E/F GSEA/PROGENy/DoRoTHEa** | 同 Fig 2A 数据 | ⭐⭐⭐ |
| **Fig 3 遗传亚组** | APOE4: GSE327711 + TREM2: NeuroLINCS + fAD: 文献 + sAD: ROSMAP | ⭐⭐⭐⭐ |
| **Fig 4 死后脑 p53** | ROSMAP RNA-seq + MSBB RNA-seq | ⭐⭐⭐⭐⭐ |
| **Fig 5 可变剪接** | iPSC RNA-seq → MAJIQ/SUPPA2 | ⭐⭐⭐ |
| **Fig 6 突变与融合** | iPSC RNA-seq → GATK-RNAvar + STAR-Fusion | ⭐⭐⭐ |
| **FigS1 RRHO** | iPSC DGE + ROSMAP DGE | ⭐⭐⭐⭐ |
| **MOFA 多组学** | syn3388564 + syn17015098 + syn4896408 + syn26007830 | ⭐⭐⭐⭐⭐ |

---

## 数据访问入口汇总

| 数据库 | 链接 |
|---|---|
| **GEO (NCBI)** | https://www.ncbi.nlm.nih.gov/geo/ |
| **Synapse (AMP-AD)** | https://adknowledgeportal.synapse.org/ |
| **ROSMAP RADC** | https://www.radc.rush.edu/ |
| **ssREAD** | https://bmblx.bmi.osumc.edu/ssread/ |
| **AMP-AD Portal** | https://adknowledgeportal.synapse.org/ |
| **NIAGADS** | https://dss.niagads.org/ |
| **GTEx Portal** | https://gtexportal.org/ |
| **ALS 参考框架 GitHub** | https://github.com/ojziff/als_genome_instability |
