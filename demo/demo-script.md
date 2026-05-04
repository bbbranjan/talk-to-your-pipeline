# Live Demo Script

**Duration:** ~20 min
**Dataset:** Himes et al. 2014 — GSE52778
**Tool:** VS Code + GitHub Copilot Chat
**Environment:** SLURM cluster

---

## The dataset

**Paper:** Himes BE et al. "RNA-Seq Transcriptome Profiling Identifies CRISPLD2 as a Glucocorticoid Responsive Gene that Modulates Cytokine Function in Airway Smooth Muscle Cells." *PLoS ONE* 2014;9(6):e99625.
**DOI:** https://doi.org/10.1371/journal.pone.0099625
**GEO:** https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE52778

**Biology:** Primary human airway smooth muscle (HASM) cells from 4 independent donors. Cells treated with 1 µM dexamethasone (a corticosteroid) for 18 hours vs vehicle control. The same dataset used in Bioconductor and DESeq2 tutorials worldwide.

**Samples used:**
- SRR1039508: donor N61311, untreated
- SRR1039509: donor N61311, dexamethasone-treated
- SRR1039512: donor N052611, untreated
- SRR1039513: donor N052611, dexamethasone-treated

---

## Pre-session setup (night before)

Have results pre-run and `results/himes/multiqc/multiqc_report.html` open before the session.

### Download FASTQs via curl

```bash
mkdir -p data/fastq
for acc in SRR1039508 SRR1039509 SRR1039512 SRR1039513; do
    curl -O --output-dir data/fastq \
        "https://sra-pub-run-odp.s3.amazonaws.com/sra/${acc}/${acc}_1.fastq.gz"
    curl -O --output-dir data/fastq \
        "https://sra-pub-run-odp.s3.amazonaws.com/sra/${acc}/${acc}_2.fastq.gz"
done
```

FASTQs land in `data/fastq/`. The samplesheet in `data/samplesheet.csv` uses these paths.

### Run nf-core/rnaseq

```bash
nextflow run nf-core/rnaseq -r 3.25.0 \
    -profile singularity \
    -c nextflow.config \
    -params-file params.yml
```

---

## Live demo — the 4 prompts

Open VS Code. Open Copilot Chat (`⇧⌘I` macOS / `Ctrl+Shift+I` Windows/Linux).
See `prompts.md` for the exact text of each prompt.

### Prompt 1 — What pipeline?

Describe the paper and the 4 samples. Ask what Nextflow pipeline to use.

> **Watch for:** Copilot recommends `nf-core/rnaseq`, mentions GRCh38, STAR or HISAT2 alignment, strandedness auto-detection, samplesheet format.
> **Say:** "I described the biology in plain English. It matched it to the right tool without me ever saying 'rnaseq'."

---

### Prompt 2 — Download the data

Ask it to download FASTQs from SRA using curl and prepare a samplesheet.

> **Watch for:** It should use the SRA HTTPS endpoint (or prefetch + fasterq-dump), give curl commands for both R1 and R2 FASTQ files for each accession, then produce a samplesheet CSV pointing at those local files. Show `data/samplesheet.csv`.
> **Say:** "SRA files are available over HTTPS. No special tools — just curl and a samplesheet."

---

### Prompt 3 — Configure for SLURM

Ask it to install modules, build `nextflow.config` and `params.yml` for SLURM.

> **Watch for:** SLURM executor block, Singularity config, resource labels per process. Show the repo's `nextflow.config` and `params.yml` side-by-side with what Copilot generates.
> **Say:** "I described what I wanted — SLURM, Singularity, these samples. It wrote both config files. The samplesheet ties it all together."

---

### Prompt 4 — Submit and monitor

Ask it to submit to SLURM, monitor, and fix failures.

> **Watch for:** The `sbatch`-style submission via Nextflow, how to check `squeue`, what to do with a failed process. Show `results/himes/multiqc/multiqc_report.html` from the pre-run.
> **Say:** "Four prompts. Dataset identified, data downloaded, cluster configured, job submitted. That's the whole workflow."

---

## File summary

```
demo/
├── nextflow.config         # SLURM executor + Singularity
├── params.yml              # GRCh38 iGenomes, samplesheet reference, output path
├── prompts.md              # The 4 demo prompts
├── demo-script.md          # This file
└── data/
    ├── sample-ids.csv      # 4 SRR accessions
    └── samplesheet.csv     # nf-core/rnaseq samplesheet (local FASTQ paths)
```
