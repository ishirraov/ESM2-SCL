# Viral Protein Classification via ESM-2 and Supervised Contrastive Learning

This repository contains the full implementation for the paper **"Reshaping ESM-2 Representation Geometry for Viral Protein Classification"** (Rao, Yale University, 2026).

## Overview

We benchmark three classification frameworks for distinguishing viral proteins from human host proteins using SwissProt-reviewed sequences:

1. **K-mer + Random Forest** — TF-IDF weighted 5-mer features as a sequence composition baseline
2. **ESM-2 + Logistic Regression** — mean-pooled embeddings from a frozen 650M parameter protein language model
3. **ESM-2 + Supervised Contrastive Learning (SupCon)** — a projection head trained on frozen ESM-2 embeddings using a supervised contrastive loss

The key finding is that SupCon raises classification accuracy on biologically ambiguous host-mimicry proteins from 69.4% (ESM-2 baseline) to **96.1%**, a 26.7 percentage-point gain concentrated precisely at the biological decision boundary.

## Data

Sequences were retrieved from [UniProt SwissProt](https://www.uniprot.org/) (manually reviewed entries only):

- **Viral**: 6,350 proteins from human-hosted viruses
- **Human**: 20,421 human proteins (non-viral)

Download the two FASTA files from UniProt and place them in the working directory as:
```
viral_BEST.fasta.gz
human_BEST.fasta.gz
```

## Requirements
```bash
pip install transformers datasets biopython accelerate umap-learn scikit-learn torch matplotlib seaborn
```

A CUDA-enabled GPU is strongly recommended for ESM-2 embedding extraction. The 650M parameter model requires approximately 6GB of GPU memory at float16 precision.

## Usage

Open and run `ESM2-SCL_FULL_CODE.ipynb` end to end. The notebook is divided into the following sections:

| Section | Description |
|---|---|
| Setup | Package installation and imports |
| K-mer classification | TF-IDF 5-mer features, Random Forest training, feature importance |
| ESM-2 embedding extraction | Batched inference, mean pooling, saving to `.npz` |
| Baseline classifier | Logistic regression probe on raw ESM-2 embeddings |
| Dimensionality reduction | UMAP and PCA manifold visualisations |
| Supervised Contrastive Learning | Projection head training, SupCon loss, logistic probe evaluation |
| Manifold evolution | Epoch-wise UMAP snapshots of the projected embedding space |
| Hard case analysis | Evaluation on 1,404 host-mimicry proteins |

ESM-2 embedding extraction is the most time-intensive step. Embeddings are saved automatically to `esm2_embeddings.npz` so that subsequent cells can be re-run without re-inference.

## Results

| Model | Overall accuracy | Viral recall | Host-mimicry accuracy |
|---|---|---|---|
| K-mer + RF | 84.0% | 40% | 55.5% |
| ESM-2 + LogReg | 98.0% | 96% | 69.4% |
| ESM-2 + SupCon | **98.69%** | **97%** | **96.1%** |

## Citation
```
@article{rao2026viral,
  title={Reshaping ESM-2 Representation Geometry for Viral Protein Classification},
  author={Rao, Ishir},
  institution={Yale University},
  year={2026}
}
```

## Acknowledgements

- Protein language model: [ESM-2](https://github.com/facebookresearch/esm) (Lin et al., 2023)
- Supervised contrastive loss: [SupCon](https://arxiv.org/abs/2004.11362) (Khosla et al., 2020)
- Sequence data: [UniProt SwissProt](https://www.uniprot.org/)
