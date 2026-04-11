# Step 02: Reference-Based Mapping using RNA STAR

## Objective
Map quality-trimmed paired-end RNA-seq reads to the Drosophila 
melanogaster reference genome (dm6) using the splice-aware aligner 
STAR, and assess mapping quality using MultiQC.

---

## Biological Context
Eukaryotic RNA-seq reads originate from processed mRNAs that have 
had introns removed via splicing. Standard DNA aligners cannot handle 
reads that span splice junctions. STAR (Spliced Transcripts Alignment 
to a Reference) is a splice-aware aligner that can identify reads 
mapping entirely within exons as well as reads spanning two or more 
exons across intron boundaries. This makes it the appropriate choice 
for eukaryotic RNA-seq data.

---

## Tools Used

| Tool | Version | Purpose |
|---|---|---|
| RNA STAR | 2.7.11b+galaxy0 | Spliced alignment to dm6 genome |
| MultiQC | 1.27+galaxy4 | Aggregation of STAR mapping logs |

---

## Part 1: Import Gene Annotation File

### What and Why
A GTF (Gene Transfer Format) annotation file defines the coordinates 
of known genes, transcripts, and exons on the reference genome. STAR 
uses this to identify splice junction sites during alignment. It is 
critical that the annotation version matches the reference genome 
version (dm6 here).

### Input

| File | Source |
|---|---|
| `Drosophila_melanogaster.BDGP6.32.109_UCSC.gtf.gz` | Zenodo |

### Steps Performed
- Imported GTF file from Zenodo into Galaxy history
- Verified datatype is `gtf.gz` and not `gff`
- Renamed dataset to `Drosophila_melanogaster.BDGP6.32.109_UCSC.gtf.gz`

> This annotation was downloaded from Ensembl and adapted for 
> compatibility with the dm6 UCSC genome build installed on Galaxy.

---

## Part 2: Spliced Mapping with RNA STAR

### Parameters

| Parameter | Value |
|---|---|
| Single-end or paired-end reads | Paired-end (as collection) |
| RNA-Seq FASTQ/FASTA paired reads | Cutadapt on collection N: Reads |
| Custom or built-in reference genome | Use a built-in index |
| Reference genome with annotation | use genome reference without builtin gene-model but provide a gtf |
| Select reference genome | Fly (Drosophila melanogaster): dm6 Full |
| Gene model GTF file | Drosophila_melanogaster.BDGP6.32.109_UCSC.gtf.gz |
| Length of genomic sequence around annotated junctions | 36 (read length - 1) |
| Per gene/transcript output | Per gene read counts (GeneCounts) |
| Compute coverage | Yes in bedgraph format |

### Outputs Generated

| Output | Description |
|---|---|
| `RNA STAR on collection N: mapped.bam` | Aligned reads in BAM format |
| `RNA STAR on collection N: log` | Mapping statistics log |
| `RNA STAR on collection N: reads per gene` | Read counts per gene |
| `RNA STAR on collection N: Coverage Uniquely mapped strand 1` | Bedgraph strand 1 |
| `RNA STAR on collection N: Coverage Uniquely mapped strand 2` | Bedgraph strand 2 |

---

## Part 3: Mapping Quality Assessment with MultiQC

### Parameters

| Parameter | Value |
|---|---|
| Which tool was used generate logs | STAR |
| Type of STAR output | Log |
| STAR log output | RNA STAR on collection N: log |


| Metric | GSM461177 (Untreated) | GSM461180 (Treated) |
|---|---|---|
| Uniquely mapped reads | ~80% | ~80% |
| Reads mapped to multiple loci | < 10% | < 10% |
| Unmapped reads | remainder | remainder |

> **Interpretation:** ~80% unique mapping rate for both samples is 
> acceptable. Values below 70% would require investigation for 
> potential contamination or quality issues. The low multi-mapping 
> rate (< 10%) is within the normal range for Illumina short-read 
> sequencing data.

### Data Files

