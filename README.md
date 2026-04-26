# SAM Medical Segmentation Project

Comprehensive notebook-based project for medical image segmentation benchmarking and fine-tuning using Segment Anything variants (SAM2 and SAM3), with a static website report for submission.

---

## 1) Project Purpose

This project studies how prompt strategies affect segmentation quality on dermoscopic lesion images and evaluates whether fine-tuning improves performance and robustness.

Main goals:

- Compare prompt modes (`bbox`, `point`, `text`, `auto`) on baseline models.
- Measure overlap quality using Dice and IoU.
- Quantify reliability using failure rate and prompt gain metrics.
- Fine-tune SAM3 and compare against zero-shot behavior.
- Present results in a polished static report website.

---

## 2) Repository Structure

```text
SAM/
├── SAM2.ipynb
├── SAM3.ipynb
├── SAM3Fine_tuned.ipynb
├── Medical Presentation..pptx
├── README.md
├── requirements.txt
├── pyproject.toml
└── website/
    ├── index.html
    ├── styles.css
    └── assets/
        ├── notebook_images/
        ├── ppt_media/
        └── rubric.png
```

### Key files

- `SAM2.ipynb`  
  SAM2 benchmark notebook with multi-prompt evaluation and summary tables/charts.

- `SAM3.ipynb`  
  SAM3 zero-shot evaluation notebook, including per-prompt metrics and lesion-size analysis.

- `SAM3Fine_tuned.ipynb`  
  SAM3 fine-tuning notebook (training loop + validation + best checkpoint + post-finetune evaluation).

- `website/index.html` + `website/styles.css`  
  Final static blog/report page with metrics, animations, image gallery, and interactive lightbox.

---

## 3) Experimental Workflow

### Phase A: Baseline benchmarking (`SAM2.ipynb`)

1. Install runtime dependencies and model code.
2. Prepare dataset and mask paths.
3. Run inference for each prompt type.
4. Compute metrics: Dice, IoU, failure.
5. Export summary CSVs and plots.

### Phase B: SAM3 zero-shot analysis (`SAM3.ipynb`)

1. Load SAM3 model and processor.
2. Run full prompt comparison (`bbox`, `point`, `text`, `auto`).
3. Aggregate metrics by prompt.
4. Stratify performance by lesion size buckets.

### Phase C: Fine-tuning and re-evaluation (`SAM3Fine_tuned.ipynb`)

1. Split data into train/validation/test style workflow.
2. Train with segmentation losses and optimizer scheduling.
3. Save best model checkpoint by validation Dice.
4. Evaluate fine-tuned model on test images using same prompt protocol.
5. Compare zero-shot vs fine-tuned quality and robustness.

---

## 4) Metrics and Definitions

Given prediction mask `P` and ground truth mask `G`:

- **Dice coefficient**  
  `Dice = 2|P ∩ G| / (|P| + |G|)`

- **IoU (Jaccard index)**  
  `IoU = |P ∩ G| / |P ∪ G|`

- **Failure Rate**  
  Fraction of predictions considered failures by the notebook criterion (thresholded quality condition).

- **TPG (Text Prompt Gain)**  
  Mean Dice(text) minus mean Dice(reference prompt), used to quantify whether text prompting helps or hurts.

These metrics are combined because overlap alone can hide reliability issues.

---

## 5) Notable Reported Results (from notebooks)

### SAM2 summary (shown in notebook output)

- `bbox`: Dice `0.8882`, IoU `0.8136`, failure `0.043`
- `point`: Dice `0.7285`, IoU `0.6349`, failure `0.286`
- `text`: Dice `0.0131`, IoU `0.0095`, failure `0.994`
- `auto`: Dice `0.3644`, IoU `0.3052`, failure `0.707`
- TPG reported: `-0.8750`

### SAM3 zero-shot summary

