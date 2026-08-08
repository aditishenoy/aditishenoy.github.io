---
title: "CASP 15 Day-2"
date: 2022-12-11T15:02:11+03:00
draft: false
tags: ["CASP"]
---

### Overview talk 1 : Mapping machine learning to protein structure problems (John Jumper)

The talk broadly covered 3 categories:

  1. **Generative modelling (Diffusion models):**  
Evidence for multi-state modelling using sub-sampling or clustering MSAs has been shown by [Diego del Alamo 2022](https://elifesciences.org/articles/75751#s3). With recent success of generative modelling, there is, scope to use diffusion models to predict multiple states in proteins. This is done by adding noise to the input (protein coordinates) which passes through the network and produces a prediction. This incldues forward stochastic differential equations (SDE) (data to noise) and reverse SDE (noise to data).Examples where these have been applied to proteins are ([Anand and Achim 2022](https://arxiv.org/abs/2205.15019), [Watson 2022](https://www.biorxiv.org/content/10.1101/2022.12.09.519842v1) and [Ingraham 2022](https://www.biorxiv.org/content/10.1101/2022.12.01.518682v1)). The challenge to evaluate multi-state proteins remains.

  2. **Language models (LM) for proteins:**  
There are two types of LM (i) Masked Language modelling (e.g. BERT - used to understand text) or (ii) Autoregressive model (predicts the last word - used for generating text). A LM where tokens in a sequence are masked and the training objective is to predict missing tokens. This requires a reasonable understanding of the world and the language. Masked LM and coupling analysis were described to be highly analogous. Given a part of the sequence, the network should be able to rest of the sequence. This could be done using either the approach as in [MSA Transformer](https://www.biorxiv.org/content/10.1101/2021.02.12.430858v3) or co-training as another objective for AlphaFold. Current models are not at par with performance of MSA-based methods (described in more detail below in the section on assessment) and perform badly for proteins having a low Neff score as well. Another downside to LM is that these are very large models (they need to memorize aspects of the protein like protein families, evolution). There is, however, potential for protein LM (PLM) due to their fast speed, ease of use and possibility to capture unusual conservation patterns that can’t be obtained from the MSAs. An increase in performance of autoregressive language models were observed as the number of parameters were increased (i.e. size of retrieval database was large) as described in the [Deepmind blog](https://www.deepmind.com/blog/improving-language-models-by-retrieving-from-trillions-of-tokens). So eventually embeddings will further grow in size to learn more complex representations of contacts and structure directly from the LM.

  3. **Scaling hypothesis:**  
The LM has three main inputs (i.e. number of parameters, compute time for training, and amount of data seen during training). This hypothesis states that a power law of perplexity can be fit to these 3 inputs. Essentailly, there is only so far an architecture can drive the performance and the real power comes with more data. [Kaplan 2020](https://arxiv.org/abs/2001.08361). Scaling to billions of parameters for sequence generation has been shown in [RITA](https://arxiv.org/pdf/2205.05789.pdf) and [ProGen2](https://arxiv.org/pdf/2206.13517.pdf).




The talk ended with problems that could use ML solutions:

  * Incorporating spare experimental data (e.g. from chemical cross-linking to identify protein-protein interactions)
  * Handling large complexes
  * Interpreting cryo-ET data
  * Informing interaction screenings with structures which do not interact (negatives)
  * Mutational and PTM-associated effects



### Assessment talks:

  1. **Protein Tertiary structure (Dan Rigden)** \- Most highly ranked methods are AF2-based. They differ based on use of diverse MSAs, dividing into domains, ranking, manual selection and refinement. No single method stands out (like CASP14). The informal submissions from DeepMind are at par with best performing method in CASP15. Most human predictions are as good as server predictions. PLM are useful for crysal structures and could be useful in cryo-EM fitting. The drop in performance from CASP14 to CASP15 could be due to the face that the targets were smaller in size, there were more alpha helices than beta sheets and the number of effective sequences (Neff score) was very low for difficult targets.

  2. **Protein assemblies, the CASP perspective (Ezgi Karaca)** \- Targets were divided 3 categories depending on if templates existed (easy), partially existed (medium) or not existing (hard). A combination of global (i.e. oligomeric lDDT, TM score), local (interface contact scores (~F1), interface patch scores (~Jaccard)). The local and global scores do not always agree e.g for cases with a large monomer in the complex would dominate the TM score of the complex. Examples between a big gap between the naive AlphaFold-Multimer (AFM) and top predictors are where there is wrong level of compaction, internal rotation (180) or incorrect interfaces predicted in dimers. For big complexes, the asymmetric unit modelling not enough to explain assembly geometry. For nanobody cases, different groups randomly rank at top. No single approach worked well - clustering key terms of abstracts of best performing methods gave the following keywords - non-paired MSA (surprising?), custom MSA, improved sampling and number of templates used. AF-2 ranking performs well to rank models. Tertiary structural quality correlates well to quaternary structure quality. Future perspectives include an approach similar to data-driven docking - pooling models from different approaches (what resources used, how was MSA selected)- clustering based on structural similarity and AFM scores (plddt, pTM, ipTM).

  3. **Protein assemblies, the CAPRI perspective (Marc Lensink)** \- The CAPRI assessment is an community consensus assessment performed along with experimentalists. The scores taken into consideration to define 4 categories (incorrect, acceptable, medium, high) use interface-based Fnat, RMS and ligand/receptor RMS (LRMS). Most observations were same as above. Certain targets showed a domain swapping. There was a high relationship between model quality and target difficulty. Nano bodies bind to different locations of same protein and 3 different antibodies had different binding modes.




### Predictions talks:

  1. **UM_TBM /Zheng (Peter Freddolino / Wei Zheng)**  
Diverse MSAs (~10) were generated using DeepMSA2. plDDT was used to rank which MSAs to use. Some sort of combinatorials were used for pairing MSA for heteromers (I missed this, does anyone remember?). The chosen MSA was passed through AlphaFold, AttentionPotential and DeepPotential. Geometric constraints were used as constraints. Finally, the predicted constraints were based through DeepFold.

  2. **Yang server/Yang Multimer (Jianyi Yang)** trRosettaX was developed based on Evoformer from AlphaFold.Pairing MSA was turned off for multimers. Proteins were cut at the level of domains (using UniDoc in-house) and the MSAs of the domains were assembled together. The disorder residues were removed for large complexes. Curating MSA helps predicting hard targets; PDB templates not important for tertiary structure prediction; homologous templates important for big protein assembly.

  3. **PEZYFoldings (Toshiyuki Oda)** Conducting diverse MSA generation (exhaustive sequence similarity search PZLAST jackhmmer and hhblits) by clustering at ~90% and then selecting query sequence using ~60-80% thresholds. The MSA was based through retrained AF but original weights. The top ranked model was selected using plddt for monomer and (0.8 _iptm and 0.2_ ptm) for multimer.

  4. **Venclovas group (Kliment Olechnovic / Justas Dapkunas)** Models were constructed using AlphaFold and rigid body docking (FTDock and Hex for hetero-oligomers, SAM for homo-oligomers) was performed. Model ranked using VoroIF-jury (Interface-focused scoring using Voronoi-tesselations derived interface contacts). The models were structually analyzed for homologous proteins. AF produced high quality models - does not model large complexes - modelled in parts.

  5. **Wallner group (Bjorn Wallner)** The diversity of models generated by AF is not very high,however models with different interfaces do exist in the predictions. Using different random seed, number of recycles (=21) or by perturbing input/MSA can increase diversity of protein models generated. Turning on dropout layers at inference is another way to do this. The default MSA from AFM was used. There were no dropouts in the structural module turned on. A large spread of model quality was found and the results were ranked using CAPRI criteria. Increased sampling seems to work for target case of nano-body protein interactions.




### Overview talk 2 : Protein structure prediction: the past and the future (Minkyung Baek)

The protein structure prediction problem has been using evolutionary information in the form of coevolution to determine contacts and hence the protein structure of the protein. Deep learning has played a massive role in moving from MSA to contact maps (subsequently distance maps) and then onto 3D structure. Early approaches used methods like ResNet which produced a one-shot prediction and the model has to undergo a series of refinement to obtain the final model. With AlphaFold, the model was generated by extracting information directly from the raw MSA iteratively in an end-to-end fashion. The question Baek asked was if it was possible for academia to replicate the efforts of AlphaFold. Her efforts resulted in RoseTTAFold, a three track (processing sequence, distance and coordinate information) neural network developed to produce protein structure prediction with end-to-end learning. The rest of the talk focussed on the following application areas using RoseTTAFold -

  1. **Large scale in-silico PPI screening** Protein-protein interactions among the core eukaryotic protein complexes in the yeast proteome were predicted using RoseTTAFold. These interactions were evaluated and showed performance equivalent to experimental methods. [Humphreys et al 2021](https://www.science.org/doi/10.1126/science.abm4805)

  2. **Nucleic acid structure and interaction prediction** The single trained network RoseTTAFoldNA [Baek et al 2022](https://www.biorxiv.org/content/biorxiv/early/2022/09/10/2022.09.09.507333.full.pdf) for protein-RNA, protein-DNA and RNA complex prediction.

  3. **De-novo functional protein design** Two very well promising methods were described for generating protein sequences based on structure - (1) using Hallucination [Wicky et al.](https://www.bakerlab.org/wp-content/uploads/2022/09/Wicky_etal_Science2022_Hallucinating_symmetric_protein_assemblies.pdf) (2) with diffusion called RF-Diffusion in [Watson 2022](https://www.biorxiv.org/content/10.1101/2022.12.09.519842v1.full.pdf).




### Key takeaways from Day 2:

  * All top-performing methods use AlphaFold (differences in MSAs, templates, dropout, manual selection, ranking confidence)
  * Aggressive sampling helps (maybe the most straightforward method among all the top-performing methods)
  * Protein-language models do not work well (even for proteins with low Neff score) - need more data and more parameters aka scaling hypothesis
  * Non-paired alignments work better for some targets
  * More collaboration between CASPs by sharing resources (maybe through ELIXIR), zoom meetings, journal clubs, standardized benchmark, training/validation/testing datasets
