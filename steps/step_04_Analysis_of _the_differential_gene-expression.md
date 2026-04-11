# Step 04: Differential Gene Expression Analysis using DESeq2

## Objective
Identify genes that are differentially expressed between Pasilla-depleted 
(treated) and control (untreated) Drosophila melanogaster samples using 
DESeq2, annotate the results, filter for significance, and visualize 
expression patterns using heatmaps.

---

## Biological Context

Raw read counts cannot be directly compared across samples due to 
differences in sequencing depth and library composition. DESeq2 addresses 
this by applying size factor normalization and modeling count data using 
a negative binomial distribution. It estimates biological variance from 
replicates and tests for statistically significant expression differences 
between conditions using the Wald test.

In this experiment two sources of variation exist:
- **Treatment** — the primary factor of interest (Pasilla RNAi depletion 
  vs untreated control)
- **Sequencing type** — a confounding factor (paired-end vs single-end) 
  that must be accounted for in the model

A multi-factor design was used to isolate the effect of treatment while 
controlling for sequencing type.

---

## Tools Used

| Tool | Version | Purpose |
|---|---|---|
| DESeq2 | 2.11.40.8+galaxy0 | Differential expression analysis |
| Annotate DESeq2 output | 1.1.0+galaxy1 | Add gene symbols and coordinates |
| Filter data | Galaxy built-in | Extract significant genes |
| Join two Datasets | Galaxy built-in | Merge normalized counts with DE genes |
| Cut columns | Galaxy built-in | Extract relevant columns |
| heatmap2 | 3.2.0+galaxy1 | Heatmap visualization |

---

## Part 1: Data Import

A new Galaxy history (`RNA-seq-Pasilla-DESeq2`) was created for this 
analysis. Seven pre-computed featureCounts count files were imported 
from Zenodo representing all biological replicates:

| Sample | Condition | Sequencing Type |
|---|---|---|
| GSM461176 | Untreated | Single-end |
| GSM461177 | Untreated | Paired-end |
| GSM461178 | Untreated | Paired-end |
| GSM461179 | Treated | Single-end |
| GSM461180 | Treated | Paired-end |
| GSM461181 | Treated | Paired-end |
| GSM461182 | Untreated | Single-end |

Source: https://zenodo.org/record/6457007

---

## Part 2: DESeq2 — Differential Expression Analysis

### Parameters

| Parameter | Value |
|---|---|
| How | Select datasets per level |
| Factor 1 name | Treatment |
| Factor 1 level 1 | treated (GSM461179, GSM461180, GSM461181) |
| Factor 1 level 2 | untreated (GSM461176, GSM461177, GSM461178, GSM461182) |
| Factor 2 name | Sequencing |
| Factor 2 level 1 | PE (GSM461177, GSM461178, GSM461180, GSM461181) |
| Factor 2 level 2 | SE (GSM461176, GSM461179, GSM461182) |
| Files have header | Yes |
| Choice of input data | Count data (featureCounts/HTSeq-count/StringTie) |
| Use beta priors | Yes |
| Output | Plots + Normalised counts |

### Outputs Generated

| Output | Saved as |
|---|---|
| DESeq2 result table | `data/DESeq2_results.tabular` |
| Normalised counts table | `data/DESeq2_normalized_counts.tabular` |
| Diagnostic plots (PDF) | `data/DESeq2_plots.pdf` |

---

## Part 3: DESeq2 Diagnostic Plots

All plots are available in `data/DESeq2_plots.pdf`.

### PCA Plot

**File:** `data/DESeq2_plots.pdf` — Page 1

**Observations:**
- PC1 explains **48% of variance** and separates samples primarily 
  by **treatment condition** — treated samples cluster on one side, 
  untreated on the other
- PC2 explains **33% of variance** and separates samples by 
  **sequencing type** — paired-end (PE) and single-end (SE) samples 
  separate along this axis
- Four distinct groups are visible: `PE:treated`, `PE:untreated`, 
  `SE:treated`, `SE:untreated`
