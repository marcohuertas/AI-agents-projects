# AI-agents-projects
Repository containing various AI-agent projects with QSP applications

## Purpose
This repository contains projects related to building AI agent workflows that can be used in activities usually encounter in QSP (quantitve systems pharmacology) workflows.
They represent hands-on projects that I have done to learn how to build AI-agent workflows.

## Projects
### 1. Agentic RAG for model retrieval
#### Description
This project consists of building an Agentic RAG that will retrieve the equations from a model that best matches a user's query.
The model uses a pre-build dataset consisting in 120 mathematical model descriptions obtained from BioModels (https://www.biomodels.org/) and that is housed at HuggingFace (https://huggingface.co/datasets). The code used to create this dataset is also provided in this repository.

#### Dataset
The models selected for this curated dataset correspond to those retrieved from BioModels using the query:
 ```
 *:* AND modellingapproach:"ordinary differential equation model" AND modelformat:"SBML" AND submitter_keywords:"*oncology"
 ```
 resulting in 120 models. 
 
 For each model and abstract was retrieved from PubMed (https://pubmed.ncbi.nlm.nih.gov/), when possible, to augment the model description provided by the authors in BioModels.
 The SBML code for each model was converted to Tellurium/Antimony (https://github.com/sys-bio/tellurium) code, which more human-readable.
 The final dataset is stored in HuggingFace's dataset repository under **biomodels-sbml-odemodels-oncology**.
 
#### Agentic RAG
The Agentic RAG is build using HuggingFace's smolagents framework. A vector store is created using the curated dataset and the embeddings model ```sentence-transformers/multi-qa-mpnet-base-dot-v1```. Semantic searches are facilitated using FAISS.

The language model used for the agent is ```Qwen/Qwen2.5-72B-Instruct``` and is run using Hugging Face's inference api.

#### Performance
The Agentic RAG performance was evaluated by grading the retrieval of models based on manually prepared queries. These queries focused on 3 broad categories of models
 - CAR (chimeric antigen receptor)
 - Interleukin
 - Breast Cancer
   
For each of these categories, 4 or 5 queries were prepared based on specific model descriptions, such that each query would produce a specific model, including at least one "negative" query for which there were a set of models that the Agent should not be retrieve.

For each query a system of 4 points was designed, where one point is given if the agent successfully
 1. retrieves the correct model
 2. provides the corresponding PMCID (a unique reference number or identifier that is assigned to every article that is accepted into PubMed Central)
 3. retrieves the equations of the model (in Tellurium/Antimony code)
 4. retrieves a model description that does not form part of a set of models for the negative query 

The validation was performed by randomly selecting one of the "positive" queries and one of the "negative" queries for each category and then grade the performance of the model using the above system. The results are 

| Category | BioModel Id | PMCID | Equations Provided | Excluded BioModels | Total | Notes positive query | Notes negative query |
|----------|-------------|------|--------------------|--------------------|-------|----------------------|----------------------|
| CAR | 1 | 1 | 1 | 1 | 4 | | | 
| Interleukin |1|1|1|1|4|There is no PMDIC for this model. Reported correctly.|There is no PMDIC for this model, which is correct. Agent provided the accession number.|
|Breast cancer|1|1|1|1|4|There is no PMCID for this model, which the model indicated correctly.|The return model description is not part of the excluded models. PMCID was reported.|

#### Jupyter Notebooks
 - Data generation
   - `creating_biomodels_dataset.ipynb` (initial pull from BioModels)
 - Agentic RAG
 
