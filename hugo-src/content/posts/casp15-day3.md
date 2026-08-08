---
title: "CASP 15 Day-3"
date: 2022-12-12T18:32:27+03:00
draft: false
tags: ["CASP"]
---

1. **RNA structure**



The standard metric used for assessing the RNA structure (in terms of watson pairs, non-watson pairs and coaxial stacking) was RMSD. The categories (easy, medium, hard) were made based on seqeunce homology. This CASP15, for the first time protein measures, such as GDT_S, TM scores and lDDT, (in addition to stereochemical clash scores) were piloted for RNA using a linear combination. The ranking by these individual scores correlated well with each other (except for the ranking using clash scores). The way multi-state RNA targets were assessed by comparing all predicted models against the experimenal structure and ranked the best. Deep learning methods have been developed to predict the 3D RNA structure but not so surprsingly, these methods did not perform well. This could be because of small size of training data (~1600 sequences), not using templates optimally or low diversity of sequences within MSA, to name a few. Some of the targets in CASP15 were designed RNA nanostructure. These have no MSA and surprisingly did well using top performing methods. The best methods were by made by seasoned RNA experts who used homology-based approaches and they did not use deep learning. It was found that canonical and wobble pairs can be predicted accurately (MCC > 0.9). Secondary structure can be predicted well sometimes. The open challenges that still remains are prediction of non-canonical pairs (MCC ~ 0.58), prediction of stacking interactions, prediction of bending helices topology of 4-way junctions and co-axial stacking of helices.

  2. **Ligand/Protein complexes**



This challenge included prediction of Protein-ligand complexes with proteins in PDB format and ligands in SMILES representation. Score used to evaluate include (a) RMSD (b) lDDT-PLI i.e. lDDT based on contacts between binding sites of ligands and protein/RNA. It was shown that a combination of these scores perform well. D3R (another challenge for protein-ligand prediction) proteins the protein structure and the ligands are drugs. In CASP, the protein and ligand need to be predicted together and there are mixed ligands considered. It was shown that small ions and endogenous non-flexible ligands were predicted very well. Current evaluation used averaging across mutliple ions, but maybe there is a better way to do a per-ligand prediction/evaluation. The methods that performed well either used template-based sampling and docking or transformer-based prediction. If not templates then VINA was used for docking. The key observations made in this session were that conformation of binding pickets (as well as side chains) are important for accurate prediction, modelling proteins and ligands separately could help, disimilar proteins bind in a similar fashion, deep learning could enhance information obtained from templates and templates can sometimes lead to an incorrect model.

  3. **Ensembles**



Showed validation for multi-state models by sub-sampling MSAs.
