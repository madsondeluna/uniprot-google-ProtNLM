# ProtNLM

## Introduction

UniProt’s [Automatic Annotation pipeline](https://www.uniprot.org/help/automatic_annotation) automatically classifies and annotates unreviewed records in UniProtKB.

Google’s **ProtNLM** (Protein Natural Language Model) contributions to this pipeline are labeled **Google:ProtNLM**.

Example: [A0A2Z4IEP2](https://www.uniprot.org/uniprotkb/A0A2Z4IEP2/entry#names_and_taxonomy).

ProtNLM is a [transformer model](https://proceedings.neurips.cc/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Paper.pdf) trained to **predict the protein name** from the protein’s amino acid sequence.

## ProtNLM methodology

ProtNLM is a method used by UniProt to **automatically annotate uncharacterized protein sequences**. It predicts a short textual description for proteins based solely on their amino acid sequence, using a [sequence-to-sequence](https://en.wikipedia.org/wiki/Seq2seq) model.

### Sequence annotation as a machine learning problem

The sequence annotation problem has similarities to other machine learning tasks. For example, predicting a protein’s name from its amino acid sequence is analogous to predicting a title (or caption) for an image or document.

![ProtNLM input-output figure](<ProtNLM _ UniProt help _ UniProt_files/protnlm-UNProtein input-output figure.png>)

ProtNLM uses a seq2seq model based on the [T5X framework](https://github.com/google-research/t5x).

### Notable challenges

This task can present challenges similar to captioning images or documents. One protein may have multiple valid names if each highlights different functional domains. For instance, sequence [Q58842](https://www.uniprot.org/uniprotkb/Q58842/entry) can be named:

- “Formaldehyde-activating enzyme”
- “3-hexulose-6-phosphate synthase”
- “Bifunctional enzyme Fae/Hps”

Evaluating automatically generated names that differ from UniProt’s can be difficult. Corroborating a proposed description often requires external evidence, bioinformatics tools or lab experiments.

### Data processing and model validation

ProtNLM was trained on sequence–name pairs from UniProt (Swiss-Prot and TrEMBL), filtering out low-quality or uninformative names as advised by UniProt experts.

Validation combines automated metrics and manual review by professional biocurators. Special subsets (e.g., low-identity sequences) were tested, and an evidence file based on traditional bioinformatics methods is released alongside predictions.

### Leveraging additional protein information

The simplest ProtNLM model uses only the amino acid sequence (UniProt release 2022_04). Recent models incorporate:

1. **Organism information**  
   Proteins from different kingdoms often follow naming conventions (e.g., “Ovule protein” in plants).  
   ![ProtNLM with organism input-output figure](<ProtNLM _ UniProt help _ UniProt_files/protnlm-UNProtein with organism input-output figure.png>)

2. **Predicted secondary structure**  
   Extracted from AlphaFold models in UniProt.  
   ![ProtNLM with secondary structure input-output figure](<ProtNLM _ UniProt help _ UniProt_files/protnlm-UNProtein with secondary structure input-output figure.png>)

### Ensembling

To boost accuracy, UniProt releases use ensembles:

- **2022_05**: Ensemble of 3 sequence-only models + 3 sequence+organism models. New names are applied if the ensemble score exceeds the original by > 0.1.  
  ![ProtNLM ensemble input-output figure](<ProtNLM _ UniProt help _ UniProt_files/protnlm-UNProtein ensemble input-output figure.png>)

- **2023_01**: All ProtNLM annotations updated; predictions released only when score > 0.2. Enhanced post-processing and an automatic corroboration pipeline select the recommended name.

- **2023_02 onward**: Added a 7th model using sequence, organism and secondary structure. Models retrained from scratch, with fine-tuning on updated biocurator-filtered data.

### Curation of predictions

Machine learning models can err. Users are encouraged to report issues via the UniProt [help desk](https://www.uniprot.org/update).

One way to validate a predicted name is to:

1. Add the query sequence (e.g., D2GX75) to your UniProt basket  
2. Search for the predicted name “Adhesion G protein-coupled receptor V1” via the [UniProt search feature](https://www.uniprot.org/uniprotkb?query=protein_name%3A%22Adhesion%20G%20protein-coupled%20receptor%20V1%22)  
3. Add reviewed entries with that name to the basket  
4. Perform sequence alignment and examine conserved features (e.g., 7 transmembrane domains in GPCRs) to assess prediction accuracy  
   ![ProtNLM evidence via alignment figure](<ProtNLM _ UniProt help _ UniProt_files/protnlm-UNProtein evidence via alignment figure.png>)

Previous releases provided a Colab notebook with precomputed alignments ([protnlm_evidencer_uniprot_2023_01.ipynb](https://colab.sandbox.google.com/github/google-research/google-research/blob/master/protnlm/protnlm_evidencer_uniprot_2023_01.ipynb)), now deprecated in favor of UniProt’s built-in search and alignment tools.

## References

- [Preprint (2022_04)](https://storage.googleapis.com/brain-genomics-public/research/proteins/protnlm/uniprot_2022_04/protnlm_preprint_draft.pdf)  
- [YouTube video](https://www.youtube.com/watch?v=FLkoaDJBC54)  
- [Colab notebook to query ProtNLM (2022_04)](https://colab.research.google.com/github/google-research/google-research/blob/master/protnlm/protnlm_use_model_for_inference_uniprot_2022_04.ipynb)  
- [Explore all UniProtKB entries with ProtNLM annotations](https://www.uniprot.org/uniprotkb?query=%28source:google%29)

> _Page last modified: Thu Mar 21 2024_
