# Talk to Your Pipeline – Using AI Assistants for Customisable & Reproducible Bioinformatics with Nextflow

**Skills O'Clock | EMBL Heidelberg**
**Date:** 5 May 2026, 14:00 CEST / 13:00 UK
**Location:** Seminar Room 1-202 + Online
**Presenter:** Bobby Ranjan (former Predoctoral Fellow, Hackett Group)

---

## Session Overview

Nextflow has become the gold standard for reproducible, scalable bioinformatics pipelines, but modifying source code has traditionally required substantial computational expertise. This session teaches participants to describe their analysis goals conversationally to adapt Nextflow pipeline code with AI assistance, and to develop enough intuition to read, tweak, and troubleshoot what's produced.

**No prior coding experience required. Curiosity about your data is enough.**

---

## Pipeline & Dataset

**Pipeline:** `nf-core/rnaseq` — universally relatable (gene expression), well-documented, visually rich output.

**Dataset:** nf-core's built-in `test` profile — tiny curated FASTQ files pulled automatically from GitHub. No manual data prep needed.

**Single command to run the whole demo:**
```bash
nextflow run nf-core/rnaseq -profile test,docker --outdir results
```

---

## Session Plan (60 min)

| Time | Segment | Content |
|------|---------|---------|
| 0–5 | Hook | The reproducibility problem: "Here's a methods section from a 2022 paper. Can you reproduce it?" Show how Nextflow + nf-core solves this. |
| 5–15 | Concepts | Nextflow mental model (10 min) |
| 15–25 | Live demo: download + run | One command, watch it go |
| 25–45 | Live demo: adapt with Copilot | Three escalating scenarios |
| 45–55 | When to trust AI | Structured reflection + failure cases |
| 55–60 | Wrap-up + template handoff | What they leave with |

---

## Conceptual Talking Points (5–15 min)

Keep this visual — draw or show a diagram, don't show code yet.

**Three things to understand about Nextflow:**

1. **Processes** = individual steps. Each is a self-contained unit: inputs in, command runs, outputs out. Like a function. Copilot can explain any process in plain English.

2. **Channels** = the pipes connecting processes. Data flows through them asynchronously. You rarely need to touch these — but knowing they exist explains why Nextflow handles parallelism automatically.

3. **`nextflow.config` + `params`** = the knobs you actually turn. This is where 90% of customisation happens — no pipeline source code needed.

**Key framing:** "You don't need to understand the whole pipeline. You need to know *where to look* and *how to ask*."

---

## Demo Script: Exact Commands

### Pre-session setup (your machine only)

```bash
# Install Nextflow (requires Java 11+)
curl -s https://get.nextflow.io | bash
sudo mv nextflow /usr/local/bin/

# Verify
nextflow -version

# Docker must be running (for nf-core profiles)
docker info
```

### Segment 1: Download + run (15–25 min)

Show the nf-core pipeline page first in browser: https://nf-co.re/rnaseq

```bash
nextflow run nf-core/rnaseq -profile test,docker --outdir results
```

Narrate what's happening as it runs:
- Nextflow is pulling the pipeline from GitHub
- The `test` profile provides sample data automatically
- Each process appears as it executes
- Point out the work directories

While it runs (or using pre-run results): open `results/` in VS Code, show the MultiQC HTML report.

---

## Copilot Demo Scenarios (25–45 min)

Three escalating scenarios. For each: open the relevant file in VS Code, invoke Copilot Chat, show the interaction.

### Scenario 1 — Understanding (2 min)

Open `modules/nf-core/star/align/main.nf` in VS Code, then in Copilot Chat:

```
Explain what this Nextflow process does in plain English.
What are its inputs and outputs?
```

Goal: show that you can understand any pipeline step without being a programmer.

### Scenario 2 — Customising parameters (8 min)

Create a custom params file. In VS Code, open a blank `my_params.yml` and ask Copilot:

```
I'm running nf-core/rnaseq on mouse samples with paired-end FASTQ files.
I want to use GRCm39 as the reference genome and skip the dupradar QC step.
Write me a params.yml file for this.
```

Then show how to use it:
```bash
nextflow run nf-core/rnaseq -profile docker --params-file my_params.yml --outdir results
```

### Scenario 3 — Troubleshooting an error (8 min)

Paste a realistic Nextflow error into Copilot Chat:

```
I got this error running nf-core/rnaseq. Explain what it means and what I should check:

[paste error here — e.g., a missing samplesheet column or memory exceeded error]
```

Goal: show the pattern of "paste error → ask → act" rather than googling blindly.

**Bonus — generate a samplesheet:**

```
Generate a valid nf-core/rnaseq samplesheet CSV for 4 paired-end samples:
SRR001, SRR002, SRR003, SRR004. Each has _R1.fastq.gz and _R2.fastq.gz files
in a folder called /data/fastq/.
```

---

## When to Trust AI (45–55 min)

### Three rules

1. **Trust it for explanation** — asking what code does is very low risk. Read the answer critically but it's usually right.
2. **Trust it for boilerplate** — samplesheets, config blocks, parameter files. Always run on test data first.
3. **Don't trust it for biology** — Copilot doesn't know your experimental design. It will confidently suggest wrong strandedness, wrong genome, wrong tool for your assay.

### Two failure cases to show

- Asking Copilot to suggest alignment parameters without telling it the library type → it picks a default that may be wrong
- Copilot suggests a `--genome` shorthand that doesn't exist in your nf-core version (version drift)

### The fix

Always tell Copilot your context explicitly:

```
I'm analysing SMART-seq2 single-cell RNA data from mouse embryos,
stranded, aligned to GRCm39. I'm using nf-core/rnaseq v3.14.
```

---

## Student Setup Guide

Send this **at least 3 days before** the session.

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

### 3. Docker Desktop

Download from https://www.docker.com/products/docker-desktop/, start it, then verify:

```bash
docker run hello-world
```

### 4. VS Code + extensions

- **GitHub Copilot** (requires GitHub account + Copilot subscription or student access)
- **Nextflow** extension by Nextflow (syntax highlighting)

### 5. Pre-flight check

```bash
nextflow run hello  # Nextflow's built-in hello world
```

Should print "Hello world!" — if it does, you're ready.

---

## What Students Leave With

A folder containing:

- `my_params.yml` — the template built during the session
- `samplesheet_template.csv` — the samplesheet Copilot generated
- `prompts.md` — the Copilot prompts used, with notes on when to use each
- Link to the nf-core pipeline list and `test` profile docs

---

## Notes & Decisions

- **Run live or pre-cached?** Running live is more authentic but risks timing issues. Recommended: pre-run and have `results/` ready as a fallback.
- **Alternative pipeline:** `nf-core/sarek` (variant calling) if audience is more genomics-focused.
