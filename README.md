# Beyond Artifacts: Real-Centric Envelope Modeling for Reliable AI-Generated Image Detection

<p align="center">
  <b>ECCV 2026</b>
</p>

<p align="center">
  <a href="https://arxiv.org/abs/2512.20937"><img src="https://img.shields.io/badge/arXiv-2512.20937-b31b1b"></a>
  <a href="https://huggingface.co/datasets/handsomerich/RealChain"><img src="https://img.shields.io/badge/%F0%9F%A4%97%20HuggingFace-RealChain-blue"></a>
  <a href="#license"><img src="https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey"></a>
</p>

## TL;DR

Existing AIGI detectors learn generator-specific **artifacts** that become obsolete as generators evolve and break under real-world degradations. **REM** flips the paradigm: it models the **real image distribution boundary**, achieving robust detection that generalizes to unseen generators and survives chain degradations (social-media compression, filters, stickers, screenshots, ...).

We also release **RealChain**, a comprehensive benchmark with 7 state-of-the-art generators and 50 realistic degradation chains simulating real social media propagation.

<p align="center">
  <img src="assets/teaser.png" width="90%">
</p>

## Highlights

- **New Paradigm**: Real-centric envelope modeling instead of artifact-driven detection — stable, transferable decision boundaries across quality domains
- **State-of-the-Art**: 94.9% average balanced accuracy across 12 benchmarks, +6.9% over previous SOTA
- **Robustness**: 84.2% on severely degraded RealChain (CD), outperforming existing methods by 18.4%
- **Efficiency**: Only requires real images + a VAE for boundary reconstruction — 10x lower GPU cost than diffusion-based data generation

## Method Overview

<p align="center">
  <img src="assets/framework.png" width="95%">
</p>

REM consists of three key modules:

| Module | What it does |
|--------|-------------|
| **MBR** (Manifold Boundary Reconstruction) | Injects controlled perturbations in VAE latent space to generate diverse near-real samples around the real manifold |
| **EE** (Envelope Estimator) | Learns a compact, smooth boundary via binary classification + tangency regularization |
| **CDC** (Cross-Domain Consistency) | Uses frozen DINO as anchor to keep the envelope stable across different quality domains |

## RealChain Benchmark

**RealChain** is a comprehensive AIGI detection benchmark designed for realistic evaluation under real-world conditions.

### Source Images

| Category | Sources | Count |
|----------|---------|-------|
| **Real** | MSCOCO, OpenImage-v7, Unsplash, ImageNet | 7,000 |
| **Flux.1** (Open-source) | Text-to-Image | 1,000 |
| **SDv3.5** (Open-source) | Text-to-Image | 1,000 |
| **QwenImage** (Open-source) | Text-to-Image | 1,000 |
| **Hunyuan 3.0** (Commercial) | Text-to-Image | 1,000 |
| **NanoBanana** (Commercial) | Text-to-Image | 1,000 |
| **Seedream 4.0** (Commercial) | Text-to-Image | 1,000 |
| **Seedream 4.0 i2i** (Commercial) | Image-to-Image | 1,000 |

### Chain Degradations

Each image undergoes a randomly constructed degradation chain (length 2-5) simulating real social media propagation:

**Propagation** (cross-platform upload/download):
`WeChat` | `TikTok` | `Baidu` | `Instagram` | `X (Twitter)`

**Post-processing** (user editing):
`Filter` | `Sticker` | `Crop/Resize` | `Screenshot`

50 unique degradation chains are applied to both real and synthetic images, producing **No Degradation (ND)** and **Chain Degradation (CD)** versions.

### Download

```bash
# Via Hugging Face
git lfs install
git clone https://huggingface.co/datasets/handsomerich/RealChain
```

### Dataset Structure

```
RealChain/
├── Real/              # 7,000 real images
├── Flux1/             # 1,000 Flux.1 generated
├── SDv3.5/            # 1,000 SD v3.5 generated
├── QwenImage/         # 1,000 QwenImage generated
├── Hunyuan3/          # 1,000 Hunyuan 3.0 generated
├── NanoBanana/        # 1,000 NanoBanana generated
├── Seedream4/         # 1,000 Seedream 4.0 (t2i)
├── i2i/               # 1,000 Seedream 4.0 (i2i)
└── degradation_chains.json  # 50 chain definitions
```

### Visual Samples

<p align="center">
  <img src="assets/realchain_samples.png" width="95%">
</p>

Each column shows original (ND) and degraded (CD) versions across all generators and real images. Chain degradations introduce JPEG artifacts, resolution loss, stickers, and color shifts — faithfully reproducing real social media environments.

## Main Results

### Overall Comparison (12 Benchmarks)

| Method | Ideal Benchmarks (Avg) | In-the-Wild Benchmarks (Avg) | RealChain ND | RealChain CD | **Overall Avg** |
|--------|:-----:|:-----:|:-----:|:-----:|:-----:|
| NPR | 50.7 | 56.2 | 74.3 | 51.7 | 56.0 |
| UnivFD | 61.6 | 52.4 | 54.2 | 51.3 | 55.8 |
| FatFormer | 62.8 | 54.4 | 53.9 | 49.2 | 56.2 |
| SAFE | 59.3 | 56.5 | 54.5 | 41.4 | 49.8 |
| C2P-CLIP | 70.4 | 58.7 | 50.0 | 50.0 | 55.3 |
| AIDE | 50.4 | 55.8 | 55.8 | 50.4 | 54.7 |
| Aligned | 79.0 | 56.5 | 63.8 | 71.9 | 68.8 |
| DRCT | 68.0 | 54.6 | 60.1 | 55.0 | 60.8 |
| DDA | 81.4 | 94.5 | 88.5 | 65.8 | 82.3 |
| **REM** | **86.5** | **94.5** | **94.2** | **84.2** | **94.9** |

### Robustness under Chain Degradations

REM maintains **84.2%** balanced accuracy on severely degraded RealChain, while most baselines fall below 60% — demonstrating strong resilience to real-world image quality degradation.

## News

- **[2026/03]** RealChain dataset is now open-sourced on [HuggingFace](https://huggingface.co/datasets/handsomerich/RealChain)!
- Training and inference code will be released soon. Stay tuned!

## Citation

```bibtex
@article{rem2025,
  title={Beyond Artifacts: Real-Centric Envelope Modeling for Reliable AI-Generated Image Detection},
  author={Anonymous},
  journal={arXiv preprint arXiv:2512.20937},
  year={2025}
}
```

## License

- **Code**: MIT License
- **RealChain Dataset**: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)

## Acknowledgements

The real images in RealChain are sourced from [MSCOCO](https://cocodataset.org), [OpenImage-v7](https://storage.googleapis.com/openimages/web/index.html), [Unsplash](https://unsplash.com), and [ImageNet](https://www.image-net.org). Synthetic images are generated using open-source models ([Flux.1](https://github.com/black-forest-labs/flux), [SDv3.5](https://stability.ai), [QwenImage](https://github.com/QwenLM/Qwen2.5-VL)) and commercial APIs ([Hunyuan 3.0](https://hunyuan.tencent.com), [NanoBanana](https://nanobanana.ai), [Seedream 4.0](https://seedream.ai)).
