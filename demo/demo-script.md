# Live Demo Script

**Duration:** ~20 min (slides 16–22, session minutes 25–45)
**Dataset:** Himes et al. 2014 — GSE52778
**Tool:** VS Code + GitHub Copilot Chat

---

## The dataset

**Paper:** Himes BE et al. "RNA-Seq Transcriptome Profiling Identifies CRISPLD2 as a Glucocorticoid Responsive Gene that Modulates Cytokine Function in Airway Smooth Muscle Cells." *PLoS ONE* 2014;9(6):e99625.

**Biology:** Primary human airway smooth muscle (HASM) cells from 4 independent donors. Cells treated with 1 µM dexamethasone (a corticosteroid) for 18 hours vs vehicle control. Dexamethasone is a common asthma treatment — understanding which genes it affects in airway cells is directly clinically relevant.

**Why this dataset:**
- Human, relatable biology (asthma/steroids)
- 4 donors × 2 conditions = 8 samples, clean paired design
- The same dataset used in Bioconductor/DESeq2 tutorials — audience may recognise it
- Freely available, no access restrictions

---

## Step 0 — Pre-session setup (do this before the session)

### Download data (takes ~2–3 hours, run the night before)

```bash
cd demo/

# Download all 8 samples using fetchngs
nextflow run nf-core/fetchngs \
    --input data/ids.csv \
    --outdir data/fetchngs_results \
    --nf_core_pipeline rnaseq \
    -profile docker

# fetchngs auto-generates a samplesheet at:
# data/fetchngs_results/samplesheet/samplesheet.csv
```

### Subsample for demo speed (optional but recommended)

```bash
# Subsample each sample to 2M read pairs (~10% of original)
# This cuts STAR alignment time from ~20 min to ~3 min per sample

for srr in SRR1039508 SRR1039509 SRR1039512 SRR1039513 \
           SRR1039516 SRR1039517 SRR1039520 SRR1039521; do
    seqtk sample -s 42 data/fetchngs_results/fastq/${srr}_1.fastq.gz 2000000 \
        | gzip > data/subsampled/${srr}_1.fastq.gz
    seqtk sample -s 42 data/fetchngs_results/fastq/${srr}_2.fastq.gz 2000000 \
        | gzip > data/subsampled/${srr}_2.fastq.gz
done
```

### Run the pipeline (takes ~1–2 hours, run before the session)

```bash
nextflow run nf-core/rnaseq \
    --input data/samplesheet.csv \
    --outdir results \
    --genome GRCh38 \
    -profile docker \
    -params-file params.yml
```

Have the `results/` folder ready to open live.

---

## Step 1 — Introduce the dataset (2 min)

Open in browser:
1. The GEO page: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE52778
2. The paper: https://doi.org/10.1371/journal.pone.0099625

Talking points:
- "4 human donors, cells treated with dexamethasone vs untreated"
- "8 samples — small enough to run in an afternoon, real enough to publish"
- "Freely available — no login, no data agreement"

---

## Step 2 — Download with fetchngs (3 min)

Open `demo/data/ids.csv` in VS Code. Show the file — just 8 SRR accessions.

```bash
# Show the command (don't run live — show pre-run output)
nextflow run nf-core/fetchngs \
    --input data/ids.csv \
    --outdir data/fetchngs_results \
    --nf_core_pipeline rnaseq \
    -profile docker
```

Show the auto-generated samplesheet at `data/fetchngs_results/samplesheet/samplesheet.csv`.

**Copilot moment:** Ask Copilot to explain what fetchngs does:
```
What does nf-core/fetchngs do and why would I use it instead of
downloading files manually from GEO?
```

---

## Step 3 — Run the pipeline (3 min)

Show the command. Don't run live — open the pre-run `results/` folder immediately.

```bash
nextflow run nf-core/rnaseq \
    --input data/samplesheet.csv \
    --outdir results \
    --genome GRCh38 \
    -profile docker \
    -params-file params.yml
```

Open `results/multiqc/multiqc_report.html` in the browser. Walk through:
- FastQC per-sample quality
- Alignment rates (>80% = good)
- Gene body coverage
- Sample-level PCA (dex vs untreated should separate)

---

## Step 4 — Copilot scenarios (12 min)

See `prompts.md` for the exact prompts to paste. In order:

1. **Understanding a process** (~2 min) — open `main.nf` for STAR_ALIGN, ask Copilot to explain it
2. **Generating a params file** (~4 min) — ask Copilot to write params.yml from a plain-English description
3. **Generating a samplesheet** (~3 min) — ask Copilot to generate the CSV from sample metadata
4. **Troubleshooting an error** (~3 min) — paste the TRIMGALORE error, watch Copilot diagnose it

---

## Notes for the presenter

- Keep VS Code and the browser side-by-side (not switching windows)
- For Copilot responses: read the first sentence aloud, then scroll to show the full output
- If Copilot generates something wrong: don't hide it — use it as a teaching moment ("this is exactly when you need to verify")
- The MultiQC PCA plot is the best visual payoff — the dex-treated samples should cluster together away from controls
