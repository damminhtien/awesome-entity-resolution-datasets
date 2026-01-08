# Entity Resolution Benchmark

A curated list of commonly used **Entity Resolution (ER)** datasets, grouped by the *actual task they support*.
ER is an umbrella term-papers often mix **pairwise matching**, **deduplication/clustering**, and **knowledge-graph alignment** under one name.
This README keeps the taxonomy clean so your benchmarks stay honest.

> **Legend**
> - **EM (Entity Matching / Record Linkage)**: binary classification over candidate pairs *(match vs non-match)*
> - **Clustering ER**: group records into entity clusters *(dedup / multi-source resolution)*
> - **KG Alignment / Instance Matching**: align entities across two knowledge graphs *(sameAs links)*

---

## Table of Contents
- [1) Tabular Pairwise Entity Matching (Clean-Clean)](#1-tabular-pairwise-entity-matching-clean-clean)
- [2) Clustering / Multi-Source Entity Resolution (Scalable)](#2-clustering--multi-source-entity-resolution-scalable)
- [3) Classic Citation / Bibliographic ER](#3-classic-citation--bibliographic-er)
- [4) Knowledge Graph (RDF) Instance Matching / Alignment](#4-knowledge-graph-rdf-instance-matching--alignment)
- [5) Magellan-Style Small Domain Benchmarks](#5-magellan-style-small-domain-benchmarks)
- [Benchmarking Notes](#benchmarking-notes)
- [Recommended “Benchmark Ladder”](#recommended-benchmark-ladder)

---

## 1) Tabular Pairwise Entity Matching (Clean-Clean)

These datasets are typically **two-source** (table A vs table B) with a **ground-truth mapping**.  
A standard pipeline is: **blocking → feature/representation → pairwise classifier → evaluation (P/R/F1)**.

### 1.1 Restaurants (Fodor's ↔ Zagat's) [![Download](https://img.shields.io/badge/Download-file.zip-blue?style=for-the-badge)](https://www.cs.utexas.edu/~ml/riddle/data/restaurant.tar.gz)
- **Task**: EM (clean-clean)
- **Domain**: Restaurants
- **Records**: two sources of restaurant listings
- **Typical attributes**: name, address, city/area, phone (varies by version)
- **Ground truth**: list of matching pairs (same restaurant)
- **Why it's useful**: tiny, fast iteration; classic string/noise issues (abbreviations, punctuation, misspellings)
- **Where used**: record linkage / entity matching baselines
[![View Notebook](https://img.shields.io/badge/Click_here-Data_Analysis_%26_Baseline-purple?style=for-the-badge)](https://github.com/damminhtien/entity-resolution-benchmark/blob/main/restaurant/restaurant_analysis.ipynb)



### 1.2 Abt-Buy [![Download](https://img.shields.io/badge/Download-file.zip-blue?style=for-the-badge)](https://dbs.uni-leipzig.de/files/datasets/Abt-Buy.zip)
- **Task**: EM (clean-clean)
- **Domain**: E-commerce products
- **Sources**: Abt ↔ Buy
- **Typical attributes**: product name/title, description, manufacturer/brand, price
- **Ground truth**: matching product pairs
- **Why it's useful**: realistic product matching with messy titles, model numbers, brand tokens

### 1.3 Amazon-GoogleProducts [![Download](https://img.shields.io/badge/Download-file.zip-blue?style=for-the-badge)](https://dbs.uni-leipzig.de/files/datasets/Amazon-GoogleProducts.zip)
- **Task**: EM (clean-clean)
- **Domain**: E-commerce products
- **Sources**: Amazon ↔ Google Products
- **Typical attributes**: title, description, manufacturer, price (varies by dump)
- **Ground truth**: matching product pairs
- **Why it's useful**: classic noisy product listings across marketplaces

### 1.4 DBLP-ACM  [![Download](https://img.shields.io/badge/Download-file.zip-blue?style=for-the-badge)](https://dbs.uni-leipzig.de/files/datasets/DBLP-ACM.zip)
- **Task**: EM (clean-clean)
- **Domain**: Bibliographic records (papers)
- **Sources**: DBLP ↔ ACM
- **Typical attributes**: title, authors, venue, year
- **Ground truth**: matching paper pairs
- **Why it's useful**: strong textual similarity but with author formatting noise

### 1.5 DBLP-Scholar [![Download](https://img.shields.io/badge/Download-file.zip-blue?style=for-the-badge)](https://dbs.uni-leipzig.de/files/datasets/DBLP-Scholar.zip)
- **Task**: EM (clean-clean)
- **Domain**: Bibliographic records
- **Sources**: DBLP ↔ Google Scholar-like dump
- **Typical attributes**: title, authors, venue, year
- **Ground truth**: matching paper pairs
- **Why it's useful**: much more unbalanced size; harder blocking; long-tail noise

> **Tip**: Many of the above appear in the **Database Group Leipzig** “ER Benchmark Datasets” collection as ready-to-download files.

---

## 2) Clustering / Multi-Source Entity Resolution (Scalable)

These datasets come with **cluster ground truth** (each record belongs to a real-world entity cluster).
They are used to test:
- **blocking at scale**
- **graph-based clustering**
- **deduplication quality**
- **runtime / memory behavior**

### 2.1 Affiliations [![Download](https://img.shields.io/badge/Download-file.zip-blue?style=for-the-badge)](https://dbs.uni-leipzig.de/files/datasets/affiliationstrings.zip)
- **Task**: Clustering ER (single-source)
- **Domain**: Academic affiliations (strings)
- **Ground truth**: clusters of equivalent affiliations
- **Why it's useful**: heavy abbreviation + reordering + multilingual variants; great for string normalization + clustering

### 2.2 Geographic Settlements [![Download](https://img.shields.io/badge/Download-file.zip-blue?style=for-the-badge)](https://dbs.uni-leipzig.de/files/datasets/geographicalSettelments.zip)
- **Task**: Clustering ER (multi-source)
- **Domain**: Places / settlements
- **Sources**: multiple sources (often 4)
- **Typical attributes**: place name, latitude, longitude (varies)
- **Ground truth**: clusters of the same real-world location
- **Why it's useful**: mixes text similarity with geo proximity; reveals “near-duplicate but different place” traps

### 2.3 MusicBrainz (20K, 200K, 2M, 20M)
- **Task**: Clustering ER (multi-source, scalable)
- **Domain**: Music recordings
- **Sources**: typically 5 sources (synthetic duplicates across sources)
- **Typical attributes**: artist, title, album, year, length (dataset-dependent)
- **Ground truth**: clusters of the same recording across sources
- **Why it's useful**: **same schema**, enormous scale options; ideal for stress-testing blocking + clustering

### 2.4 North Carolina Voters (5M, 10M)
- **Task**: Clustering ER (multi-source, very large)
- **Domain**: Population / voters
- **Sources**: multiple sources (constructed as N sources × 1M each in common releases)
- **Typical attributes**: first name, last name, suburb/area, postcode (varies)
- **Ground truth**: clusters of the same person
- **Why it's useful**: extreme scale + high ambiguity (common names); reveals precision/recall trade-offs in blocking

> **Tip**: For clustering datasets, report **both** quality and efficiency:
> - Quality: pairwise-F1, B³-F1, ARI/V-measure (pick what your papers/community use)
> - Efficiency: candidate count, time, memory, scalability curve

---

## 3) Classic Citation / Bibliographic ER

These are “old but gold” datasets widely referenced in early ER / record linkage literature.
They often come in multiple variants and preprocessings; always record the exact version you used.

### 3.1 Cora
- **Task**: citation matching / deduplication (often clustering)
- **Domain**: academic citations
- **Typical attributes**: title, authors, venue, year (extracted/noisy)
- **Ground truth**: citations grouped by the same underlying paper (version-dependent)
- **Why it's useful**: extreme noise from extraction + inconsistent formats

### 3.2 CiteSeer (Citation datasets)
- **Task**: citation matching / disambiguation (often clustering)
- **Domain**: academic citations from CiteSeer-like corpora
- **Typical attributes**: similar to Cora (citation fields)
- **Ground truth**: clusters/pairs depending on release
- **Why it's useful**: classic benchmark for disambiguation under noisy metadata

---

## 4) Knowledge Graph (RDF) Instance Matching / Alignment

These benchmarks align entities across two knowledge graphs with very different schemas.
They test **schema-agnostic matching** and **relation/neighbor evidence**.

### 4.1 Restaurant (OAEI-style instance matching)
- **Task**: KG instance matching / alignment
- **Domain**: Restaurants
- **Input**: two RDF datasets
- **Ground truth**: sameAs-style mappings
- **Why it's useful**: small, easy to iterate; still “real RDF” (properties + relations)

### 4.2 Rexa ↔ DBLP
- **Task**: KG instance matching / alignment
- **Domain**: Publications and authors
- **Input**: two large RDF graphs
- **Ground truth**: mappings for publication and/or author entities (release-dependent)
- **Why it's useful**: realistic scholarly KGs; strong signals in coauthor/venue neighborhood

### 4.3 BBCmusic ↔ DBpedia
- **Task**: KG instance matching / alignment
- **Domain**: Music entities (artists, bands, etc.)
- **Input**: BBCmusic KG aligned to DBpedia
- **Ground truth**: sameAs links
- **Why it's useful**: massive schema heterogeneity + many types/attributes

### 4.4 YAGO ↔ IMDb
- **Task**: KG instance matching / alignment
- **Domain**: Movies and related entities
- **Input**: two huge KGs
- **Ground truth**: sameAs links for movie entities and related nodes (release-dependent)
- **Why it's useful**: web-scale alignment; great for token + neighborhood + blocking research

> **Tip**: KG alignment is usually evaluated with P/R/F1 over links, but effective systems rely on:
> - token blocking (label/alias tokens)
> - neighbor similarity (shared relations or linked literals)
> - iterative propagation (graph-based reinforcement)

---

## 5) Magellan-Style Small Domain Benchmarks

These are convenient “small-to-medium” datasets often used in supervised entity matching work,
sometimes packaged as ready-made train/validation/test splits.

### 5.1 Bikes / Movies / Books (Magellan-style domains)
- **Task**: typically EM (pairwise)
- **Domain**: consumer items and media
- **Ground truth**: labeled pairs
- **Why it's useful**: fast experimentation across multiple domains; good for ablation studies

> **Note**: Names like “Books” and “Movies” can appear in multiple repositories/testbeds.
> Always cite the repository/release and checksum the files in your experiments for reproducibility.

---

## Benchmarking Notes

### A) Be explicit about the ER problem you solve
- If you evaluate on **pairwise-labeled** EM datasets, don't claim you solved “clustering ER”.
- If you evaluate on clustering datasets, report cluster metrics and candidate explosion behavior.

### B) Standard metrics (pairwise EM)
\[
P=\frac{TP}{TP+FP},\quad R=\frac{TP}{TP+FN},\quad F1=\frac{2PR}{P+R}
\]

### C) Reproducibility checklist
- dataset version + download URL
- preprocessing steps (normalization, tokenization, missing values)
- blocking strategy and candidate count
- train/valid/test split strategy (random vs time-based vs entity-based)
- seeds, hardware, runtime

---

## Recommended “Benchmark Ladder”

A practical evaluation ladder that tells a convincing story in a paper or internal report:

1. **Tiny EM sanity check**: Restaurants (Fodor–Zagat)  
2. **Product EM realism**: Abt-Buy + Amazon-GoogleProducts  
3. **Bibliographic EM**: DBLP-ACM + DBLP-Scholar  
4. **Scalable clustering**: MusicBrainz (scale up to 20M)  
5. **Extreme-scale clustering**: North Carolina Voters (5M/10M)  
6. **Schema-heterogeneous KG alignment**: BBCmusic–DBpedia + YAGO–IMDb  
7. **Scholarly KG alignment**: Rexa–DBLP  

This ladder gradually increases:
- noise
- class imbalance
- schema mismatch
- scale
- graph complexity

---

## Credits & License
This document is a curated guide. Dataset licenses vary by source-always follow the original dataset license/terms.
