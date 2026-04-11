# RNA-seq Pasilla Analysis — Reference-Based RNA-seq Pipeline in Galaxy

## Overview
This repository documents a complete reference-based RNA-seq data analysis pipeline performed in Galaxy, following the Galaxy Training Network (GTN) tutorial. The analysis investigates differential gene expression in *Drosophila melanogaster* following depletion of the **Pasilla (PS)** splicing regulatory gene via RNAi, based on Brooks et al. 2011.

---

## Biological Question
What genes and biological pathways are differentially expressed in *Drosophila melanogaster* following depletion of the Pasilla splicing regulator?

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

> Subsampled FASTQ files (~5MB) were used for Steps 00–03. Full count files from all 7 samples were used for Steps 04–05.

---

## Pipeline

```
Raw FASTQ reads
↓
Step 00: Data Upload and Collection Setup
↓
Step 01: Quality Control — Falco, MultiQC, Cutadapt
↓
Step 02: Mapping — RNA STAR, IGV, JBrowse2
↓
Step 03: Strandness Estimation and Read Counting — featureCounts
↓
Step 04: Differential Expression Analysis — DESeq2
↓
Step 05: Functional Enrichment — GO and KEGG via goseq and Pathview
```


---

## Tools Used

| Tool | Version | Purpose |
|---|---|---|
| Falco | 1.2.4+galaxy0 | Per-sample read QC |
| MultiQC | 1.27+galaxy4 | QC report aggregation |
| Cutadapt | 5.2+galaxy0 | Adapter trimming |
| RNA STAR | 2.7.11b+galaxy0 | Splice-aware mapping |
| IGV | Desktop | BAM and Sashimi visualization |
| JBrowse2 | 3.6.5+galaxy1 | In-browser genome visualization |
| pyGenomeTracks | 3.9+galaxy0 | Strand coverage plots |
| featureCounts | 2.1.1+galaxy0 | Read counting per gene |
| DESeq2 | 2.11.40.8+galaxy0 | Differential expression |
| heatmap2 | 3.2.0+galaxy1 | Expression heatmaps |
| goseq | 1.50.0+galaxy0 | GO and KEGG enrichment |
| Pathview | 1.34.0+galaxy0 | KEGG pathway visualization |

---

## Repository Structure

```
RNA-seq-Pasilla-Analysis/
├── README.md
├── steps/
│   ├── step_00_data_upload.md
│   ├── step_01_quality_control_Falco_MultiQC.md
│   ├── step_02_mapping_STAR.md
│   ├── step_03_strandness_and_read_counting.md
│   ├── step_04_DESeq2_differential_expression.md
│   └── step_05_functional_enrichment_GO_KEGG.md
├── images/
└── data/
```

---

## Step Documentation

| Step | File | Description |
|---|---|---|
| 00 | [step_00_data_upload.md](steps/step_00_data_upload.md) | FASTQ import and paired collection setup |
| 01 | [step_01_quality_control_Falco_MultiQC.md](steps/step_01_quality_control_Falco_MultiQC.md) | QC, aggregation, trimming |
| 02 | [step_02_mapping_STAR.md](steps/step_02_mapping_STAR.md) | Mapping, QC, genome visualization |
| 03 | [step_03_strandness_and_read_counting.md](steps/step_03_strandness_and_read_counting.md) | Strandness and read counting |
| 04 | [step_04_DESeq2_differential_expression.md](steps/step_04_DESeq2_differential_expression.md) | DE analysis, annotation, heatmaps |
| 05 | [step_05_functional_enrichment_GO_KEGG.md](steps/step_05_functional_enrichment_GO_KEGG.md) | GO and KEGG enrichment |

---

## Key Results

| Metric | Value |
|---|---|
| Unique mapping rate | ~80% per sample |
| Library strandness | Unstranded |
| Significant DE genes | 113 (adj p < 0.05, \|log2FC\| > 1) |
| Pasilla gene (FBgn0261552) | Downregulated in treated samples |
| PC1 variance | 48% — separates by treatment |
| PC2 variance | 33% — separates by sequencing type |
| Over-represented KEGG pathway | dme00010 Glycolysis/Gluconeogenesis |
| Under-represented KEGG pathway | dme03040 Spliceosome |

---

## Reproducibility

1. Create a Galaxy account at [usegalaxy.org](https://usegalaxy.org)
2. Create history `RNA-seq-Pasilla-Analysis`
3. Import subsampled FASTQ files from Zenodo record 6457007
4. Follow each step markdown in `steps/` in order
5. For Steps 04–05 create a new history `RNA-seq-Pasilla-DESeq2` and import the 7 pre-computed count files from the same Zenodo record

---

## References

- Brooks et al. 2011: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3032923/
- GTN Tutorial: https://training.galaxyproject.org/training-material/topics/transcriptomics/tutorials/ref-based/tutorial.html
- Dobin et al. 2013 — STAR: https://academic.oup.com/bioinformatics/article/29/1/15/272537
- Love et al. 2014 — DESeq2: https://genomebiology.biomedcentral.com/articles/10.1186/s13059-014-0550-8
- Liao et al. 2013 — featureCounts: https://academic.oup.com/bioinformatics/article/30/7/923/232889
- Young et al. 2010 — goseq: https://genomebiology.biomedcentral.com/articles/10.1186/gb-2010-11-2-r14
- Ewels et al. 2016 — MultiQC: https://academic.oup.com/bioinformatics/article/32/19/3047/2196507

---

## Author
**Maheen Ali**
Galaxy RNA-seq Pipeline — April 2026
Based on GTN Tutorial: Reference-based RNA-Seq data analysis
