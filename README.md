# DRC-KG — Dengue Research Case Knowledge Graph

This repository holds the bilingual dengue research-case knowledge graph: the literature index,
the annotation workbooks, the ontology, the standardized tables, the RDF serialization, the
validation packages and the two web viewers.

## Language convention

The release uses one rule everywhere, so a reader never has to guess what language a field is in:

| Layer | Language |
|---|---|
| Source text and annotation content (entity names, relation text, rule text, article titles) | kept in the language of the source article (Chinese or English) |
| Column headers, field names, status enums (`candidate`, `unresolved`, `true`/`false`), verdicts (`Pass`, `Correct`, `Reviewed`) | English |
| Ontology terms | bilingual: English `rdfs:label` plus a Chinese `rdfs:label`, and a Chinese name/definition comment |
| Web interface | bilingual, switched at runtime; status enums are translated for display |
| Documentation (README files) | English only |

Free-text review notes that were originally written in Chinese keep the Chinese wording and carry an
English companion column (`Confirmed Issue (EN)`, `Correction Details or Direction (EN)`,
`Validation Notes (EN)`), so no row mixes languages inside one cell.

## Layout

```
01_source_literature/       261-article index (CSV + XLSX)
02_annotations/             four classification workbooks (zh / en mirror, 7 sheets each)
03_ontology/                OWL/Turtle ontology, term map, bilingual spreadsheets
04_knowledge_graph/
  01_standardized_data/     the 13 released tables + data_dictionary + release manifest
  02_rdf_graph/             instance graph (Turtle / N-Triples / JSON-LD sample)
  03_code/                  build scripts (tables, RDF, papers index, ontology labels, role map)
05_validation/              4.1 completeness, 4.2 reliability, 4.3 consistency, 4.4 usability
06_code/                    Gradio app (DRC-KG-Web) and its static HTML/JS build
```

## Rebuild order

```bash
python -X utf8 04_knowledge_graph/03_code/build_kg_tables.py        # 02_annotations -> 01_standardized_data
python -X utf8 04_knowledge_graph/03_code/build_papers_table.py     # cases.csv -> 01_source_literature
python -X utf8 04_knowledge_graph/03_code/build_kg_rdf.py           # tables -> 02_rdf_graph
python -X utf8 06_code/DRC-KG-Web-HTML-JS/tools/build_web_data.py   # tables -> web data
python -X utf8 06_code/DRC-KG-Web-HTML-JS/tools/build_i18n.py       # ui_text.py -> web interface text
```

`build_kg_tables.py` is strict on purpose: it refuses to build if the English term map drifts from the
reviewed mapping sheet, if a Chinese participant role has no English mapping (`role_en_map.json`), or if
any `role_en` value still contains Chinese. The build is byte-reproducible from the four workbooks.

## Checks

```bash
python -X utf8 05_validation/4.1_completeness/code/verify_package.py   # package integrity + manifest hashes
python -X utf8 06_code/DRC-KG-Web/check_std.py                         # 13 tables -> view-level smoke test
node 06_code/DRC-KG-Web-HTML-JS/tools/smoke_test.js                    # static site code paths
```
