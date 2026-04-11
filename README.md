# RNA-seq Pasilla Analysis — Reference-Based RNA-seq Pipeline in Galaxy

## Overview

This repository documents a complete reference-based RNA-seq data 
analysis pipeline performed in Galaxy, following the Galaxy Training 
Network (GTN) tutorial by Batut et al. The analysis investigates 
differential gene expression in *Drosophila melanogaster* cells 
following depletion of the **Pasilla (PS)** splicing regulatory gene 
via RNA interference (RNAi).

The dataset is derived from Brooks et al. 2011, which identified genes 
and pathways regulated by the Pasilla gene — the Drosophila homologue 
of the mammalian Nova-1 and Nova-2 splicing regulators.

---

## Biological Question

**What genes and biological pathways are differentially expressed in 
*Drosophila melanogaster* following depletion of the Pasilla splicing 
regulator gene?**

---

## Dataset

| Sample | Condition | Sequencing Type |
|---|---|---|
| GSM461176 | Untreated | Single-end |
| GSM461177 | Untreated | Paired-end |
| GSM461178 | Untreated | Paired-end |
| GSM461179 | Treated (PS depleted) | Single-end |
| GSM461180 | Treated (PS depleted) | Paired-end |
| GSM461181 | Treated (PS depleted) | Paired-end |
| GSM461182 | Untreated | Single-end |

