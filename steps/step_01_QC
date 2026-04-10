# Quality Control and Read Trimming Summary (Falco, MultiQC, Cutadapt)

## Overview
This section documents the quality control (QC), summarization, and read trimming steps performed on paired-end RNA-seq FASTQ files in Galaxy. The workflow includes initial QC using Falco, aggregation of QC reports using MultiQC, read trimming using Cutadapt, and post-trimming QC validation.

---

## 1. Initial Quality Control using Falco

### Tool Used
- Falco (Galaxy version 1.2.4+galaxy0)

### Input Data
Paired-end FASTQ dataset collection:
- GSM461177_untreat_paired_forward / reverse  
- GSM461180_treat_paired_forward / reverse  

### Processing Steps
- The paired dataset collection was flattened into individual FASTQ files
- Falco was executed on each FASTQ file to generate per-sample QC reports

### Generated Outputs (Stored in Repository)

| File Name | Repository Path | Description |
|------------|----------------|-------------|
| Falco_Forward_Webpage.zip | data/Falco_Forward_Webpage.zip | Falco QC HTML report for forward reads |
| Falco_Reverse_Webpage.zip | data/Falco_Reverse_Webpage.zip | Falco QC HTML report for reverse reads |

---

### QC Summary Statistics (Falco via MultiQC aggregation)

Source file: `data/multiqc_stats.tabular`

| Sample | % Duplicates | % GC | Avg Sequence Length | Median Sequence Length | % Fails | Total Sequences |
|--------|--------------|------|----------------------|--------------------------|----------|------------------|
| GSM461177_untreat_paired_forward | 23.60 | 55.0 | 37 | 37 | 9.09 | 1.057657 |
| GSM461177_untreat_paired_reverse | 25.17 | 55.0 | 37 | 37 | 18.18 | 1.057657 |
| GSM461180_treat_paired_forward | 23.74 | 56.0 | 37 | 37 | 18.18 | 1.226483 |
| GSM461180_treat_paired_reverse | 8.10 | 56.0 | 37 | 37 | 18.18 | 1.226483 |

### Interpretation
- All reads have consistent length (37 bp)
- Moderate duplication levels observed across samples
- GC content is stable (55–56%)
- Variation in % fails indicates differences in initial read quality across samples

---

## 2. MultiQC Aggregation (Pre-Trimming QC)

### Tool Used
- MultiQC (Galaxy version 1.27+galaxy4)

### Input
- Falco-generated QC reports

### Generated Outputs

| File Name | Repository Path | Description |
|------------|----------------|-------------|
| multiqc_webpage.zip | data/multiqc_webpage.zip | Aggregated Falco QC report (HTML) |
| multiqc_stats.tabular | data/multiqc_stats.tabular | Combined QC statistics table |

### Purpose
MultiQC was used to aggregate individual Falco QC reports into a single unified report for easier comparison across samples.

---

## 3. Read Trimming using Cutadapt

### Tool Used
- Cutadapt (Galaxy version 5.2+galaxy0)

### Input Data
- Paired-end FASTQ dataset collection

### Parameters Used
- Paired-end mode: enabled  
- Quality cutoff (R1): 20  
- Minimum read length: 20  
- Adapter/quality trimming enabled  
- Cutadapt report generation enabled (for MultiQC compatibility)

### Output
- Trimmed paired-end FASTQ files
- Cutadapt trimming reports

---

## 4. Post-Trimming Quality Control (MultiQC)

### Tool Used
- MultiQC (Galaxy version 1.27+galaxy4)

### Input
- Cutadapt report files

### Generated Outputs

| File Name | Repository Path | Description |
|------------|----------------|-------------|
| multiqc_stats_final.tabular | data/multiqc_stats_final.tabular | Post-trimming summary statistics |
| multiqc_webpage_final.zip | data/multiqc_webpage_final.zip | Aggregated post-trimming QC report |

---

### Trimming Summary Results

| Sample | % Reads Trimmed |
|--------|------------------|
| GSM461177_untreat_paired | 2.49 |
| GSM461180_treat_paired | 12.39 |

### Interpretation
- The treated sample (GSM461180) required significantly more trimming, indicating lower initial read quality
- The control sample (GSM461177) showed minimal trimming, indicating higher overall read quality
- Trimming successfully removed low-quality bases while preserving most reads for downstream analysis

---

## 5. Workflow Summary

1. Raw paired-end FASTQ files imported into Galaxy  
2. Paired dataset collection flattened for tool compatibility  
3. Initial QC performed using Falco  
4. QC results aggregated using MultiQC  
5. Reads trimmed using Cutadapt (quality + length filtering)  
6. Post-trimming QC performed using MultiQC  



---