- `bbox`: Dice `0.6885`, IoU `0.6327`, failure `0.3`
- `point`: Dice `0.3147`, IoU `0.2166`, failure `0.4`
- `text`: Dice `0.0000`, IoU `0.0000`, failure `1.0`
- `auto`: Dice `0.0954`, IoU `0.0912`, failure `0.9`
- TPG reported: `-0.3147`

### Fine-tuned SAM3 summary

- `bbox`: Dice `0.9417`, IoU `0.8952`, failure `0.0`
- `point`: Dice `0.8910`, IoU `0.8219`, failure `0.5%`
- `text`: Dice `0.8832`, IoU `0.8121`, failure `2.5%`
- `auto`: Dice `0.8719`, IoU `0.7980`, failure `2.5%`
- Fine-tuned TPG: `-0.0078`

Interpretation: fine-tuning significantly improves all prompt modes and greatly reduces failure.

---

## 6) Environment and Setup

The original notebooks were authored in Colab and use paths like `/content/...` and `/content/drive/MyDrive/...`.

For local runs, update paths in notebook cells to your local dataset/checkpoint directories.

### Option A: `requirements.txt`

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

### Option B: `pyproject.toml`

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -e .
```

### Python version

- Recommended: Python `3.10+`

### GPU note

- These workflows are GPU-oriented and may be very slow on CPU.

---

## 7) External Code and Model Dependencies

- `SAM2.ipynb` installs SAM2 from source in-notebook (`facebookresearch/sam2`).
- `SAM3.ipynb` and `SAM3Fine_tuned.ipynb` expect the SAM3 package/runtime in environment.
- Hugging Face hub is used for model checkpoint downloads.

If running outside Colab, ensure:

- model checkpoints are downloaded locally,
- paths are updated,
- CUDA-compatible PyTorch build is installed.

---

## 8) Data and Path Conventions

Common path patterns used in notebooks:

- `/content/data/images`, `/content/data/masks`
- `/content/ISIC/images`, `/content/ISIC/masks`
- `/content/checkpoints/...`
- `/content/drive/MyDrive/...`

For local execution:

1. Keep a clean `images/` and `masks/` structure.
2. Maintain matching file names for image-mask pairs.
3. Replace all hardcoded Colab paths before running end-to-end.

---

## 9) Static Website Report

A submission-ready report website is included in `website/`.

Features:

- structured project narrative,
- interactive metric dashboard,
- animated KPI/cards/charts,
- image lightbox popup (click-to-zoom),
- curated notebook and slide visuals.

Run/open:

```bash
open website/index.html
```

or open the file directly in any browser.

---

## 10) Reproducibility Guidelines

To improve reproducibility and consistency:

1. Pin all critical package versions after your final successful run.
2. Log checkpoint path, dataset version, and run date.
3. Keep notebook outputs only for final evidence; clear noisy intermediate outputs.
4. Save exported CSV summaries alongside plots.
5. Use a consistent random seed for split-sensitive experiments.

---

## 11) Common Issues and Fixes

- **Issue:** Missing module errors  
  **Fix:** Reinstall dependencies in a clean virtual environment.

- **Issue:** Path/file not found  
  **Fix:** Replace `/content/...` references with local absolute/relative paths.

- **Issue:** CUDA mismatch / GPU unavailable  
  **Fix:** Install compatible PyTorch CUDA build and verify GPU visibility.

- **Issue:** Notebook too slow or crashes  
  **Fix:** Reduce sample size first, then scale to full evaluation.

---

## 12) Suggested Next Improvements

- Add script-based pipeline (`src/` + CLI) for reproducible non-notebook runs.
- Add a fixed experiment config file (`yaml/json`) for paths and hyperparameters.
- Add formal train/val/test split logs and seed tracking.
- Add automated result export into `website/assets/` for one-command report refresh.

---

## 13) Authors

- Taher Mustansir Bhurka (PRN: 938005585)
- Jeet Ashokbhai Patel (PRN: 138008847)
- Yash Vijay Patil (PRN: 738001186)
