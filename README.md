### 1. Introduction

We ran [VTC 1.0](https://github.com/MarvinLvn/voice-type-classifier), ALICE (https://github.com/orasanen/ALICE), and VCM (https://github.com/LAAC-LSCP/vcm) and converted all files to .csv using [ChildProject](https://childproject.readthedocs.io/en/latest/).
This repository contains:
1) Annotation files for ACLEW, LENA along with human annotations (.csv)
2) Python code to extract performance metrics (identification error, percentage correct, confusion matrices, etc.) from the .csv files
3) Python or R code to generate the figures

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
