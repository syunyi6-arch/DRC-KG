# DRC-KG · Dengue Research Case Knowledge Graph

**A bilingual, evidence-traceable knowledge graph built from 261 dengue research cases**

[English](README.md) | [简体中文](README.zh-CN.md)

![research cases](https://img.shields.io/badge/research_cases-261-1f6f5c)
![languages](https://img.shields.io/badge/languages-Chinese_138_%2F_English_123-2d6cdf)
![annotation rows](https://img.shields.io/badge/annotation_rows-216%2C442-b5651d)
![RDF triples](https://img.shields.io/badge/RDF_triples-5.13M-6b4fbb)

---

## Contents

[Overview](#overview) · [Quick start](#quick-start) · [Scale](#scale) · [Repository layout](#repository-layout) ·
[Data records](#data-records) · [Technical validation](#technical-validation) ·
[Interactive explorer](#interactive-explorer) · [Reproduction](#reproduction) ·
[Usage notes and limitations](#usage-notes-and-limitations) · [How to cite](#how-to-cite) · [Contact](#contact)

---

## Overview

Dengue knowledge is published overwhelmingly as individual research cases: outbreak investigations, clinical
case series, vector surveys and laboratory studies, each reporting a handful of entities, measurements and
transmission claims in prose and tables. That knowledge is readable by epidemiologists but invisible to
machines, and its uncertainty is rarely separable from its assertions.

DRC-KG converts 261 dengue research-case publications — 138 Chinese-language and 123 English-language —
into one bilingual knowledge graph in which:

- every entity, attribute and relation is anchored to the research case that reported it;
- every structured statement is linked to the exact annotation sheet, row and source file it came from;
- transmission assertions are represented as source claims with their participants, qualifiers and
  certainty, so possible, inferred and confirmed findings stay distinguishable;
- Chinese and English annotations are mapped onto a single reviewed ontology (103 classes, 217 attributes,
  154 semantic relations), so the two language halves are queryable together.

The result is a dataset for evidence-aware retrieval, epidemiological knowledge exploration and
knowledge-graph methodology work, shipped with its ontology, its validation materials and two working
explorers.

## Quick start

```bash
# 1. the corpus inventory, with per-case counts for every derived table
01_source_literature/DRC-KG_261_Research_Papers.csv

# 2. the annotation corpus: 4 workbooks, 7 sheets each (zh / en ontology variant)
02_annotations/

# 3. field-level semantics for everything downstream
04_knowledge_graph/01_standardized_data/data_dictionary.csv

# 4. the graph itself
04_knowledge_graph/02_rdf_graph/graph_sample.ttl      # 3-case sample, loads anywhere
04_knowledge_graph/01_standardized_data/*.csv         # 13 tables + data dictionary
```

```python
# reading a table is enough for most analyses: one row per fact, with provenance columns
import pandas as pd
entities = pd.read_csv("04_knowledge_graph/01_standardized_data/entities.csv")
entities.groupby(["case_id", "class_label_en"]).size().head()
```

To click through the data instead of reading it, run the explorer (see
[Interactive explorer](#interactive-explorer)); it opens on a single case and loads roughly 200 KB of JSON
per case.

## Scale

| Item | Count |
|---|---:|
| Research cases | **261** (Chinese 138 / English 123) |
| Annotation rows across the 7 annotation sheets | **216,442** (Chinese 113,563 / English 102,879) |
| Entity candidates | **72,147** |
| Attribute facts | **66,670** |
| Relation statements | **53,024** |
| Transmission-related records (TR) / participants / qualifiers | **625 / 1,776 / 2,242** |
| Rules / rule elements / rule groups | **250 / 948 / 166** |
| Knowledge units / clusters | **41,342 / 41,285** |
| Evidence references | **216,442** |
| RDF triples / subjects | **5,127,002 / 497,178** |
| Ontology | **103 classes · 217 attributes · 154 relations**, 467 bilingual term pairs |

All counts are rebuilt from the four annotation workbooks by the scripts in
`04_knowledge_graph/03_code/` and are re-asserted in `release_manifest.json` and
`graph_build_manifest.json`.

## Repository layout

| Directory | Contents |
|---|---|
| `01_source_literature/` | Bibliographic inventory of the 261 cases (`case_id`, title, authors, journal, year) plus the per-case count ledger for every derived table |
| `02_annotations/` | The annotation corpus: 4 workbooks (Chinese / English cases × Chinese / English ontology variant), 7 sheets each — classes, attributes, relations, rules, transmission classes, transmission attributes, transmission relations |
| `03_ontology/` | The bilingual ontology (`.ttl` + `.owl`) and the reviewed mapping workbooks, including the 467-entry term map used for every URI |
| `04_knowledge_graph/` | `01_standardized_data/` (13 tables + data dictionary + release manifest), `02_rdf_graph/` (instance vocabulary, sample, full serialisations, build manifest), `03_code/` (builders) |
| `05_validation/` | Completeness, reliability, consistency and usability evaluation: datasets, scripts, results and per-experiment READMEs |
| `06_code/` | Two front ends over the same data: `DRC-KG-Web/` (Gradio, Python) and `DRC-KG-Web-HTML-JS/` (static build, Hugging Face Space) |

## Data records

### 1. Literature inventory

`01_source_literature/DRC-KG_261_Research_Papers.csv` holds one row per case (`CN-001…CN-138`,
`EN-001…EN-123`). Besides bibliographic fields it is the count ledger for the dataset: `n_01…n_07` record
the raw annotation rows per sheet, `total_source_rows` their sum, and `n_entities … n_evidence` the number of
facts derived from that case. Bibliographic fields are transcribed from the source articles; where a source
gives no journal or year, the field is left blank rather than imputed.

### 2. Annotation corpus

Each of the four workbooks in `02_annotations/` carries the same seven sheets:

| Sheet | Content |
|---|---|
| `01_普通三元组_类` | Entity mentions and their ontology class |
| `02_普通三元组_属性` | Attribute facts (entity, attribute, value, source wording) |
| `03_普通三元组_关系` | Semantic relations between entities, with direction and source wording |
| `04_规则汇总` | Epidemiological rules, decomposed into subject / condition / result / authority |
| `05_传播关系_类` | Transmission participants (`TR-…` codes) and their roles |
| `06_传播关系_属性` | Transmission qualifiers: place, time, vector, certainty, evidence type |
| `07_传播关系_关系` | Relations among transmission participants |

The `-zh` workbooks carry the annotation content and a trailing batch column; the `-en` workbooks are
row-aligned English-label mirrors. One increment batch (`increment_1115`, 149 rows over 17 cases) is merged
into the four workbooks and flagged in that column, so a rebuild from these files alone reproduces every
count above.

### 3. Ontology

`03_ontology/Dengue_Research_Case_Ontology.ttl` (Turtle, editable source) and `.owl` (RDF/XML) are two
serialisations of the same 6,395-triple ontology graph: 103 hierarchical classes, 217 attribute
declarations (210 distinct attribute names), 154 semantic relations and 11 annotation properties for
provenance. Identifiers are stable and shared with the data:

| Prefix | Namespace | Holds |
|---|---|---|
| `dkg` | `urn:dengekg:ontology:` | ontology IRI |
| `dgoc` | `urn:dengekg:ontology:class:` | classes (`DKG-C-0001…`) |
| `dgoa` | `urn:dengekg:ontology:attribute:` | attributes (`DKG-A-0001…`) |
| `dgor` | `urn:dengekg:ontology:relation:` | relations (`DKG-R-0001…`) |
| `dgom` | `urn:dengekg:ontology:meta:` | annotation properties |

`ontology_term_map.csv` lists the 467 reviewed term pairs (103 classes, 210 attribute names, 154 relations)
that assign every Chinese label a canonical English term; the standardized tables, the term map and the OWL
declarations share one URI space, and the build asserts a 100% label match (386,606 labels, 0 drift,
0 unmapped). See `03_ontology/README.md` for the suffix rules that generate IRIs for repeated and secondary
attributes.

### 4. Standardized tables

`04_knowledge_graph/01_standardized_data/` holds 13 analytical tables plus a data dictionary and a release
manifest. All CSVs are UTF-8 with BOM and use the same keys as the annotation sheets:

| Table | Rows | Key | Notes |
|---|---:|---|---|
| `cases.csv` | 261 | `case_id` | one row per research case, with derived counts |
| `entities.csv` | 72,147 | `entity_id` | case-scoped entity candidate, class label in both languages, `class_uri` |
| `attributes.csv` | 66,670 | — | owner entity, ontology attribute, value as written |
| `relations.csv` | 53,024 | `relation_id` | head / tail entity, raw relation wording, normalized ontology relation |
| `transmission.csv` | 625 | `transmission_id` | TR record with participant counts and `graph_ready` |
| `transmission_participants.csv` | 1,776 | — | participant roles, source wording and English gloss |
| `transmission_attributes.csv` | 2,242 | — | transmission qualifiers |
| `rules.csv` | 250 | `rule_id` | rule text aggregated by element category |
| `rule_elements.csv` | 948 | — | raw element label and normalized category |
| `rule_groups.csv` | 166 | — | rules grouped per case |
| `knowledge_units.csv` | 41,342 | `ku_id` | normalized text unit as SHA-256 + length (no source text) |
| `knowledge_unit_clusters.csv` | 41,285 | `cluster_id` | deduplicated text units |
| `evidence_references.csv` | 216,442 | `evidence_id` | one per annotation row: `fact_id` → sheet, row, file, batch |

`data_dictionary.csv` defines every field and its value domain; `release_manifest.json` records the row
counts, SHA-256 hashes of all 13 tables, the derivation rule for each table and the label provenance. The
tables are mutually joinable on `case_id` and on the fact identifiers (`entity_id`, `relation_id`,
`transmission_id`, `rule_id`, `ku_id`).

### 5. RDF graph

`04_knowledge_graph/02_rdf_graph/` contains the graph in four forms:

| File | Size | Content |
|---|---:|---|
| `instances.ttl` | 247 MiB | full graph, Turtle |
| `instances.nt` | 532 MiB | full graph, N-Triples (5,127,002 triples, unsorted) |
| `graph_sample.ttl` / `.jsonld` | 2.1 / 2.5 MiB | CN-001…CN-003 subset (3,883 nodes, 41,909 statements) |
| `instance_vocab.ttl` | 4 KiB | instance namespaces, predicates and datatypes used by the export |

Instance IRIs live under `urn:dengekg:instance:` and `urn:dengekg:graph:`, with ontology terms resolved
through the same term map as the tables. Provenance is carried on the graph itself (`sourceBatch`,
`sourceBatches`, `sourceFile`, `nIncrementRows`). The two full serialisations exceed GitHub's per-file size
limit and travel with the release package instead of the Git history; `graph_build_manifest.json` lists
their byte sizes and SHA-256 hashes, and `03_code/build_kg_rdf.py` regenerates them from the standardized
tables.

### 6. Validation materials

`05_validation/` is organised by evaluation dimension (`4.1_completeness`, `4.2_reliability`,
`4.3_consistency`, `4.4_usability`). Each folder ships its inputs, its scripts, its results and a README
that states the protocol, the excluded cases and the exact denominators, so every number in the next
section can be recomputed rather than trusted.

## Technical validation

| Dimension | Design | Result |
|---|---|---|
| Completeness | 30-article reference set (15 Chinese / 15 English), author-adjudicated binary TP/FN protocol, no partial matching | **93.91%** fact recall (3,483 / 3,709 eligible facts); Chinese 93.19%, English 94.39% |
| Reliability — agreement | 1,422 paired decisions across five dimensions (Cohen's κ), plus a 211-decision evidence-set check | **82.8%** agreement, **κ = 0.823** (per dimension 0.752–0.842) |
| Reliability — precision | 1,000 facts stratified by language × fact type (6 strata, weighted estimate) | **90.2%** sample precision, 90.16% weighted, 95% CI 88.2–92.1% |
| Consistency — RDF | 216,442 annotation rows instantiated into RDF; 261 cases; 625 TR codes; SHACL shapes with pyshacl + RDFS inference | **0 SHACL violations**; 49 machine-reproducible cross-table structural gaps (Chinese 40 / English 9) |
| Consistency — bilingual mapping | 600-record stratified sample + 100 label pairs, author-adjudicated, Wilson intervals | **96.17%** pass (95% CI 94.31–97.43%); 96 / 100 pairs; 0 bidirectional label conflicts over 329,564 occurrences |
| Usability | 40 competency questions answered over the evidence-aware graph and over an SPO projection of the same 42 source assertions | **92.5%** (37/40) vs **40.0%** (16/40); 40/40 field-support for the evidence-aware representation |

The consistency work also reports 35,895 surface-level heuristic flags, counted separately by class (for
example head-entity strings not located verbatim in the source, or domain/range pairs awaiting review);
they are documented in `05_validation/4.3_consistency/README.md` and are excluded from the hard-compliance
figures above. SHACL is used with RDFS subclass inference; this is not an OWL 2 DL consistency proof, and
domain/range statements in the ontology are descriptive rather than asserted axioms.

## Interactive explorer

Two front ends ship with the dataset; both read the standardized tables and share wording and graph rules.
Both open on a single case, with a Chinese/English switch, an entity–attribute–relation browser, a
transmission view that keeps uncertainty visible, and evidence traces back to annotation rows.

**Live demo** — static build on Hugging Face Spaces:
<https://huggingface.co/spaces/syy1210/Dengue-Research-Case-Knowledge-Graph>

**Static build (no Python runtime).** Open it through an HTTP server — browsers block `fetch()` from
`file://`:

```bash
cd 06_code/DRC-KG-Web-HTML-JS
preview.bat                 # Windows: http://127.0.0.1:7870/
# or: python -m http.server 7870 --bind 127.0.0.1
```

The page loads only the case you select (~200 KB of JSON per case, ~135 MB of shards in `data/`). Add
`?lang=zh` to open in Chinese.

**Gradio build (Python).** The fuller build, and the one wired to the standardized tables:

```bash
cd 06_code/DRC-KG-Web
install_windows.bat         # creates .venv, installs gradio 5.49.1 + pyvis 0.3.2
start_std.bat               # standard-table mode → http://127.0.0.1:7860/
```

Three modes are available in this build: `demo` (two synthetic demo cases, `start_demo.bat`), `std`
(reads `04_knowledge_graph/01_standardized_data/`, `start_std.bat`) and `public` (reads a checked
`public_data/` view that must pass `release_check.py`, `start_public.bat`). On other platforms:

```bash
python -m pip install -r requirements.txt
DENGEKG_MODE=std python app.py          # PowerShell: $env:DENGEKG_MODE="std"; python app.py
```

`python check_std.py` runs the same checks as the app without opening a browser.

## Reproduction

```bash
# standardized tables  (02_annotations + increment batch -> 13 tables)
python 04_knowledge_graph/03_code/build_kg_tables.py

# literature ledger     (cases.csv -> 01_source_literature inventory)
python 04_knowledge_graph/03_code/build_papers_table.py

# RDF graph             (standardized tables -> instances.ttl / instances.nt / vocabulary)
python 04_knowledge_graph/03_code/build_kg_rdf.py
```

The builders are deterministic; the table and graph builds write manifests with row counts and SHA-256
hashes, and rebuilds are compared against them. Validation scripts are self-contained per dimension, with
their own `requirements.txt` where they need one.

## Usage notes and limitations

**Entity identity is literal and case-scoped.** Entities are candidate mentions (`identity_status = candidate`)
matched within a single research case; no cross-publication disambiguation or coreference is applied.
Statements about "the same" pathogen strain or location across papers therefore remain separate records.

**Transmission claims are source claims, not confirmed pathways.** A `TR-…` code marks a
transmission-related record in the literature. `graph_ready = true` (494 of 625) means the participants
needed to draw an edge are present and structurally usable; it says nothing about epidemiological
confirmation. Possible, inferred and confirmed findings are kept apart through the qualifier tables, and
unconfirmed research claims are never promoted to confirmed facts.

**Evidence is by reference, not by text.** Source articles are not redistributed: knowledge units store a
normalized text SHA-256 and length, and `evidence_references.csv` points to the sheet, row and file of the
annotation rather than reproducing the passage. Reuse of any quoted wording remains subject to the
publisher's terms.

**Some metadata is absent at the source.** Journal and publication year are blank where the source articles
did not state them; attribute values are kept as written, without numeric normalization or unit
conversion. Downstream analyses that need SI units should convert explicitly.

**Corpus scope.** DRC-KG covers 261 dengue research-case publications selected for case-level reporting; it
is not a global surveillance dataset, and its counts describe this corpus rather than dengue epidemiology
at large.

**Release contents.** Version 1.0 (2026-11-15) of the dataset consists of the annotation corpus, the
ontology, the 13 standardized tables, the validation materials and both explorers. The full RDF
serialisations exceed the per-file size limit and travel with the release package; the sample and the
builders in this repository regenerate them, and rebuilds are checked against the hashes in the manifests.

## How to cite

If you use DRC-KG, please cite the dataset and the manuscript that describes its construction and
validation:

> Zhang, X., Su, Y., Cai, S., Wei, N., Han, B., Liu, H., Chen, C., Ye, P., & Chen, J. (2026).
> *An Evidence-Traceable Knowledge Graph of Dengue Research Cases.*

> DRC-KG: Dengue Research Case Knowledge Graph, version 1.0 (2026). Dataset, ontology and validation
> materials.

Section-level provenance for any single statement is available inside the dataset itself: every fact
resolves to a `case_id`, and from there to the annotation sheet and row recorded in
`evidence_references.csv`.

## Contact

Correspondence: Shuyan Cai (261302156@njnu.edu.cn, [ORCID 0009-0006-5275-6266](https://orcid.org/0009-0006-5275-6266))
and Na Wei (weina@njnu.edu.cn, [ORCID 0009-0001-3413-4686](https://orcid.org/0009-0001-3413-4686)),
Key Laboratory of Virtual Geographic Environment, Ministry of Education, Nanjing Normal University.

Please open an issue for problems with the data, the build scripts or the explorers.
