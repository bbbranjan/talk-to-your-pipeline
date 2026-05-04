# Live Demo Script

**Duration:** ~20 min (session minutes 25–45)
**Dataset:** Himes et al. 2014 — GSE52778
**Tool:** VS Code + GitHub Copilot Chat

---

## The dataset

**Paper:** Himes BE et al. "RNA-Seq Transcriptome Profiling Identifies CRISPLD2 as a Glucocorticoid Responsive Gene that Modulates Cytokine Function in Airway Smooth Muscle Cells." *PLoS ONE* 2014;9(6):e99625.
**DOI:** https://doi.org/10.1371/journal.pone.0099625
**GEO:** https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE52778

**Biology:** Primary human airway smooth muscle (HASM) cells from 4 independent donors. Cells treated with 1 µM dexamethasone (a corticosteroid) for 18 hours vs vehicle control. Dexamethasone is a common asthma treatment — the same dataset used in Bioconductor and DESeq2 tutorials worldwide.

**Design:** 4 donors × 2 conditions = 8 samples total. For the demo we use 4 samples (2 donors) with full reads streamed directly from ENA and aligned to GRCh38 via nf-core's iGenomes index.

**Why this dataset?**
- Freely available on SRA, streamable directly via ENA URLs — no download needed
- Clean, well-powered experiment with a clear biological question
- The same data used in the DESeq2 vignette — attendees may have already seen the downstream analysis

---

## Step 0 — Pre-session setup (night before)

Two steps: download the data with fetchngs, then run the pipeline. Start both the night before.

### A. Download the 4 SRA samples with fetchngs

```bash
cd "/Users/bobby/ownCloud/EMBL - Staff/Teaching/Skills O'Clock/talk-to-your-pipeline/demo"

nextflow run nf-core/fetchngs -r dev \
    --input data/sample-ids.csv \
    --outdir data/fetchngs_results \
    --nf_core_pipeline rnaseq \
    -profile docker \
    -c nextflow.config
```

FASTQs land in `data/fetchngs_results/fastq/`. fetchngs also auto-generates its own samplesheet
at `data/fetchngs_results/samplesheet/samplesheet.csv` — but we use our custom one in
`data/samplesheet.csv` which has cleaner sample names.

### B. Run the pipeline

```bash
nextflow run nf-core/rnaseq -r 3.25.0 \
    -profile docker \
    -c nextflow.config \
    -params-file params.yml
```

Reference comes from nf-core iGenomes (GRCh38) — no local reference download needed.
Have `results/himes/multiqc/multiqc_report.html` open and ready before the session starts.

---

## Live demo (25–45 min)

### Hook — the paper (2 min)

Open in browser:
1. Paper: https://doi.org/10.1371/journal.pone.0099625
2. GEO page: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE52778

Talking points:
- "I found this paper. It describes an RNA-seq experiment comparing an asthma drug, dexamethasone, against untreated cells."
- "The data is on GEO — 8 samples, freely available, deposited as SRA accessions."
- "The methods section says: 'RNA-seq reads were processed using standard pipelines.' No version numbers, no parameters."
- "The question I want to answer today: could you reproduce this, starting from that sentence?"

---

### Ask Copilot about the paper (3 min)

Open VS Code. Open Copilot Chat (`⇧⌘I` macOS / `Ctrl+Shift+I` Windows/Linux).

**Prompt:**
```
I'm looking at a 2014 RNA-seq paper: Himes et al., PLoS ONE, GSE52778.
It compared dexamethasone-treated vs untreated human airway smooth muscle cells,
4 donors, paired-end RNA-seq, deposited on SRA as accessions SRR1039508–SRR1039521.

What nf-core pipeline should I use to process this data, and what are the
most important parameters I need to set for a human paired-end RNA-seq experiment?
```

> Copilot should recommend `nf-core/rnaseq`, mention genome (GRCh38), aligner, strandedness detection, and samplesheet format.
> **Say:** "I described the biology. It matched it to a tool and told me what knobs to turn. I never mentioned 'rnaseq' — it inferred the right pipeline from the experiment type."

---

### Build the config with Copilot (5 min)

Walk through generating the params file and samplesheet live. See `prompts.md` Acts 3a–3c for the exact prompts.

Open `params-chr22.yml` and `data/samplesheet.csv` in VS Code to compare what Copilot produces against what's already in the repo.

**Key teaching moment:** "I described what I wanted in plain English. It wrote the config file. The samplesheet points directly at ENA URLs — the data never touched my laptop."

---

### Show the results (5 min)

Open `results/himes/multiqc/multiqc_report.html` (from pre-run).

Walk through:
1. **FastQC** — read quality per sample (should all look clean for published GEO data)
2. **STAR alignment** — expect ~80–90 % alignment rate against full GRCh38
3. **Gene body coverage** — check for 3′ bias (a common QC flag for degraded RNA)
4. **PCA / sample clustering** — dex-treated samples should separate clearly from controls

---

### Copilot scenarios (remaining time)

See `prompts.md` for exact prompts. In order:

1. **Understanding a process** — open `main.nf` for STAR_ALIGN, ask Copilot to explain it
2. **Troubleshooting an error** — paste the TRIMGALORE read-filter error, ask Copilot to diagnose
3. **Next steps** — ask Copilot to write the DESeq2 starter code

---

## File summary

```
demo/
├── nextflow.config         # Apple Silicon fix: --platform linux/amd64
├── params-chr22.yml        # Run params: GRCh38 iGenomes, ENA samplesheet
├── data/
│   ├── ids-chr22.csv       # 4 SRR accessions
│   └── samplesheet-chr22.csv  # Samplesheet with direct ENA HTTPS URLs
└── prompts.md              # Copilot prompts for all scenarios
```
