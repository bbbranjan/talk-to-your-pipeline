# Demo Prompts — Talk to Your Pipeline

Paste each prompt in order into **Copilot Chat** (`⇧⌘I` macOS / `Ctrl+Shift+I` Windows/Linux).

---

## Prompt 1 — Identify the pipeline

```
I found a 2014 RNA-seq paper: Himes et al., PLoS ONE, GSE52778.
The experiment compared dexamethasone-treated vs untreated primary human
airway smooth muscle cells from 4 independent donors. Paired-end RNA-seq.
Data is deposited on SRA as accessions SRR1039508–SRR1039521. I only want to use the following samples:
- SRR1039508: donor N61311, untreated
- SRR1039509: donor N61311, dexamethasone-treated
- SRR1039512: donor N052611, untreated
- SRR1039513: donor N052611, dexamethasone-treated

What NextFlow pipeline should I use to process this?
```

---

## Prompt 2 — Download the data

```
Can you download the FASTQ files for these samples from SRA using curl and prepare them as input for nf-core/rnaseq?
```

---

## Prompt 3 — Configure for SLURM

```
I am running this nf-core/rnaseq on a SLURM cluster. Install the required modules for this (Nextflow, Docker/Singularity, Java, Git). Build my nextflow.config file, params.yml YAML file and refer to the samplesheet CSV file such that they are usable by NextFlow to submit jobs to SLURM and handle resource allocation.
```

---

## Prompt 4 — Run and monitor

```
Submit the job to SLURM. Monitor its progress, and if it fails, fix any errors and rerun it.
```
