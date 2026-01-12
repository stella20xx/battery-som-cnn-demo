# Battery Cross-Cell Health Transfer with SOM–CNN

Label-free, physics-aware transfer learning for lithium-ion battery health
staging across cells, using Self-Organizing Maps (SOM) as a teacher and a
lightweight CNN as a student.

This demo uses the public **NASA PCoE Li-ion Aging** datasets (cells B0005,
B0006, B0007) to show how SOM centroids and CNN embeddings can be reused
across cells without any target labels.

<p align="center">
<strong>docs/battery_cross_cell_som_cnn_demo.pdf</strong> contains all the
figures and slides described below.
</p>

---

## Problem

- Battery state-of-health (SOH) estimation usually assumes full labels and
  cell-specific supervised training.
- Real-world fleets have **no ground-truth health labels** for new cells.
- Goal: **transfer health-stage knowledge from a labeled reference cell
  (B0005) to new cells (B0006, B0007) without target labels.**

---

## Data

- **Dataset:** NASA PCoE Li-ion Aging (public benchmark)
- **Cells:** B0005 (reference, used for SOM + CNN training),
  B0006 & B0007 (unlabeled targets)
- **Signal:** per-cycle voltage curves under aging protocols
- **Task:** zero-label **cross-cell health stage inference**

---

## Method (SOM → CNN Embedding + Domain Projection)

1. **9-D structural descriptor**

   Each cycle’s voltage curve is mapped into a 9-D feature vector that
   captures morphology (plateau lengths, slopes, curvature, hysteresis,
   etc.), preserving physically meaningful degradation behavior.

2. **PCA → SOM clustering**

   - PCA reduces to 6-D while preserving dominant variance.
   - An unsupervised SOM is trained on B0005 to form stable,
     interpretable clusters.
   - An Elbow criterion is used to automatically select the number of
     health stages (K).

3. **Cross-cell projection**

   - B0006/B0007 cycles are projected into the **same SOM feature space**
     via prototype matching.
   - SOM weight vectors (centroids) are **reused across cells**: no
     re-training, no labels on B0006/B0007.

4. **CNN embedding (optional student)**

   - A lightweight 1D CNN is trained on B0005 with SOM labels as
     supervision (teacher–student).
   - Its latent embedding is used to visualize and separate health
     stages via t-SNE/UMAP.

Key properties:

- **Fully self-supervised:** no manual SOH labels are required.
- **Physics-aware:** features are designed from battery degradation
  mechanisms instead of raw sequence fitting.
- **Transfer-ready:** SOM centroids are reused across cells that share
  the same sensing physics.
- **Explainable:** SOM clusters correspond to interpretable
  degradation modes.

---

## Results (see PDF)

Highlights from `docs/battery_cross_cell_som_cnn_demo.pdf`:

- SOM weight-vector topology learned on B0005 is **reused unchanged** on
  B0006/B0007, giving a consistent 3-stage degradation pattern.
- Cross-cell reuse of SOM centroids enables **zero-shot transfer**:
  B0006/B0007 cycles are staged without any target labels.
- CNN latent embeddings show that cycles from different cells occupy a
  consistent, low-dimensional degradation manifold, supporting **early
  prognosis** and fleet-wide monitoring.

---

## Status

This repository currently serves as a **documentation/demo hub** for the
battery cross-cell SOM–CNN pipeline. Code and runnable notebooks will be
added progressively as the framework is cleaned and released.

---

## Data source

If you would like to reproduce the results, please download the NASA PCoE
battery datasets from the official site:

- NASA Ames Prognostics Center of Excellence – Li-ion Aging Datasets

Please cite the NASA PCoE dataset accordingly in your own work.
# battery-som-cnn-demo
