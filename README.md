# Layer-Adaptive Quantization on Diffusion Model using Fisher Information (LAQFI)

> ### **TL;DR**
> - Proposed **layer-adaptive quantization** for diffusion models using **Fisher Information**
> - Identified layer importance in U-Net–based DDPMs to guide selective precision reduction
> - Achieved **49.4% model size reduction** while **improving FID by up to 7%** over uniform quantization
> - Presented as a poster at the **2024 IEIE Symposium**

<br>

This repository presents a research-driven approach to **efficient diffusion model compression** by examining layer-wise sensitivity in U-Net–based architectures.
Instead of applying uniform precision reduction, this work leverages **Fisher Information** to estimate each layer’s relative contribution to image generation quality and uses this signal to guide selective quantization.

By explicitly modeling layer importance, the project demonstrates how principled, information-theoretic analysis can enable both **model efficiency and generative quality preservation** in diffusion models.


[📄 Download Poster (PDF)](https://github.com/doeunyy/ewha-laqfi/blob/main/LAQFI_poster_eng.pdf)

## Table of Contents
1. [Overview](#overview)
2. [Key Contributions](#key-contributions)
3. [Repository Structure](#repository-structure)
4. [Setup & Execution](#setup--execution)
5. [Methodology](#methodology)
6. [Experimental Results](#experimental-results)
7. [Authors](#authors)


## Overview
Diffusion Models deliver high-quality image generation but require heavy memory and slow computation due to full-precision parameters. Traditional quantization reduces memory but often degrades image quality by treating all layers equally.

This project introduces:
Layer-importance-aware quantization using Fisher Information, selectively applying precision reduction where it least affects performance.

Evaluated on DDPM with MNIST dataset.

## Key Contributions
- Fisher Information–based analysis of U-Net layer significance
- Three differential quantization strategies:
  - Global threshold
  - Layer-group adaptive threshold
  - Fully layer-wise adaptive threshold
- Demonstrated both model compression and performance gains
- Fully reproducible pipeline: Fisher computation → Quantization → FID evaluation

## Repository Structure
```graphql
LAQFI/
│
├── simplediffusion.py   # Train baseline DDPM + generate images + compute Fisher + FID + memory
│
├── whole_threshold.py   # Global threshold quantization
├── layer_group.py       # Group-based thresholds (e.g., Layer 1&2 vs 3–6)
├── layer_ratio.py       # Layer-wise thresholds using percentile ratio
├── layer_math.py        # Layer-wise thresholds using mean/variance + scaling
│
├── env.yml              # Conda environment file
└── README.md

```

## Setup & Execution
### 1. Create & Activate Environment
```bash
git clone https://github.com/<USER>/LAQFI.git
cd LAQFI

conda env create -f env.yml
conda activate sd_env
```

### 2. Train Baseline & Compute Fisher Information
```bash
python3 simplediffusion.py
```
This will:
- Train DDPM on MNIST dataset
- Generate evaluation images
- Compute Fisher Information for layer importance
- Measure baseline FID & model size

### 3. Run Quantization Experiments
| Experiment                              | Run Command                  |
| --------------------------------------- | ---------------------------- |
| Global threshold                        | `python3 whole_threshold.py` |
| Layer-group thresholds                  | `python3 layer_group.py`     |
| Percentile-based layer-wise thresholds  | `python3 layer_ratio.py`     |
| Mean/variance-based adaptive thresholds | `python3 layer_math.py`      |

Each experiment performs: <br>
Quantization → Sampling → FID evaluation → Memory measurement


## Methodology
- Compute layer-wise Fisher Information → Estimate importance of each U-Net layer to final image quality
- Set threshold rules per strategy
- Quantize only weights below threshold
- Measure compression & FID change
- Fisher trend insights:
  - Decoder-side (later timesteps) layers more influential
  - Layers 1–2 show notably higher Fisher values ⇒ Protected from aggressive quantization

## Experimental Results
| Model                   | FID ↓     | Size (MB) ↓ | Reduction % ↑ |
| ----------------------- | --------- | ----------- | ------------- |
| Baseline (FP32)         | 22.7512   | 134.2       | –             |
| Uniform Quantization    | 24.1324   | 67.7        | 49.55%        |
| Layer-Group (12 / 3456) | ~23.84    | **67.9**    | **49.4%**     |
| Ratio p = 0.25          | **22.42** | 102.26      | 23.8%         |
| Math-Based Layer Wise   | 23.16     | 81.57       | 39.2%         |

- Best Balance: Layer-Group Strategy
- Best FID: Layer-wise Ratio p=0.25 (≈ +7% improvement over uniform quantization)

 
## Authors

- Doeun Kim (Co-first Author) — [doeunkim.cs@gmail.com](mailto:doeunkim.cs@gmail.com)
- Jieun Byeon (Co-first Author)
- Inae Park (Co-first Author)
- Jaehyeong Sim (Advisor)

Department of Computer Science and Engineering <br>
Ewha Womans University
