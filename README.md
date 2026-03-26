# Context‑Aware Drug Embedding Pipeline
<br>

This repository implements a complete, end‑to‑end pipeline for generating context‑specific molecular embeddings from LINCS L1000 gene‑expression data, integrating:
- RNA‑seq preprocessing
- STRING protein–protein interaction networks
- Context‑specific subgraph construction
- Graph neural network (GNN) embedding generation
- UMAP visualisation
- Drug‑class neighbourhood analysis

The workflow is modular and each stage is implemented as a Jupyter notebook, forming a clear linear pipeline.
<br>

----
# Table of Contents

## [📄 Notebook Summary](#notebook-summary-1)
### [💊 Preprocess LINCS](#1-preprocess-lincs)
### [🧬 Preprocess RNA‑seq](#2-preprocess-rna-seq)
### [🧵 Preprocess STRING](#3-preprocess-string)
### [🔎 Analyse Base Graph](#4-analyse-base-graph)
### [🛠️ Generate Context Graphs](#5-generate-context-graphs)
### [🤖 Generate Embeddings](#6-generate-embeddings)
### [🔎 Analyse ID‑Level Embeddings](#7a-analyse-id-level-embeddings-7a_analyse_id_embeddings)
### [🔎 Analyse Name‑Level Embeddings](#7b-analyse-name-level-embeddings-7b_analyse_name_embeddings)

## [📚 Citation](#citation)
<br>

----
# 📄 Notebook Summary

## 1. Preprocess LINCS
Clean and harmonise LINCS L1000 metadata and perturbagen annotations.

### Key steps
- Load raw LINCS metadata.
- Standardise perturbagen IDs and names.
- Filter for high‑quality profiles.
- Export:
- df_perturbagen_info.csv
- lists of drug classes (statins, opioids, profens, HDAC inhibitors)

These files are used throughout the pipeline.

## 2. Preprocess RNA‑seq
Prepare expression matrices for downstream graph construction.

### Key steps
- Load raw RNA‑seq matrices.
- Normalise and filter genes.
- Align gene identifiers with STRING.
- Export cleaned matrices per experimental context.

## 3. Preprocess STRING
Build the base protein–protein interaction (PPI) graph.

### Key steps
- Load STRING interactions.
- Filter by confidence score.
- Convert to a weighted NetworkX graph.
- Export:
- graph_base.pkl
- node lists
- edge‑weight distributions

This graph is the backbone for all context‑specific subgraphs.

## 4. Analyse Base Graph
Goal: Characterise the global PPI network and landmark gene coverage.

### Analyses
- Largest connected component extraction.
- Spring‑layout coordinate generation.
- Overlay of landmark vs. non‑landmark nodes.
- Edge‑weight histograms.
- Degree distributions.
- GO‑term coverage comparison between landmark and non‑landmark genes.

This validates that landmark genes provide reasonable functional coverage.

## 5. Generate Context Graphs
Build subgraphs representing each experimental context.

### Key steps
- For each perturbagen × timepoint:
- Select top DE genes.
- Extract induced subgraph from the base STRING network.
- Annotate graph metadata (name, timepoint, perturbagen ID).

### Export
- Graphs for CID (per experiment)
- Graphs for MEAN (averaged per perturbagen)
- Graphs for NAME (averaged per perturbagen name)

These graphs are the direct input to the GNN.

## 6. Generate Embeddings

Train a graph neural network and embed each context graph.

### Key steps
- Convert NetworkX graphs → PyTorch Geometric format.
- Train a GNN encoder (e.g., GraphSAGE / GCN).
- Generate embeddings for:
- CID‑level graphs
- MEAN‑level graphs
- NAME‑level graphs

### Export:
- list_emb.pkl — list of embedding matrices
- list_pyg.pkl — list of graph objects with metadata

These embeddings are analysed in notebooks 7A or 7B.

## 7A. Analyse ID‑Level Embeddings
Visualise and interpret embeddings generated at the perturbagen ID level.

### UMAP projection
- Compute consensus embedding across all GNN runs.
- Project to 2D using cosine‑metric UMAP.
- Merge with perturbagen metadata.
### Drug‑class visualisation
- Overlay statins, opioids, profens, HDAC inhibitors, and treatments (Halo, Nita, Paro).
- Label selected drugs with collision‑avoiding text placement.
### Neighbour‑composition analysis
For each treatment:
- Identify its 10 nearest neighbours in UMAP space.
- Compute the proportion of neighbours belonging to each drug class.
- Compare:
- % of neighbours of type X
- % of all drugs that are type X

### Outputs
- Stacked barplots of neighbour composition.
- Scatterplots comparing neighbour‑enrichment vs. global frequency.

## 7B. Analyse Name‑Level Embeddings
Goal: Repeat the 7A analysis using perturbagen name embeddings.
This provides a higher‑level view where multiple IDs for the same drug collapse into a single embedding.
Analyses mirror 7A:
- UMAP projection
- Drug‑class overlays
- Neighbour‑composition analysis
- Enrichment scatterplots
This allows comparison between ID‑level and NAME‑level embedding behaviour.

----
# 📄 Citation
If you use this pipeline in academic work, please cite:
- STRING database
- LINCS L1000 dataset
- UMAP
- PyTorch Geometric