- **Organism:** *Drosophila melanogaster*
- **Reference genome:** dm6 (UCSC)
- **Annotation:** Ensembl BDGP6.32.109
- **Data source:** [Zenodo record 6457007](https://zenodo.org/record/6457007)
- **Original study:** [Brooks et al. 2011](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3032923/)

> **Note:** Subsampled FASTQ files (~5MB each) were used for Steps 
> 00–03 to demonstrate the QC and mapping workflow efficiently. 
> Full count files from all 7 samples were used for Steps 04–05.

---

## Pipeline Summary
Raw FASTQ reads
↓
Step 00: Data Upload & Collection Setup
↓
Step 01: Quality Control (Falco + MultiQC + Cutadapt)
↓
Step 02: Reference-Based Mapping (RNA STAR + MultiQC)
└── Mapping Inspection (IGV + JBrowse2)
↓
Step 03: Strandness Estimation + Read Counting (featureCounts)
↓
Step 04: Differential Expression Analysis (DESeq2)
└── Annotation + Filtering + Heatmap Visualization
↓
Step 05: Functional Enrichment Analysis (GO + KEGG via goseq + Pathview)

---

## Tools Used

| Tool | Version | Purpose |
|---|---|---|
| Falco | 1.2.4+galaxy0 | Per-sample read quality control |
| MultiQC | 1.27+galaxy4 | Aggregation of QC reports |
| Cutadapt | 5.2+galaxy0 | Adapter trimming and quality filtering |
| RNA STAR | 2.7.11b+galaxy0 | Splice-aware read mapping to dm6 |
| IGV | Desktop | BAM visualization and Sashimi plots |
| JBrowse2 | 3.6.5+galaxy1 | In-browser genome and BAM visualization |
| pyGenomeTracks | 3.9+galaxy0 | Strand-specific coverage visualization |
| featureCounts | 2.1.1+galaxy0 | Read counting per annotated gene |
| DESeq2 | 2.11.40.8+galaxy0 | Differential expression analysis |
| heatmap2 | 3.2.0+galaxy1 | Expression heatmap visualization |
| goseq | 1.50.0+galaxy0 | GO and KEGG enrichment analysis |
| Pathview | 1.34.0+galaxy0 | KEGG pathway visualization |

---

## Repository Structure
RNA-seq-Pasilla-Analysis/
│
├── README.md
│
├── steps/
│   ├── step_00_data_upload.md
│   ├── step_01_quality_control_Falco_MultiQC.md
│   ├── step_02_mapping_STAR.md
│   ├── step_03_strandness_and_read_counting.md
│   ├── step_04_DESeq2_differential_expression.md
│   └── step_05_functional_enrichment_GO_KEGG.md
│
├── images/
│   ├── Step00_history_files_green.png
│   ├── Step00_collection_created.png
│   ├── Falco_Forward_HTML.png
│   ├── Falco_Reverse_Html.png
│   ├── Step02_MultiQC_STAR_mapping_stats.png
│   ├── Step02_IGV_chr4_mapping.png
│   ├── Step02_IGV_Sashimi_plot.png
│   ├── Step02_JBrowse2_chr4_mapping.png
│   ├── Step03_IGV_strandness_chr3R.png
│   ├── Step03_JBrowse2_strandness_chr3R.png
│   ├── Step03_pyGenomeTracks_strand_coverage_chr4.png
│   ├── Step03_MultiQC_STAR_genecounts.png
│   ├── Step03_MultiQC_featureCounts_summary.png
│   ├── pathview_dme00010_glycolysis.png
│   └── pathview_dme03040_spliceosome.png
│
└── data/
├── multiqc_report.zip
├── multiqc_report.tabular
├── multiqc_cutadapt_report.zip
├── multiqc_cutadapt_report.tabular
├── multiqc_star_report.zip
├── multiqc_star_report.tabular
├── multiqc_strandness_report.zip
├── multiqc_strandness_report.tabular
├── multiqc_featurecounts_report.zip
├── multiqc_featurecounts_report.tabular
├── featurecounts_counts.tabular
├── DESeq2_results.tabular
├── DESeq2_normalized_counts.tabular
├── DESeq2_plots.pdf
├── DESeq2_annotated_results.tabular
├── DESeq2_significant_genes_filtered.tabular
├── heatmap_normalized_counts.pdf
├── heatmap_zscore.pdf
├── goseq_GO_results.tabular
├── goseq_GO_top10_terms.pdf
├── goseq_GO_DE_genes_per_category.tabular
├── goseq_KEGG_results.tabular
└── goseq_KEGG_DE_genes_per_pathway.tabular

---

## Step-by-Step Documentation

| Step | File | Description |
|---|---|---|
| 00 | [step_00_data_upload.md](steps/step_00_data_upload.md) | Import FASTQ files and create paired collection |
| 01 | [step_01_quality_control_Falco_MultiQC.md](steps/step_01_quality_control_Falco_MultiQC.md) | QC with Falco, MultiQC aggregation, Cutadapt trimming |
| 02 | [step_02_mapping_STAR.md](steps/step_02_mapping_STAR.md) | Splice-aware mapping, MultiQC, IGV and JBrowse2 inspection |
| 03 | [step_03_strandness_and_read_counting.md](steps/step_03_strandness_and_read_counting.md) | Strandness estimation and featureCounts read counting |
| 04 | [step_04_DESeq2_differential_expression.md](steps/step_04_DESeq2_differential_expression.md) | DESeq2 analysis, annotation, filtering, heatmaps |
| 05 | [step_05_functional_enrichment_GO_KEGG.md](steps/step_05_functional_enrichment_GO_KEGG.md) | GO enrichment, KEGG pathway analysis, Pathview |

---

## Key Results

| Finding | Value |
|---|---|
| Mapping rate (STAR) | ~80% uniquely mapped reads per sample |
| Library strandness | Unstranded (confirmed by 4 methods) |
| Significantly DE genes (adj p < 0.05, \|log2FC\| > 1) | 113 genes |
| Pasilla gene (FBgn0261552) | Confirmed downregulated in treated samples |
| PC1 variance explained | 48% — separates by treatment condition |
| PC2 variance explained | 33% — separates by sequencing type |
| Over-represented KEGG pathway | dme00010 — Glycolysis/Gluconeogenesis |
| Under-represented KEGG pathway | dme03040 — Spliceosome |

---

## How to Reproduce

1. Create a Galaxy account at [usegalaxy.org](https://usegalaxy.org)
2. Create a new history named `RNA-seq-Pasilla-Analysis`
3. Import subsampled FASTQ files from Zenodo record 6457007
4. Follow each step markdown file in the `steps/` folder in order
5. For Steps 04–05, import the 7 pre-computed count files from 
   the same Zenodo record into a new history named 
   `RNA-seq-Pasilla-DESeq2`

All tool versions, parameters, and expected outputs are documented 
in each step file.

---

## Galaxy Histories

| History Name | Contents |
|---|---|
| `RNA-seq-Pasilla-Analysis` | Steps 00–03 (QC, mapping, counting) |
| `RNA-seq-Pasilla-DESeq2` | Steps 04–05 (DESeq2, GO, KEGG) |

---

## References

- Brooks et al. 2011 — Original Pasilla dataset:
  https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3032923/
- Batut et al. 2018 — Galaxy Training Network:
  https://doi.org/10.1016/j.cels.2018.05.012
- GTN Tutorial — Reference-based RNA-seq:
  https://training.galaxyproject.org/training-material/topics/transcriptomics/tutorials/ref-based/tutorial.html
- Dobin et al. 2013 — STAR:
  https://academic.oup.com/bioinformatics/article/29/1/15/272537
- Love et al. 2014 — DESeq2:
  https://genomebiology.biomedcentral.com/articles/10.1186/s13059-014-0550-8
- Liao et al. 2013 — featureCounts:
  https://academic.oup.com/bioinformatics/article/30/7/923/232889
- Young et al. 2010 — goseq:
  https://genomebiology.biomedcentral.com/articles/10.1186/gb-2010-11-2-r14
- Ewels et al. 2016 — MultiQC:
  https://academic.oup.com/bioinformatics/article/32/19/3047/2196507

---

## Author

**Maheen Ali**
Galaxy RNA-seq Pipeline — April 2026
Based on GTN Tutorial: Reference-based RNA-Seq data analysis