- The multi-factor design correctly captured both sources of variation, 
  confirming the experimental design is appropriate

### Sample-to-Sample Distance Heatmap

**File:** `data/DESeq2_plots.pdf` — Page 2

**Observations:**
- Samples cluster by condition — untreated samples (GSM461176, 
  GSM461177, GSM461178, GSM461182) group together and treated 
  samples (GSM461179, GSM461180, GSM461181) group together
- Within-condition distances are smaller (darker blue) than 
  between-condition distances, confirming biological replicates 
  are consistent
- Distance scale ranges from 0 to ~25, indicating meaningful 
  separation between treated and untreated groups

### Dispersion Estimates

**File:** `data/DESeq2_plots.pdf` — Page 3

**Observations:**
- Gene-wise dispersion estimates (black points) are shrunk towards 
  the fitted trend line (red) to produce final estimates (blue)
- Dispersion decreases as mean normalized counts increase — typical 
  and expected behavior for RNA-seq count data
- A small number of outlier genes are not shrunk and flagged 
  accordingly — this is normal
- The plot confirms DESeq2 dispersion modeling performed as expected

### p-value Histogram

**File:** `data/DESeq2_plots.pdf` — Page 4

**Observations:**
- A large spike of low p-values near 0 is visible, indicating a 
  substantial number of truly differentially expressed genes between 
  treated and untreated conditions
- The remaining p-values are approximately uniformly distributed, 
  consistent with the null hypothesis for non-DE genes
- This distribution confirms the statistical model is well-calibrated

### MA Plot

**File:** `data/DESeq2_plots.pdf` — Page 5

**Observations:**
- X-axis represents mean normalized counts (log scale), Y-axis 
  represents log2 fold change
- Blue points represent genes with adjusted p-value < 0.1 — 
  significantly differentially expressed genes
- log2 fold changes range from approximately -4 to +2
- More genes appear downregulated (negative log2FC) than upregulated 
  in treated vs untreated samples
- Genes with low mean counts show higher variance in fold change, 
  which is expected and handled by DESeq2 shrinkage

---

## Part 4: Annotation of DESeq2 Results

The GTF annotation file was imported from Zenodo and used to annotate 
the DESeq2 result table with gene symbols, chromosomal coordinates, 
and feature information.

### Tool: Annotate DESeq2/DEXSeq output tables

| Parameter | Value |
|---|---|
| Tabular output of DESeq2 | DESeq2 result file |
| Input file type | DESeq2/edgeR/limma |
| Reference annotation | Drosophila_melanogaster.BDGP6.32.109_UCSC.gtf.gz |

A header row was added via Concatenate with the following columns:
GeneID | Base mean | log2(FC) | StdErr | Wald-Stats |
P-value | P-adj | Chromosome | Start | End | Strand |
Feature | Gene symbol

Output renamed to: `Annotated DESeq2 results`

**Downloaded as:** `data/DESeq2_annotated_results.tabular`

---

## Part 5: Filtering Differentially Expressed Genes

### Filter 1 — Adjusted p-value < 0.05

| Parameter | Value |
|---|---|
| Filter | Annotated DESeq2 results |
| Condition | `c7<0.05` |
| Header lines to skip | 1 |

Output renamed to: `Genes with significant adj p-value`

### Filter 2 — Absolute log2 Fold Change > 1

Applying abs(log2FC) > 1 corresponds to fold change > 2 or < 0.5.

| Parameter | Value |
|---|---|
| Filter | Genes with significant adj p-value |
| Condition | `abs(c3)>1` |
| Header lines to skip | 1 |

Output renamed to: `Genes with significant adj p-value & abs(log2(FC)) > 1`

**Result: 113 differentially expressed genes retained**

The Pasilla gene (**ps**, FBgn0261552) is present in the filtered 
list and is confirmed to be downregulated by the RNAi treatment 
(negative log2FC), consistent with the experimental design.

**Downloaded as:** `data/DESeq2_significant_genes_filtered.tabular`

---

## Part 6: Heatmap Visualization

### 6A — Normalized Counts Heatmap

