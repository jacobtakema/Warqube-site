---
title: Architecture
nav_order: 5
layout: default
---

# Architecture

## Technical principles Warqube
Warqube analyses your [WARC files](https://en.wikipedia.org/wiki/WARC_(file_format)) and presents the results in a local dashboard webapp. Warqube is build in R programming language but also uses Python and SQL. It uses [JHOVE](https://jhove.openpreservation.org/) for validation and characterisation, [WARCIO](https://github.com/webrecorder/warcio) for indexing the content, Microsoft Presidio / Spacy for PII and [pywb](https://github.com/webrecorder/pywb) for playback. Under the hood it uses [DuckDB](https://duckdb.org/) as an analytical processing database. The data is stored in a .duckdb file for reuse in later sessions.