```text
 ███   ███  █      ███  ████
█   █ █   █ █     █   █ █   █
█     █   █ █     █   █ █   █
 ███  █████ █     █████ ████
    █ █   █ █     █   █ █ █
█   █ █   █ █     █   █ █  █
 ███  █   █ █████ █   █ █   █

 ▓▓▓   ▓▓▓  ▓     ▓   ▓ ▓▓▓  ▓▓▓
▓   ▓ ▓   ▓ ▓     ▓   ▓  ▓  ▓   ▓
▓     ▓   ▓ ▓     ▓   ▓  ▓  ▓   ▓
 ▓▓▓  ▓▓▓▓▓ ▓     ▓   ▓  ▓  ▓▓▓▓▓
    ▓ ▓   ▓ ▓     ▓   ▓  ▓  ▓   ▓
▓   ▓ ▓   ▓ ▓      ▓ ▓   ▓  ▓   ▓
 ▓▓▓  ▓   ▓ ▓▓▓▓▓   ▓   ▓▓▓ ▓   ▓
```

# Cognitive Immunization Framework (CIF)

**A multi-layer defense-in-depth architecture for LLM safety via representation engineering.**

Salar Farzaneh, Independent Researcher · ORCID: [0009-0008-7236-9241](https://orcid.org/0009-0008-7236-9241)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/SaliSalvia/cif-cognitive-immunization/blob/main/notebooks/CIF_Layer3_Validation.ipynb)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## Status

**Theoretical framework and reference implementation: complete.**
**Empirical validation: in progress.** Results reported here are updated as evaluation runs complete — see [`results/`](results/) for the latest numeric output and the paper for full discussion. This repository does not claim validated results until numbers are committed to `results/` and reflected in the paper.

## What this is

CIF is a four-layer defense-in-depth architecture against LLM jailbreaking: input intent bounding, constitutional prompt neutralization, SAE/activation-based representation surgery, and output validation. This repo contains the reference implementation and evaluation protocol for **Layer 3** (runtime representation surgery via orthogonal projection), which is the component with a testable, falsifiable hypothesis (the *Latent Bounding Hypothesis* — see the paper, Section 4).

This is explicitly framed as a **candidate architecture and a hypothesis to be tested**, not a proven safety guarantee. See `paper/` for the full theoretical treatment, including the Limitations section, which should be read before interpreting any result here.

## Repository structure

```
.
├── README.md
├── LICENSE
├── CITATION.cff
├── requirements.txt
├── notebooks/
│   └── CIF_Layer3_Validation.ipynb   # Colab-ready empirical validation notebook
├── paper/
│   └── CIF_Cognitive_Immunization_Framework_v1.pdf
└── results/
    └── cif_empirical_results.json    # populated after the notebook is run
```

## Reproducing the evaluation

1. Open `notebooks/CIF_Layer3_Validation.ipynb` in Google Colab (badge above), or clone this repo and run it locally with a CUDA GPU (≥16GB VRAM recommended for 4-bit inference).
2. Set `Runtime → Change runtime type → T4 GPU` (Colab) or ensure a CUDA device is visible (local).
3. Provide a HuggingFace access token with **Read** scope in the first cell (`HF_TOKEN`).
4. Run all cells top to bottom. The notebook:
   - loads `unsloth/llama-3-8b-Instruct-bnb-4bit` in 4-bit precision,
   - extracts the harmful direction `f_harm` via difference-in-means on a held-out split disjoint from the evaluation set (see paper Section 3, "Note on the reference implementation" for why difference-in-means is used instead of a trained SAE in this version),
   - evaluates Attack Success Rate (ASR) and over-refusal rate with and without the Layer 3 projection active, using both a keyword heuristic and a Llama-Guard-3 classifier pass,
   - writes results to `cif_empirical_results.json`.
5. Copy the resulting JSON into `results/` and commit it — this is the artifact that should be cited alongside any number reported in the paper.

## Known limitations (see paper for full list)

- The Latent Bounding Hypothesis assumes harmful content is isolable as a single linear direction; this is untested against multi-objective or compositional attacks.
- The reference implementation uses raw-activation difference-in-means, not a trained Sparse Autoencoder, as a disclosed simplification for free-tier compute.
- Sequential Layer 2/Layer 4 model calls introduce latency overhead not yet measured here.
- No cross-layer ablation has been performed.

## Citation

If you use this code or build on this work, please cite via `CITATION.cff` or the archived DOI (Zenodo record — link added once published).

## License

MIT — see [LICENSE](LICENSE).
