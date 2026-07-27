#  Performance and Biases of the LENA and ACLEW Algorithms in Analyzing Language Environments in Down, Fragile X, Angelman Syndromes, and Populations at Elevated Likelihood for Autism 

Code and anonymized data accompanying:

> Lavechin M, Hamrick LR, Kelleher B, Seidl A. Performance and Biases of the LENA and ACLEW
> Algorithms in Analyzing Language Environments in Down, Fragile X, Angelman Syndromes, and
> Populations at Elevated Likelihood for Autism. *Dev Sci*. 2026 Sep;29(5):e70239.
> doi: [10.1111/desc.70239](https://doi.org/10.1111/desc.70239).
> PMID: 42417178; PMCID: PMC13343393.

This repository validates and compares two automated language-environment analysis
algorithms — the proprietary **LENA®** system and the open-source **ACLEW** pipeline
(VTC + ALICE + VCM) — against human annotations from 50 age-matched 2-year-olds across
five diagnostic groups (low-risk controls, Down syndrome, Fragile X syndrome, Angelman
syndrome, and siblings of children with autism). It contains:

1. Anonymized, per-clip annotation transcripts for LENA, ACLEW (VTC/ALICE/VCM), and human
   annotation (`data/annotations/`), plus de-identified recording/child metadata
   (`data/metadata/`).
2. Python code to extract language-environment measures (CTC, AWC, CVC) and performance
   metrics (identification error rate, percentage correct, confusion matrices) from those
   transcripts (`metrics/`, `utils/`).
3. Analysis code reproducing the paper's tables and figures (`analysis/`).

Performance metrics reported in the paper are also available in tabular format at
https://doi.org/10.17605/OSF.IO/5QE4M.

### 1. Reproducing analyses

This repository already contains all the data needed to reproduce the figures/tables of the paper.

Notebooks used to produce the paper's tables and figures can be found in `analysis/metrics`:

| Notebook | Produces |
|---|---|
| `[HUMAN] Summary statistics.ipynb` | Table 4 and 5 |
| `[BIASES SEGMENTATION] 2-mn clips.ipynb` | Table 6 |
| `[BIASES MEASURES] 2-mn clips.ipynb` | Table 7 |
| `[PERFORMANCE PREDICTORS] 30-min clips.ipynb` | Table 8 |
| `[SEGMENTATION] Confusion matrices.ipynb` | Figure 3 |
| `[MEASURES] 30-mn metrics.ipynb` | Figure 4 and 5 |
| `[ANNOTATION] How much.ipynb` | Supplementary Figure 1 |

Companion analyses at the other clip resolution, kept for reference:

- `[BIASES SEGMENTATION] 30-mn clips.ipynb`
- `[MEASURES] 2-mn metrics.ipynb`
- `[SEGMENTATION] 2-mn metrics.ipynb` / `[SEGMENTATION] 30-mn metrics.ipynb`

If you want to go further and recompute language environments measures and performance metrics, you can follow instructions in Section 2. and 3. 

### 2. Compute language environment measures

The folder `measures_files` contains language environment measures (e.g., AWC, CVC, CTC) that need to be extracted using ChildProject. 
You can extract them for our three systems (LENA, ACLEW, human) only for human-annotated regions using the following commands:

```sh
python metrics/compute_standard_measures.py --data_path data --measures_file measures_files/custom_human_chunks.csv --output data/measures/human_measures_chunks.csv --only_human_annotated
python metrics/compute_standard_measures.py --data_path data --measures_file measures_files/custom_lena_chunks.csv --output data/measures/lena_measures_chunks.csv --only_human_annotated
python metrics/compute_standard_measures.py --data_path data --measures_file measures_files/custom_aclew_chunks.csv --output data/measures/aclew_measures_chunks.csv --only_human_annotated
```

These will create three files into the `data/measures` folder containing the language environment measures that we will use to compute performance metrics of our two algorithms.

### 3. Compute LENA and ACLEW performance metrics

Precision, recall, and fscore can be computed using: 

```sh
# LENA vs human
python metrics/compute_pyannote_metrics.py --data_path data --hyp its --ref eaf/an1 --metric fscore
# ACLEW vs human
python metrics/compute_pyannote_metrics.py --data_path data --hyp vtc --ref eaf/an1 --metric fscore
```

These will create two files `results/pyannote_metrics/its_eaf_an1/fscore_30mn_clips.csv` and `results/pyannote_metrics/vtc_eaf_an1/fscore_30mn_clips.csv`.

Next, let's compute confusion matrices using:

```sh
# LENA vs human
python metrics/compute_confusion.py --data_path data --set1 its --set2 eaf/an1
# ACLEW vs human
python metrics/compute_confusion.py --data_path data --set1 vtc --set2 eaf/an1
```

These will create confusion matrices in the `results/conf/its_eaf_an1` and the `results/conf/vtc_eaf_an1` folders. 

Finally, we can compute identification error rate and percentage correct:

```shell
python metrics/compute_pyannote_metrics.py --data_path data --hyp its --ref eaf/an1 --metric ider --two_mn_clip_level
python metrics/compute_pyannote_metrics.py --data_path data --hyp vtc --ref eaf/an1 --metric ider --two_mn_clip_level
```

These will create two files `results/pyannote_metrics/its_eaf_an1/ider_30mn_clips.csv` (for performance metrics computed on the megaclip) and `results/pyannote_metrics/its_eaf_an1/ider_2mn_clips.csv` (for performance metrics at the 2-min clip level).
Similarly for ACLEW, these will create two files `results/pyannote_metrics/vtc_eaf_an1/ider_30mn_clips.csv` and `results/pyannote_metrics/vtc_eaf_an1/ider_2mn_clips.csv`
