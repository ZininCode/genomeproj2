🧬 HG002 Genome Analysis – Preprocessing & Rare Variant Profiling

This repository documents the initial stages of a full genome analysis pipeline applied to the **HG002 Genome in a Bottle sample**.  
The focus so far has been on **VCF preprocessing, quality control, and rare variant summaries**.

---

## 📂 Project Structure
```
project/
├── data/
│   ├── raw/          # original HG002 VCF
│   ├── preproc/      # filtered & normalized VCFs
│   └── slices/       # SNP-only, per-chromosome, and test subsets
├── results/
│   ├── qc_stats_*.txt       # bcftools QC reports
│   ├── variant_summary_*.tsv
│   ├── per_chrom_snp_summary.tsv
│   └── summaries/           # rare variant counts (per chr, impact, gene)
└── R/               # RStudio scripts and plots
```

---

## 🔹 Step 0 — Preparation & Setup
- Installed **bcftools, samtools, tabix, VEP**, and **R + RStudio** with packages (`vcfR`, `VariantAnnotation`, `dplyr`, `ggplot2`).
- Downloaded:
  - **HG002 GRCh38 VCF** (raw data)  
  - **GRCh38 reference genome** (`.fa + .fai`)  
- Created organized project structure for raw data, preprocessing, results, and R scripts.

---

## 🔹 Step 1 — Variant Preprocessing
Pipeline run inside WSL:

1. **Filter to primary contigs** → remove alt haplotypes  
   → `HG002.primary.GRCh38.vcf.gz`
2. **Normalize & split multiallelics** against GRCh38 reference  
   → `HG002.norm.split.GRCh38.vcf.gz`
3. **QC filter (PASS + QUAL≥30)**  
   → `HG002.qc1.passq30.GRCh38.vcf.gz`  
   - Stats: `qc_stats_passq30.txt`  
   - Summary TSV: `variant_summary_passq30.tsv`
4. **Export smaller VCFs**  
   - SNP-only  
   - Per-chromosome SNPs  
   - 50k test subset
5. **Per-chromosome SNP summary** (counts + Ts/Tv ratio)  
   → `per_chrom_snp_summary.tsv` (copied to Windows for RStudio plotting)
6. **Genotype-level QC filter** (GT present, DP≥10, GQ≥20)  
   → `HG002.qc2.genofilt.GRCh38.vcf.gz`  
   - Stats: `qc_stats_q30_dp10_gq20.txt`  
   - Summary TSV: `variant_summary_q30_dp10_gq20.tsv`

---

## 🔹 Step 2 — Rare Variant Profile (in progress)
- Generated **rare variant summaries** from VEP output (`rare_all.tsv`):  
  - Per chromosome → `rare_counts_per_chr.tsv`  
  - By impact → `rare_counts_by_impact.tsv`  
  - By gene symbol → `rare_counts_by_gene.tsv`
- These TSVs were imported into **RStudio** for visualization.  
- ✅ **Final plots are already generated in RStudio** (rare variant distribution by chromosome, impact categories, and top genes).  
- Next steps:  
  - Complete full **VEP annotation** of HG002 QC-filtered VCF.  
  - Parse `CSQ` field in R.  
  - Apply rare coding + population AF filters.  
  - Export master rare variant table.

---

## 📊 Outputs Produced
- **TSV reports**:  
  - Variant statistics (before/after QC)  
  - Per-chromosome SNP summary  
  - Rare variant counts (chr, impact, gene)  
- **Plots in RStudio**:  
  - Rare variants per chromosome  
  - Distribution by impact (HIGH, MODERATE, LOW)  
  - Top genes with rare variants  
