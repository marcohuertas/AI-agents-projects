# AI-agents-projects
Repository containing various AI-agent projects with QSP applications

## Purpose
This repository contains projects related to building AI agent workflows that can be used in activities usually encounter in QSP (quantitve systems pharmacology) workflows.
They represent hands-on projects that I have done to learn how to build AI-agent workflows.

## Projects
### 1. Agentic RAG for model retrieval
#### Description
This project consists of building an agentic-RAG that will retrieve the equations from a model that best matches a user's query.
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
 
#### Agentic-RAG agent
#### Performance
#### Jupyter Notebooks
 - Data generation
 - Agentic RAG
 
