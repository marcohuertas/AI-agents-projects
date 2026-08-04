# AI-agents-projects
Repository containing various AI-agent projects with QSP applications

# Motivation
In the every-day practice of QSP, modelers frequently rely on existing published models as starting points for new modeling development. Manually searching BioModels and reviewing SBML code is time-consuming. This project explores whether an agentic RAG system can automate this search, returning not just a model reference but the actual equations, enabling a modeler to quickly assess whether a retrieved model is suitable for their needs.

# Purpose
This repository contains projects related to building AI agent workflows that can be used in activities usually encounter in QSP (quantitve systems pharmacology) workflows.
They represent hands-on projects that I have done to learn how to build AI-agent workflows.

# Project: Agentic RAG for model retrieval
## Description
This project consists of building an Agentic RAG that will retrieve the equations from a model that best matches a user's query.
The model uses a pre-build dataset consisting in 120 mathematical model descriptions obtained from BioModels (https://www.biomodels.org/) and that is housed at HuggingFace (https://huggingface.co/datasets). The code used to create this dataset is also provided in this repository.

## Dataset
The models selected for this curated dataset correspond to those retrieved from BioModels using the query:
 ```
 *:* AND modellingapproach:"ordinary differential equation model" AND modelformat:"SBML" AND submitter_keywords:"*oncology"
 ```
 resulting in 120 models. 
 
 For each model and abstract was retrieved from PubMed (https://pubmed.ncbi.nlm.nih.gov/), when possible, to augment the model description provided by the authors in BioModels.
 The SBML code for each model was converted to Tellurium/Antimony (https://github.com/sys-bio/tellurium) code, which more human-readable.
 The final dataset is stored in HuggingFace's dataset repository under **biomodels-sbml-odemodels-oncology**.
 
## Agentic RAG
The Agentic RAG is build using HuggingFace's smolagents framework. A vector store is created using the curated dataset and the embeddings model ```sentence-transformers/multi-qa-mpnet-base-dot-v1```. Semantic searches are facilitated using FAISS.

The language model used for the agent is ```Qwen/Qwen2.5-72B-Instruct``` and is run using Hugging Face's inference api.

## Performance
The Agentic RAG performance was evaluated by grading the retrieval of models based on manually crafted queries to retrieve specific model ids. These queries focused on 5 broad categories
 - CAR (chimeric antigen receptor)
 - Interleukin
 - Breast Cancer
 - Pancreatic cancer
 - Leukemia
   
For each of these categories, 4 or 5 queries were prepared based on specific model descriptions, such that for each "positive" query a specific model id was expected, and for a "negative" query a set of models that the Agent should not be retrieve.

For each query a system of 4 points was designed, where one point is given if the agent successfully
 1. retrieves the expected model id
 2. provides the corresponding PMCID (a unique reference number or identifier that is assigned to every article that is accepted into PubMed Central) if there is one or indicates the absence of it
 3. retrieves the equations of the model (in Tellurium/Antimony code)
 4. retrieves a model ID not belonging to the set of excluded models when a negative query is run

The validation was performed by randomly selecting one of the "positive" queries and one of the "negative" queries for each category and then grade the performance of the model using the above system. A perfect score is 20.

The results are in the following table

| Category | BioModel Id | PMCID | Equations Provided | Excluded BioModels | Total | Notes positive query | Notes negative query |
|----------|-------------|------|--------------------|--------------------|-------|----------------------|----------------------|
| CAR | 1 | 1 | 1 | 1 | 4 | | | 
| Interleukin |1|1|1|1|4| Agent correctly reported the absence of PMDIC for this model.| Agent correctly reported the absence of PMDIC for this model. However, it reported the accession number.|
|Breast cancer|1|1|1|1|4|TAgent correctly reported the absence of PMDIC for this model.| Negative query returned model ID outside of the excluded models and correctly reported the absence of PMCID.|
|Pancreatic cancer | 1 | 0 | 0 | 0 | 1 | Agent retrieved correct model ID but failed to report absence of PMCID and did not include reactions. | Negative query returned an excluded model, suggesting retrieval is sensitive to shared vocabulary (both models describe pancreatic tumor-immune dynamics) rather than distinguishing query intent|
| Leukemia | 1 | 1 | 1 | 0 | 3 | Reported correctly that no PMCID was available.| Agent returned the same model as for positive query, suggesting retrieval is sensitive to a shared vocabulary.|
| | | | | Final score | 16/20 (80%) | | |

| Final score |
|-------------|
| 16 out of 20 points.|

### Known Limitations and Future Work
- Evaluation set is small (of the 10 queries across 5 categories one positive and one negative was selected randomly) due to 
  inference credit constraints; a larger set would give more robust performance estimates.
- Negative query performance (2/5) suggests the agent retrieves by semantic similarity without fully respecting exclusion criteria in 
  the prompt. A re-ranking step or explicit filtering layer could address this.
- The dataset is limited to 120 ODE oncology models; expanding to other modelling approaches or therapeutic areas would broaden 
  applicability.
- Future work: evaluate alternative embedding models and compare retrieval precision systematically.

## Code
### Jupyter Notebooks
 - Data generation
   - `creating_biomodels_dataset.ipynb` (initial pull from BioModels)
   - `augmenting_biomodels_dataset.ipynb` (augmenting dataset with abstract and Tellurium/Antimony code)
 - Agentic RAG
   - `agentic_RAG_systems_biomodels_dataset.ipynb` (defines agent tools, vector store and validation set)
 
## Acknowledgments
This project was developed with assistance from Claude (Anthropic) for architecture design, code implementation, and evaluation framework design. All domain-specific decisions, dataset curation, and project direction reflect the author's expertise in quantitative systems pharmacology.