| File | Location in repo |
|---|---|
| MultiQC STAR HTML report | `data/multiqc_star_report.html` |
| MultiQC STAR tabular summary | `data/multiqc_star_report.tabular` |

---

## Part 4: Mapping Inspection

### 4A — IGV Visualization

**Region inspected:** `chr4:540,000-560,000`

![IGV chr4 mapping view](Step02_IGV_chr4_mapping.png)

**Observations:**
- Grey coverage peaks confirm reads map to known expressed regions 
  on chromosome 4
- Arcs represent splice junctions where reads span across introns, 
  confirming successful spliced alignment by STAR
- Annotated genes visible: `zfh2`, `Thd1`, `lncRNA:CR43958`, `Pur-alpha`

---

### 4B — Sashimi Plot (IGV)

**Region inspected:** `chr4:540,000-560,000`

![IGV Sashimi plot showing splice junctions](Step02_IGV_Sashimi_plot.png)

**Observations:**
- Red vertical bars represent per-position read depth
- Numbered arcs represent splice junctions — numbers indicate how 
  many reads span each junction
- Stacked blue boxes at the bottom represent distinct transcript 
  isoforms, confirming alternative splicing events in this region
- Junction counts are low but present, consistent with subsampled data

---

### 4C — JBrowse2 Visualization

**Region inspected:** `chr4:540,000-560,000`

**JBrowse2 Parameters:**

| Parameter | Value |
|---|---|
| Action | New JBrowse Instance |
| Reference genome | Fly (Drosophila melanogaster): dm6 Full |
| Default region | chr4:540000..560000 |
| Track 1 Label | BAM |
| Track 1 Type | BAM Pileups |
| Track 1 Data | RNA STAR on collection N: mapped.bam |
| Track 2 Label | Genes |
| Track 2 Type | GFF/GFF3/BED Features |
| Track 2 Data | Drosophila_melanogaster.BDGP6.32.109_UCSC.gtf.gz |

![JBrowse2 chr4 view showing both samples and gene annotations](Step02_JBrowse2_chr4_mapping.png)

**Observations:**
- Both `GSM461180_treat_paired` and `GSM461177_untreat_paired` 
  are displayed as separate tracks
- Coverage peaks and splice junction arcs are visible in both samples
- Gene annotation track confirms alignment to known Flybase transcripts:
  `FBti0064041-RA`, `FBti0064064-RA`, `FBti0064066-RA`, 
  `FBti0064068-RA`, `lncRNA:CR43958`
- Expression patterns appear visually consistent between conditions 
  at this locus

> **Note:** JBrowse2 built-in genome option produced a server-side 
> FileNotFoundError (dm6.fasta missing from Galaxy /corral4 storage). 
> This is a known Galaxy server issue. The tool was rerun successfully 
> using dm6.fa.gz imported from UCSC as the reference genome.

---

## Key Conclusions

| Observation | Conclusion |
|---|---|
| ~80% unique mapping rate | Mapping quality is acceptable |
| Splice junctions detected in IGV and JBrowse2 | STAR correctly performed spliced alignment |
| Reads map to known gene regions | No major contamination or quality issues |
| Both samples show consistent coverage patterns | Data suitable for downstream counting |

---

## Reproducibility Notes

- Galaxy History: `RNA-seq-Pasilla-Analysis`
- STAR version: 2.7.11b+galaxy0
- MultiQC version: 1.27+galaxy4
- JBrowse2 version: 3.6.5+galaxy1
- Reference genome: dm6 (Drosophila melanogaster, UCSC)
- Annotation: Drosophila_melanogaster.BDGP6.32.109_UCSC.gtf.gz
- Zenodo source: https://zenodo.org/record/6457007

---

## References

- Dobin et al. 2013 — STAR aligner: 
  https://academic.oup.com/bioinformatics/article/29/1/15/272537
- Ewels et al. 2016 — MultiQC: 
  https://academic.oup.com/bioinformatics/article/32/19/3047/2196507
- Robinson et al. 2011 — IGV: 
  https://www.nature.com/articles/nbt.1754
