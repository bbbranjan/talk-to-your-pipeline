# Copilot Demo Prompts — Talk to Your Pipeline

Each block below is a prompt to paste into **Copilot Chat** (`⇧⌘I` macOS / `Ctrl+Shift+I` Windows/Linux).
The annotation below each prompt tells you what to watch for and what to say to the audience.

---

## Setup

```bash
mkdir talk-to-your-pipeline && cd talk-to-your-pipeline
code .
```

Open Copilot Chat. You now have a completely empty folder. Everything that follows comes from asking Copilot.

### Docker alternatives (if Docker Desktop is unavailable)

| Option | Platforms | Notes |
|--------|-----------|-------|
| **[Rancher Desktop](https://rancherdesktop.io)** | macOS, Windows, Linux | Free, open source. Uses dockerd — `docker` CLI works identically. Recommended fallback. |
| **[Podman Desktop](https://podman-desktop.io)** | macOS, Windows, Linux | Free, rootless. Run `alias docker=podman` and nf-core pipelines work unchanged. |
| **[OrbStack](https://orbstack.dev)** | macOS only | Fast, lightweight. Free tier available. Popular with M1/M2 Mac users. |
| **[Colima](https://github.com/abiosoft/colima)** | macOS, Linux | CLI-only. `brew install colima docker` then `colima start`. |

---

## Act 0 — Pre-session setup (night before, not shown live)

Two steps: download the data with fetchngs, then run nf-core/rnaseq.
Reference is pulled automatically from nf-core iGenomes (GRCh38) — no local reference download needed.

---

### 0a. Download the data with fetchngs

```
I want to download 4 RNA-seq samples from SRA for the Himes et al. 2014
dataset (GSE52778): accessions SRR1039508, SRR1039509, SRR1039512, SRR1039513.
Give me the nf-core/fetchngs command to download them using Docker, formatted
for an nf-core/rnaseq downstream run. I'm on Apple Silicon.
```

> **Expected output:**
> ```bash
> nextflow run nf-core/fetchngs -r dev \
>     --input data/sample-ids.csv \
>     --outdir data/fetchngs_results \
>     --nf_core_pipeline rnaseq \
>     -profile docker \
>     -c nextflow.config
> ```
> **Watch for:** Copilot should mention that `--nf_core_pipeline rnaseq` makes fetchngs generate
> a ready-to-use samplesheet. FASTQs land in `data/fetchngs_results/fastq/`.
> **Say:** "One command. Accessions in, FASTQs and samplesheet out. That's the nf-core ecosystem."

---

### 0b. Run the pipeline

```
Give me the Nextflow command to run nf-core/rnaseq using Docker, loading
parameters from params.yml. I'm on Apple Silicon.
```

> **Expected output:**
> ```bash
> nextflow run nf-core/rnaseq -r 3.25.0 \
>     -profile docker \
>     -c nextflow.config \
>     -params-file params.yml
> ```
> Run this after fetchngs completes. Have `results/himes/multiqc/multiqc_report.html` open before the session.

---

## Act 1 — What is this? (2 min)

### 1a. Orient yourself

```
What is nf-core/rnaseq and why would a biologist use it instead of
running STAR or HISAT2 directly?
```

> **Watch for:** Copilot explains the pipeline concept — reproducibility, containerisation, community standards.
> **Say:** "I didn't know any of this. I asked. That's the whole session."

---

### 1b. Check your setup

```
I want to run nf-core/rnaseq on my laptop. I have Java 19 installed.
What else do I need, and how do I check if it's already installed?
Give me the exact shell commands to verify each dependency.
```

> **Watch for:** It should list Nextflow + Docker and give you `nextflow -version` and `docker info` to run.
> **Do:** Open the integrated terminal (`⌃\`` macOS / `Ctrl+\`` Windows/Linux) and run them live.

---

### 1c. Install Nextflow (if not present)

```
Give me the exact commands to install Nextflow on macOS and add it to my PATH.
```

> **Expected output:**
> ```bash
> curl -s https://get.nextflow.io | bash
> sudo mv nextflow /usr/local/bin/
> nextflow -version
> ```

---

### 1d. Configure for Apple Silicon (M1/M2/M3 Macs only)

```
I'm on an Apple Silicon Mac (M2, ARM64). I want to run nf-core pipelines
using Docker. What problems should I expect, and how do I configure
Nextflow and Docker to handle the platform mismatch between ARM64 and
the AMD64 images that nf-core uses?
```

> **Watch for:** Copilot explains that nf-core Docker images are AMD64-only, that Docker runs them
> under Rosetta 2 emulation on Apple Silicon, and that without an explicit platform flag you'll get
> a warning and potentially FASTQ validation errors. It should suggest:
> ```groovy
> // nextflow.config
> docker.runOptions = '--platform linux/amd64'
> ```
> **Do:** Create `nextflow.config` with that one line.
> **Say:** "One line. Copilot knew it because this is a well-known nf-core gotcha on Apple Silicon."
> **Note:** Windows and Intel Mac users can skip this step.

---

## Act 2 — The paper and the dataset (3 min)

Open in browser:
1. Paper: https://doi.org/10.1371/journal.pone.0099625
2. GEO: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE52778

**Say:** "I found this paper. The methods section says 'RNA-seq reads were processed using standard pipelines.' No versions. No parameters. Let's see if we can reproduce it — starting from that sentence and the GEO accession number."

### 2a. Describe the dataset, ask which pipeline to use

```
I found a 2014 RNA-seq paper: Himes et al., PLoS ONE, GSE52778.
The experiment compared dexamethasone-treated vs untreated primary human
airway smooth muscle cells from 4 independent donors. Paired-end RNA-seq.
Data is deposited on SRA as accessions SRR1039508–SRR1039521.

What nf-core pipeline should I use to process this, and what are the
most important parameters I need to set?
```

> **Watch for:** It recommends `nf-core/rnaseq`, mentions GRCh38, STAR or HISAT2 alignment,
> strandedness auto-detection, and the samplesheet format.
> **Say:** "I described the biology in plain English. It matched it to a tool and told me what knobs exist.
> I never mentioned 'rnaseq'. It inferred the right pipeline from the experiment type."

---

### 2b. Ask about the GEO data structure

```
The Himes et al. data is on GEO under accession GSE52778 and on SRA as
accessions SRR1039508–SRR1039521. How do I download this data and prepare
it for nf-core/rnaseq? Is there an nf-core pipeline that handles the download automatically?
```

> **Watch for:** It should mention `nf-core/fetchngs` — give it a CSV of SRR accessions and it
> downloads the FASTQs and auto-generates a ready-to-use nf-core samplesheet.
> **Say:** "There's a whole pipeline just for fetching public data. One command, and the samplesheet
> is written for you. That's the nf-core ecosystem."

---

## Act 3 — Build the analysis (5 min)

### 3a. Create the samplesheet

```
The Himes et al. 2014 data is publicly available on ENA. Can I point
nf-core/rnaseq directly at the ENA download URLs instead of downloading
the files first? If so, create a valid nf-core/rnaseq samplesheet CSV
using the ENA HTTPS URLs for these 4 paired-end samples.
Use strandedness: auto for all.

Samples:
- SRR1039508: donor N61311, untreated
- SRR1039509: donor N61311, dexamethasone-treated
- SRR1039512: donor N052611, untreated
- SRR1039513: donor N052611, dexamethasone-treated
```

> **Watch for:** Copilot will likely say Nextflow supports HTTP/FTP URLs in samplesheets — and
> Nextflow itself does. But nf-core/rnaseq has its own samplesheet validator that checks file
> existence as local paths, so URLs fail at validation time.
> **Do:** Open `data/samplesheet.csv` — show that it points to local paths from fetchngs.
> **Say:** "Copilot was right about Nextflow, but wrong about this specific pipeline. This is exactly
> the 'trust but verify' lesson — always test what it tells you before you rely on it."

---
### 3b. Create the params file

```
I'm running nf-core/rnaseq on a subset of Himes et al. 2014:
- 4 samples: 2 donors (N61311, N052611) × 2 conditions (dex vs untreated)
- Paired-end RNA-seq, human (GRCh38)
- Use the nf-core iGenomes GRCh38 reference (genome: GRCh38)
- STAR alignment + Salmon quantification
- Strandedness: auto-detect
- Skip dupradar
- Samplesheet at data/samplesheet.csv, output to results/himes

Write me a complete params.yml for nf-core/rnaseq.
```

> **Watch for:** A valid YAML with `genome: GRCh38`, `input`, `outdir`, `aligner`, `skip_dupradar`.
> **Do:** Save as `params.yml`. Open the existing one from the repo side-by-side and compare.
> **Say:** "I described what I wanted in English. It produced the config file. Notice `genome: GRCh38` — that one shorthand replaces the FASTA, GTF, and STAR index entirely."

---

### 3c. Get the run command

```
Give me the exact Nextflow command to run nf-core/rnaseq using Docker
and my params.yml file. I'm on Apple Silicon so I need my nextflow.config included.
```

> **Expected:**
> ```bash
> nextflow run nf-core/rnaseq -profile docker -c nextflow.config -params-file params.yml
> ```
> **Say:** "I started this run before the session. Let me show you what it produced."

---

## Act 4 — Results (5 min)

Open `results/himes/multiqc/multiqc_report.html` (pre-run).

### 4a. Ask Copilot to guide the QC review

```
I'm looking at a MultiQC report from nf-core/rnaseq. The experiment is
dexamethasone vs untreated human airway smooth muscle cells, 4 samples,
aligned to full GRCh38. What should I look at first and what would tell
me the experiment worked?
```

> **Watch for:** It should mention FastQC read quality, STAR alignment rate (~80–90% expected),
> gene body coverage for 3′ bias, and PCA/sample clustering showing dex vs untreated separation.
> **Do:** Walk through each section live while Copilot narrates what to look for.

---

## Act 5 — Understand the pipeline (3 min)

### 5a. Open a process file

Open `~/.nextflow/assets/nf-core/rnaseq/modules/nf-core/star/align/main.nf` in VS Code.

```
Explain what this Nextflow process does in plain English.
What files does it take as input and what does it produce as output?
What would happen if the input FASTQ files contained no reads?
```

> **Watch for:** Copilot reads the `input:`, `output:`, and `script:` blocks and explains them clearly.
> **Say:** "I just understood a 50-line bioinformatics script without knowing STAR's documentation."

---

### 5b. Understand a params file

Open `results/himes/pipeline_info/params_*.json`.

```
This is the params file that nf-core/rnaseq used for my run.
Explain each parameter in plain English — what it controls, what the
default would be without it, and whether I'd need to change it for
my own experiment.
```

> **Watch for:** A clean parameter-by-parameter explanation.
> **Say:** "This is how you learn what knobs exist — not by reading docs, by asking."

---

## Act 6 — Troubleshoot (3 min)

### 6a. Paste a real error

```
I got this error running nf-core/rnaseq. What does it mean and what should I check?

ERROR ~ Error executing process > 'NFCORE_RNASEQ:RNASEQ:FASTQ_FASTQC_UMITOOLS_TRIMGALORE:TRIMGALORE (N61311_untreated)'

Caused by:
  Process `TRIMGALORE` terminated with an error exit status (1)

Command error:
  ERROR: No reads passed the filter. Consider lowering --min_trimmed_reads.
  --min_trimmed_reads: 10000
```

> **Watch for:** Copilot identifies that subsampled data falls below the default read threshold
> and gives the fix: add `min_trimmed_reads: 500` to params.yml.
> **Say:** "Faster than Stack Overflow. And it gave the exact param to change."

---

### 6b. Version drift

```
I ran nf-core/rnaseq and specified --genome GRCh38 in my params file,
but I got this error:

  No entry for 'GRCh38' in genomes config for profile 'test'

I copied this param from an nf-core tutorial from 2021. What's happening
and how do I fix it?
```

> **Watch for:** Copilot explains the iGenomes config, suggests checking `--show_hidden_params`,
> and notes that genome shorthands depend on the pipeline version.
> **Say:** "Version drift — the tutorial was right in 2021. Always give Copilot the pipeline version."

---

## Act 7 — Next steps (2 min)

### 7a. What comes after the pipeline?

```
nf-core/rnaseq has finished. I have Salmon quantification files for 4 samples
in results/himes/star_salmon/. My experiment compares dexamethasone vs untreated
human airway smooth muscle cells with 2 donors per condition.

What should I do next to find differentially expressed genes? What tool
should I use, and write me the minimal R code to get started with DESeq2
using the correct paired-donor design.
```

> **Watch for:** DESeq2 with `~ donor + condition` design, `tximeta` or `tximport` to load Salmon
> output, and a `results()` call.
> **Say:** "It wrote a DESeq2 script from a sentence. Stop here though — verify the design formula
> with your statistician. This is where AI can be confidently wrong."

---

## Key teaching moments

| Moment | What to say |
|--------|-------------|
| Copilot recommends nf-core/rnaseq from biology description | "I never said 'rnaseq'. It inferred the tool from the experiment." |
| fetchngs mentioned unprompted | "There's a pipeline for downloading data too. It knew the ecosystem." |
| params.yml generated from English | "Describe what you want, not how to do it." |
| Samplesheet matches the nf-core schema | "It knew the column names. Verify for niche pipelines — it won't always." |
| Low alignment rate correctly diagnosed | "It reasoned about biology, not just the number." |
| TRIMGALORE error fixed in one step | "Faster than docs. But understand the fix before applying it." |
| Version drift identified | "Always give Copilot the pipeline version. Always." |
| DESeq2 design formula | "Stop here. Verify with your statistician. Confident AI ≠ correct AI." |