Normalized counts for the 113 most differentially expressed genes 
were extracted by joining the DESeq2 normalized count table with 
the filtered gene list, then cutting columns c1-c8.

**heatmap2 Parameters:**

| Parameter | Value |
|---|---|
| Input | Normalized counts for most DE genes |
| Data transformation | Log2(value+1) |
| Enable data clustering | Yes |
| Labeling | Label columns not rows |
| Colormap | Gradient with 2 colors |

**File:** `data/heatmap_normalized_counts.pdf`

**Observations:**
- Samples cluster into two clear groups — treated 
  (GSM461179, GSM461180, GSM461181) and untreated 
  (GSM461176, GSM461177, GSM461178, GSM461182)
- Within each condition, paired-end and single-end samples 
  cluster together, reflecting the sequencing type effect 
  captured in the DESeq2 model
- Two major gene clusters are visible — genes with higher 
  expression in treated samples and genes with higher expression 
  in untreated samples
- The clustering confirms the treatment has a strong and 
  consistent effect on gene expression across all replicates

### 6B — Z-score Heatmap

**heatmap2 Parameters:**

| Parameter | Value |
|---|---|
| Input | Normalized counts for most DE genes |
| Data transformation | Plot the data as it is |
| Compute z-scores | Compute on rows |
| Enable data clustering | Yes |
| Labeling | Label columns not rows |
| Colormap | Gradient with 3 colors |

**File:** `data/heatmap_zscore.pdf`

**Observations:**
- Z-score normalization removes the effect of absolute expression 
  level and highlights relative differences across samples
- Treated samples (GSM461180, GSM461181, GSM461179) cluster 
  together on the left with consistent z-score patterns
- Untreated samples (GSM461177, GSM461178, GSM461182, GSM461176) 
  cluster on the right
- Red (high z-score) and blue (low z-score) blocks are clearly 
  separated between conditions, confirming robust differential 
  expression patterns
- The three-color gradient clearly distinguishes upregulated, 
  baseline, and downregulated genes across conditions

---

## Key Conclusions

| Finding | Conclusion |
|---|---|
| PC1 (48%) separates by treatment | Treatment is the dominant source of variation |
| PC2 (33%) separates by sequencing type | Multi-factor design was necessary and correct |
| Samples cluster by condition in heatmap | Biological replicates are consistent |
| 113 DE genes pass both filters | Pasilla depletion has significant transcriptomic effects |
| Pasilla gene (FBgn0261552) is downregulated | RNAi treatment successfully depleted Pasilla expression |
| MA plot shows mostly downregulation | PS gene depletion primarily suppresses gene expression |

---

## Repository Files

### data/ folder

| File | Description |
|---|---|
| `DESeq2_results.tabular` | Raw DESeq2 output table |
| `DESeq2_normalized_counts.tabular` | Normalized counts for all 7 samples |
| `DESeq2_plots.pdf` | All DESeq2 diagnostic plots (PCA, heatmap, dispersion, MA, p-value) |
| `DESeq2_annotated_results.tabular` | DESeq2 results annotated with gene symbols |
| `DESeq2_significant_genes_filtered.tabular` | 113 significant DE genes (adj p < 0.05, abs log2FC > 1) |
| `heatmap_normalized_counts.pdf` | Heatmap of normalized counts for 113 DE genes |
| `heatmap_zscore.pdf` | Z-score heatmap for 113 DE genes |

---

## Reproducibility Notes

- Galaxy History: `RNA-seq-Pasilla-DESeq2`
- DESeq2 version: 2.11.40.8+galaxy0
- heatmap2 version: 3.2.0+galaxy1
- Reference genome: dm6
- Annotation: Drosophila_melanogaster.BDGP6.32.109_UCSC.gtf.gz
- Beta prior: enabled (betaPrior=TRUE) for consistency with 
  DESeq2 versions prior to 1.16
- Significance thresholds: adjusted p-value < 0.05, 
  abs(log2FC) > 1
- Zenodo source: https://zenodo.org/record/6457007

