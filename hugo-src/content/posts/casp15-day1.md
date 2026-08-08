---
title: "CASP 15 Day-1"
date: 2022-12-10T11:59:17+03:00
draft: false
tags: ["CASP"]
---

1. **Expanding criteria for review**



John Moult proposes a way to add evaluation criteria (e.g. reproducibility, robustness, training bias, suitability for benchmarking) for method-based and application-based papers. To keep up with the fast-pased research in the field, criteria like these could be useful to suggest what has been done (e.g. using protein language models for proteins with shallow alignments or graph transformers for protein-ligand complex prediction and where we fall short

  2. **Evaluating targets during CASP15**



Andriy Kryshtafovych described the how domains were defined to evaluate targets during CASP15 (this was done differently from CASP14 where homology was manually examined). First, domain definition programs (like Domain, DomainParser2,SWORD) were used followed by homology search (PSIBLAST, HHsearch). These two steps were iteratively performed with manual inspection to produce Evaluation Units (EVUs). The predictions were run on these definitons and then evalauted using Grishin plots.

(TIL - Grishin plots are ways to compare how the performance of the individual domains perform as compared to the prediction of the entire complex. This is given by the GDT_TS score of domains (weighted and averaged) on y-axis and GDT_TS score of the entire complex on the x-axis)

Based on homology searches, these were then divided into 4 categories based on difficulty (Template-based modelling (TBM) easy, TBM-hard, TBM/Free modelling (FM) and FM). The average target difficulty across the CASP is becoming more difficult (low sequence identity % and low coverage in PDB). The quality of models is inversely correlated with the number of effective sequences (i.e. how deep the MSA is)

  3. **CAMEO - Weekly automated evaluation of prediction methods**



CAMEO consists of automated pipeline for selecting targets by first removing homology (99% sequence identity clustering, if 2 proteins not in same cluster then included, additional homology reduction for ligands) and running them on automated servers for protein complex/protein-ligand complexes prediction. The current challenges include prediction of stoichiometry and prediction of full complexes. The current evaluation critera include QS-score and lDDT-PMI*.

  4. **Disorder evaluation (CAID)**



Data obtained from DisProt (manual annotation). Each method is containerized and all the features produced by CAID team. The time varies for these evaluations among various methods. Mostly, AlphaFold predicts disorder regions well (plDDT < 50) with some exceptions for other categories.

Question of the hour - How important are the quality of the MSAs for predictions?

  * Did anybody find any resources on this?
