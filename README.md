# Talk to Your Pipeline – Using AI Assistants for Customisable & Reproducible Bioinformatics with Nextflow

**Skills O'Clock | EMBL Heidelberg**
**Date:** 5 May 2026, 14:00 CEST / 13:00 UK
**Location:** Seminar Room 1-202 + Online
**Presenter:** Bobby Ranjan (former Predoctoral Fellow, Hackett Group)

---

## Session Overview

Nextflow has become the gold standard for reproducible, scalable bioinformatics pipelines, but modifying source code has traditionally required substantial computational expertise. This session shows how to describe analysis goals conversationally to an AI assistant, use it to configure and run nf-core pipelines on an HPC cluster, and develop enough intuition to read, tweak, and troubleshoot what's produced.

**No prior coding experience required.**

---

## Pipeline & Dataset

**Pipeline:** `nf-core/rnaseq` — well-documented, visually rich output, universally relatable.

**Dataset:** Himes et al. 2014 (GSE52778) — dexamethasone vs untreated human airway smooth muscle cells. 4 samples across 2 donors. The same data used in the DESeq2 Bioconductor vignette.

**Samples:**

| Accession   | Donor   | Condition              |
|-------------|---------|------------------------|
| SRR1039508  | N61311  | untreated              |
| SRR1039509  | N61311  | dexamethasone-treated  |
| SRR1039512  | N052611 | untreated              |
| SRR1039513  | N052611 | dexamethasone-treated  |

---

## The Demo — 4 Prompts

The entire live demo is driven by 4 Copilot Chat prompts. See [`demo/prompts.md`](demo/prompts.md) for the exact text.

| # | Prompt goal |
|---|-------------|
| 1 | Identify the right nf-core pipeline from a plain-English dataset description |
| 2 | Download FASTQs from SRA and prepare a samplesheet |
| 3 | Build `nextflow.config` and `params.yml` for a SLURM cluster |
| 4 | Submit the job to SLURM, monitor progress, fix failures |

---

## Session Plan (60 min)

| Time  | Segment                    | Content                                                        |
|-------|----------------------------|----------------------------------------------------------------|
| 0–5   | Hook                       | The reproducibility problem: methods section with no versions  |
| 5–15  | Concepts                   | Nextflow mental model (processes, channels, config)            |
| 15–25 | Dataset + pipeline choice  | Prompt 1 — identify the pipeline from biology                  |
| 25–35 | Data download              | Prompt 2 — curl download, samplesheet                          |
| 35–45 | Cluster configuration      | Prompt 3 — SLURM config, params file                           |
| 45–55 | Submit + results           | Prompt 4 — run, monitor, pre-run MultiQC walkthrough           |
| 55–60 | When to trust AI           | Structured reflection + failure cases                          |

---

## Conceptual Talking Points (5–15 min)

**Three things to understand about Nextflow:**

1. **Processes** = individual steps. Each is self-contained: inputs in, command runs, outputs out. Copilot can explain any process in plain English.

2. **Channels** = the pipes connecting processes. Data flows through them asynchronously — Nextflow handles parallelism automatically.

3. **`nextflow.config` + `params.yml`** = the knobs you actually turn. This is where 90% of customisation happens — no pipeline source code needed.

**Key framing:** "You don't need to understand the whole pipeline. You need to know *where to look* and *how to ask*."

---

## When to Trust AI (45–55 min)

### Three rules

1. **Trust it for explanation** — asking what code does is very low risk. Read the answer critically.
2. **Trust it for boilerplate** — config blocks, parameter files. Always run on test data first.
3. **Don't trust it for biology** — it doesn't know your experimental design. Verify strandedness, genome, and design formula with a human expert.

### Two failure cases to show

- Copilot suggests a `--genome` shorthand that doesn't exist in the installed nf-core version (version drift)
- DESeq2 design formula generated without knowing the paired-donor structure of the experiment

---

## Repo Contents

```
demo/
├── nextflow.config         # SLURM executor + Singularity
├── params.yml              # GRCh38 iGenomes, samplesheet reference, output path
├── prompts.md              # The 4 demo prompts
├── demo-script.md          # Presenter notes and walkthrough
└── data/
    ├── sample-ids.csv      # 4 SRR accessions
    └── samplesheet.csv     # nf-core/rnaseq samplesheet (local FASTQ paths)
```

---

## Student Setup Guide

### 1. Java 11+

```bash
java -version  # should show 11 or higher
# If not: brew install openjdk@17  (Mac)
```

### 2. Nextflow

```bash
curl -s https://get.nextflow.io | bash
sudo mv nextflow /usr/local/bin/
nextflow -version
```

### 3. VS Code + extensions

Download from https://code.visualstudio.com. Install:

- **GitHub Copilot** (free tier: 50 chat messages/month, no credit card needed)
- **Nextflow** by Nextflow (syntax highlighting)

Activate Copilot free tier at [github.com/settings/copilot](https://github.com/settings/copilot).

### 4. Pre-flight check

```bash
nextflow run hello
```

Should print "Hello world!" — you're ready.
