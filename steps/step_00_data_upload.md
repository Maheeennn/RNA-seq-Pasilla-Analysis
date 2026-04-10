# Step 00: Data Upload and Paired Collection Setup

## Objective
Import raw paired-end RNA-seq FASTQ files into Galaxy and organize them
into a paired collection for downstream processing.

## Biological Context
This dataset is from Brooks et al. 2011, profiling Drosophila melanogaster
cells after depletion of the Pasilla splicing regulator gene via RNAi.
Two samples are used: one untreated (GSM461177) and one treated (GSM461180).

## Tool
Galaxy Upload Tool (Paste/Fetch Data)

## Inputs

| File | Sample | Condition | Type |
|---|---|---|---|
| GSM461177_1_subsampled.fastqsanger | GSM461177 | Untreated | Forward |
| GSM461177_2_subsampled.fastqsanger | GSM461177 | Untreated | Reverse |
| GSM461180_1_subsampled.fastqsanger | GSM461180 | Treated | Forward |
| GSM461180_2_subsampled.fastqsanger | GSM461180 | Treated | Reverse |

Source: https://zenodo.org/record/6457007

## Parameters
- Datatype: `fastqsanger`
- Collection type: List of Dataset Pairs
- Collection name: `2 PE fastqs`

## Outputs

| Pair identifier | Forward | Reverse |
|---|---|---|
| GSM461177_untreat_paired | GSM461177_1_subsampled | GSM461177_2_subsampled |
| GSM461180_treat_paired | GSM461180_1_subsampled | GSM461180_2_subsampled |


## Reproducibility Notes
- Subsampled files (~5MB each) were used for efficiency
- Full files (~1.5GB each) available at the same Zenodo record
- Galaxy version: 26.0.1
- History name: RNA-seq-Pasilla-Analysis
