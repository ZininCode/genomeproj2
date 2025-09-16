# Step 1 — Variant Preprocessing (Final Summary)

**Goal:** Clean and prepare HG002 VCF into an *analysis-ready callset* for annotation and downstream analysis.  
This step ensures that the input genome data is normalized, quality-filtered, and documented with QC metrics before moving into functional annotation (Step 2).  

---

## 1.0 — Filter to primary assembly contigs
- Removed random/decoy contigs; kept only chromosomes 1–22, X, Y, MT.  
- **Output:**  
  `data/preproc/HG002.primary.GRCh38.vcf.gz` (+ `.tbi`)

**Why?** → Decoy contigs often contain low-quality or redundant calls. Keeping only canonical chromosomes simplifies downstream tools (e.g., VEP, PLINK).  

---

## 1.1 — Normalize & split multiallelics
- Left-aligned indels, split multiallelic sites into biallelics.  
- **Output:**  
  `data/preproc/HG002.norm.split.GRCh38.vcf.gz` (+ `.tbi`)

**Why?** → Most annotation tools require normalized, biallelic variants for consistent interpretation.  

---

## 1.2 — First-pass site-level QC (PASS + QUAL ≥ 30)
- Kept only sites that passed variant caller filters and had high site quality.  
- **Output VCF:**  
  `data/preproc/HG002.qc1.passq30.GRCh38.vcf.gz` (+ `.tbi`)  
- **Raw QC stats:**  
  `results/qc_stats_passq30.txt`

**Why?** → Removes obvious false positives before heavier downstream analyses. QUAL≥30 is a standard high-confidence threshold.  

---

## 1.3 — Compact QC summary (TSV)
- Extracted key counts from `bcftools stats`.  
- **Output:**  
  `results/variant_summary_passq30.tsv`  

Includes:  
- total_records  
- snps, indels, mnps, multiallelic_sites  
- transitions, transversions, Ti/Tv ratio  

**Why?** → Provides a quick at-a-glance overview for reporting and QC checks.  

---

## 1.4 — Small exports for Windows RStudio
Created manageable slices of the callset for testing/teaching.  
- **SNP-only VCF:**  
  `data/slices/HG002.passq30.SNPs.vcf.gz` (+ `.tbi`)  
- **chr1 SNPs:**  
  `data/slices/HG002.passq30.SNPs.chr1.vcf.gz` (+ `.tbi`)  
- **50k SNP test subset (with header):**  
  `data/slices/HG002.passq30.SNPs.first50k.withhdr.vcf.gz` (+ `.tbi`)  

**Why?** → Full genome files are slow to handle in R on Windows. These small exports allow faster prototyping and visualization.  

---

## 1.5 — Per-chromosome SNP summary (hybrid WSL → RStudio)
- Counted SNPs, transitions, transversions per chromosome; computed Ti/Tv ratios.  
- **Output:**  
  `results/per_chrom_snp_summary.tsv`  
- Copied to Windows:  
  `results/per_chrom_snp_summary.tsv`  

**Why?** → Summarizing SNPs per chromosome in WSL avoids the overhead of loading full VCFs into R. Useful for quick genome-wide QC plots.  

---

## 1.6 — Genotype-level QC filter (per-sample DP/GQ)
- Ensured HG002 has reliable calls at each site:  
  `GT != mis && DP ≥ 10 && GQ ≥ 20`  
- **Output VCF:**  
  `data/preproc/HG002.qc2.genofilt.GRCh38.vcf.gz` (+ `.tbi`)  
- **Raw QC stats:**  
  `results/qc_stats_q30_dp10_gq20.txt`

**Why?** → Even if a site is high quality, the *sample’s genotype* may be low confidence. This filter ensures downstream rare-variant and clinical scans are based on trustworthy calls.  

---

## 1.7 — Compact QC summary (after genotype filter)
- Extracted same metrics as 1.3, but after genotype filter.  
- **Output:**  
  `results/variant_summary_q30_dp10_gq20.tsv`  

### Comparison (before → after):  
- **Total records:** 4.95M → 4.83M (−2.3%)  
- **SNPs:** 3.91M → 3.84M (−1.6%)  
- **Indels:** 1.04M → 0.99M (−4.9%)  
- **Ti/Tv ratio:** ~2.0 both → stable (expected genome-wide QC benchmark).  

**Why?** → Shows that ~2% of variants were removed for low per-sample quality, without distorting key QC metrics.  

---

# 📊 Final Deliverables from Step 1

### VCFs (for downstream analysis)
- `HG002.qc2.genofilt.GRCh38.vcf.gz` (+ `.tbi`) → **analysis-ready callset**  

### QC Results (for reporting)
- `results/variant_summary_passq30.tsv`  
- `results/variant_summary_q30_dp10_gq20.tsv`  
- `results/per_chrom_snp_summary.tsv`  
- `results/qc_stats_passq30.txt` (full bcftools stats)  
- `results/qc_stats_q30_dp10_gq20.txt` (full bcftools stats after genotype filter)  

### Key takeaways
- Total: ~4.8M high-confidence variants in HG002.  
- SNPs: ~3.8M; Indels: ~1.0M.  
- Genome-wide Ti/Tv ratio ≈ **2.0**, consistent with high-quality callsets.  
- Genotype-level filtering removed ~2% low-quality variants, improving reliability for downstream steps.  

---

# Next Steps

Proceed to **Step 2: Annotation with Ensembl VEP** using the final cleaned callset:  

`data/preproc/HG002.qc2.genofilt.GRCh38.vcf.gz`  

This file will serve as the input for rare variant profiling, clinical database scans, pharmacogenomics, and LoF burden analysis.  
