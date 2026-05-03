# Copilot prompts — Skills O'Clock demo

Paste these into Copilot Chat during the live demo. Each is annotated with when to use it and what to watch for in the response.

---

## Scenario 1 — Understanding a process

**When:** Open `modules/nf-core/star/align/main.nf` in VS Code, then open Copilot Chat.

```
Explain what this Nextflow process does in plain English.
What files does it take as input, and what does it produce as output?
```

**What to show:** Copilot reads the process block and gives a plain-English summary.
Point out: you understood a tool without knowing STAR's documentation.

---

## Scenario 2 — Generating a params file

**When:** Open a blank `params.yml` in VS Code.

```
I'm running nf-core/rnaseq on human airway smooth muscle cells.
The data is paired-end RNA-seq from 4 donors, comparing dexamethasone
treatment vs untreated control. I'm using GRCh38 as the reference genome.
I want to use STAR for alignment and Salmon for quantification.
Write me a params.yml file for nf-core/rnaseq for this experiment.
```

**What to show:** A complete params.yml. Compare it to the one in this repo.
Point out: the biology context in the prompt is what makes the output accurate.

---

## Scenario 3 — Generating a samplesheet

**When:** After showing the downloaded FASTQ files.

```
Generate a valid nf-core/rnaseq samplesheet CSV for these 8 paired-end samples.
Each sample has files named SRR{accession}_1.fastq.gz and SRR{accession}_2.fastq.gz
in a folder called data/.

Samples:
- SRR1039508: cell line N61311, untreated
- SRR1039509: cell line N61311, dexamethasone
- SRR1039512: cell line N052611, untreated
- SRR1039513: cell line N052611, dexamethasone
- SRR1039516: cell line N080611, untreated
- SRR1039517: cell line N080611, dexamethasone
- SRR1039520: cell line N061011, untreated
- SRR1039521: cell line N061011, dexamethasone

Use strandedness: auto for all samples.
```

**What to show:** A valid CSV matching the nf-core/rnaseq schema.
Compare to `data/samplesheet.csv` in this repo.

---

## Scenario 4 — Troubleshooting an error

**When:** Show this error message (paste into Copilot Chat).

```
I got this error running nf-core/rnaseq. What does it mean and what should I check?

ERROR ~ Error executing process > 'NFCORE_RNASEQ:RNASEQ:FASTQ_FASTQC_UMITOOLS_TRIMGALORE:TRIMGALORE (N61311_untreated)'

Caused by:
  Process `TRIMGALORE` terminated with an error exit status (1)

Command error:
  ERROR: No reads passed the filter. Consider lowering --min_trimmed_reads.
  --min_trimmed_reads: 10000
```

**What to show:** Copilot identifies the cause immediately (too few reads after trimming)
and suggests the fix (lower `--min_trimmed_reads` or check input file).
Point out: this is faster than reading the nf-core docs.

---

## Bonus — Asking about the biology

**When:** If time allows, after showing the MultiQC report.

```
The nf-core/rnaseq pipeline has finished. I can see from MultiQC that all
samples have >80% alignment rate and good QC metrics. My experiment compares
dexamethasone-treated vs untreated human airway smooth muscle cells across
4 donors. What should I do next to find differentially expressed genes,
and what tool would you recommend?
```

**What to show:** Copilot suggests DESeq2 or edgeR, explains the paired design,
and can write starter R code.
Point out the limitation: confirm the biology-specific recommendations manually.
```
