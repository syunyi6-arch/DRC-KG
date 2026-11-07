# DRC-KG: Dengue Research Case Knowledge Graph

**A Bilingual, Evidence-Traceable Knowledge Graph of Dengue Research Cases**

[English](README.md) | [简体中文](README.zh-CN.md)

## 1. Overview

DRC-KG (Dengue Research Case Knowledge Graph) is a bilingual, ontology-based epidemiological knowledge graph constructed from 261 dengue research-case publications, including 138 Chinese-language and 123 English-language studies.

The project aims to transform epidemiological knowledge reported in dengue research cases into structured, queryable and evidence-traceable knowledge representations.

DRC-KG integrates epidemiological events, entities, attributes, semantic relationships, rules, transmission-related information and supporting evidence. Particular emphasis is placed on representing uncertainty in infectious disease transmission and preserving connections between structured knowledge and its source literature.

The repository provides the data processing pipeline, ontology resources, standardized knowledge graph data, technical validation materials and an interactive web-based knowledge graph explorer.

## 2. Data Sources and Knowledge Graph Construction

The source corpus comprises 261 Chinese and English dengue research-case papers.

Knowledge is organized through structured annotation of narrative text and tables. The extracted information is classified using a unified bilingual domain ontology and transformed into standardized datasets and RDF knowledge graphs.

The workflow includes:

1. Research paper selection and case inventory construction.
2. Sentence-level and table-level knowledge annotation.
3. Classification of entities, attributes, relationships and epidemiological rules.
4. Representation of transmission relationships and uncertainty information.
5. Bilingual ontology mapping and standardized data integration.
6. RDF knowledge graph construction and evidence linkage.
7. Technical validation and interactive graph exploration.

Transmission claims are represented together with their supporting evidence and uncertainty information, allowing possible, inferred and confirmed findings to be distinguished.

## 3. Dataset and Knowledge Graph

DRC-KG includes:

| Component | Description |
|---|---|
| Research cases | 261 Chinese and English dengue research papers |
| Annotation datasets | Structured classification tables for epidemiological knowledge |
| Bilingual ontology | Unified classes, attributes and semantic relationships |
| Standardized data | Interconnected CSV datasets |
| RDF knowledge graph | Machine-readable graph representations |
| Evidence references | Links between structured statements and source literature |
| Validation resources | Data and scripts for technical evaluation |
| Interactive explorer | Bilingual web interface for knowledge graph exploration |

The unified ontology includes 103 classes, 213 attributes and 154 semantic relationships.

The knowledge graph supports the representation of epidemiological events, cases, laboratory investigations, vector surveillance, preventive measures, transmission relationships and research findings.

## 4. Repository Structure

The repository is organized into the following main directories:

- **`01_source_literature/`** — Bibliographic inventory and identifiers for the 261 research cases.
- **`02_annotations/`** — Chinese and English knowledge annotation and classification datasets.
- **`03_ontology/`** — Bilingual ontology files, terminology mappings and machine-readable ontology representations.
- **`04_knowledge_graph/`** — Standardized datasets, RDF graph resources and knowledge graph construction scripts.
- **`05_validation/`** — Technical validation datasets, scripts and evaluation results.
- **`06_code/`** — Source code, data processing tools and the interactive knowledge graph explorer.

Large RDF instance files may be distributed separately through Zenodo because they exceed standard GitHub file-size limits.

## 5. Technical Validation

DRC-KG is evaluated using four complementary dimensions:

| Evaluation dimension | Description |
|---|---|
| Completeness | Comparison with a manually constructed reference dataset |
| Reliability | Inter-annotator agreement evaluation |
| Consistency | SHACL validation and structural consistency checks |
| Usability | Competency questions and SPARQL query evaluation |

The validation results provide quantitative evidence of the dataset's coverage, annotation reliability, structural integrity and query capabilities.

Detailed evaluation procedures, datasets and results are available in `05_validation/`.

## 6. Interactive Knowledge Graph Explorer

DRC-KG includes a bilingual interactive web explorer for querying and visualizing research-case knowledge.

The explorer supports:

- Research case browsing and selection.
- Chinese and English interfaces.
- Entity, attribute and relationship exploration.
- Interactive knowledge graph visualization.
- Transmission uncertainty information.
- Evidence provenance and source tracing.

**Online demonstration**

Hugging Face Space:

https://huggingface.co/spaces/syy1210/Dengue-Research-Case-Knowledge-Graph

Access to the online demonstration may be restricted while the project is under development.

### Local Web Deployment

The static web explorer is implemented using HTML and JavaScript and does not require a server-side Python runtime.

To run the explorer locally, navigate to its application directory under `06_code/` and start a local HTTP server:

`python -m http.server 8000`

Then open `http://localhost:8000` in a web browser.

The required data files must be placed in the locations expected by the application. Refer to the application-specific documentation for details.

## 7. Data Availability

The research data and source code are being prepared for public archival release through Zenodo.

The planned archive includes the research paper inventory, annotation datasets, bilingual ontology, standardized knowledge graph data, RDF representations, technical validation materials and reproducibility code.

Large graph files and other research data may be provided through Zenodo rather than stored directly in the GitHub repository.

The Zenodo dataset DOI and archival link will be added following formal publication.

## 8. Data Use and Limitations

The original source publications are not redistributed in full because of copyright restrictions.

The dataset retains structured epidemiological knowledge and available provenance information.

Entity records are maintained as candidates and are not necessarily disambiguated across publications. Some transmission relationships represent hypotheses or uncertain findings rather than confirmed epidemiological pathways.

A transmission instance marked as `graph_ready = true` indicates technical readiness for graph representation and does not establish epidemiological confirmation.

Users should interpret the knowledge graph in conjunction with its supporting evidence, uncertainty information and documented data limitations.

## 9. Citation

If you use DRC-KG in your research, please cite the corresponding Zenodo dataset and associated research publication when available.

Citation information and the dataset DOI will be provided following formal publication.

## 10. License

Licensing information is provided with the applicable dataset and code components.

The permissions for software, original structured data and third-party source materials may differ. Please consult the relevant license and documentation before redistribution or reuse.

## 11. Project Status

DRC-KG is a research project under active development and publication preparation.

The GitHub repository, interactive website and Zenodo archive may have different access settings during the development and review stages.

Public release information will be updated when the corresponding resources become available.
