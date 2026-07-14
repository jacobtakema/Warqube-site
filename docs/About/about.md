---
title: About
nav_order: 6
layout: default
---

# About

Warqube did not start as a research project or a generic WARC viewer. It
originated from a practical need to assess the quality of incoming web archives
at the National Archives of the Netherlands.

Existing tools each addressed part of that task, but no single application
combined validation, quality assurance, analysis and playback in one local
workflow. Warqube was developed to bring those activities together and make
the structure and quality of WARC collections easier to investigate.

## Why Warqube was created

Rather than replacing established web archiving tools, Warqube integrates them
into a single desktop application. Validation, record indexing, content
analysis and playback are brought together around a shared DuckDB database.

This allows users to move from a high-level overview of a collection to
file-level, record-level and content-level inspection without repeatedly
switching between separate tools.

Although Warqube was originally developed for government web archiving, it is
intended to support anyone working with WARC collections, including archives,
libraries, researchers and digital preservation professionals.

## Design philosophy

Warqube follows a local-first approach. Original WARC files remain untouched,
while derived analysis results are stored separately in a reusable database.

Established web archiving tools are reused for validation, WARC processing and
playback. Analysis results can be regenerated from the original WARC files
when the required Warqube dependencies and processing environment are
available.

For a detailed description of the processing workflow and components, see
[Architecture](../Architecture/architecture.html).

## Project goals

Warqube aims to:

- make the quality and structure of WARC collections easier to understand;
- combine validation, analysis and playback in one consistent workflow;
- support both collection-level assessment and detailed investigation;
- make findings transparent and traceable to the underlying WARC files;
- reduce the amount of manual work required to inspect incoming web archives;
- provide a foundation for policy-based validation and quality assurance.

Warqube does not attempt to replace professional judgement. Its dashboards and
checks are intended to support investigation and help users identify findings
that require closer review.

## Current status

Warqube is under active development. The current version provides a working
local application for WARC validation, metadata and content analysis, playback
and the exploration of derived quality indicators.

The documentation currently focuses on the behaviour of the application. More
guidance on interpreting results against the WARC specification, archival best
practice and organisational policies will be added over time.

Warqube is presently developed and tested primarily for Windows. Features,
installation requirements and documentation may change as the project moves
towards wider release.

## Acknowledgements

Warqube builds on established open-source projects, including R, Shiny,
DuckDB, JHOVE, warcio, pywb, Presidio and spaCy.

Their work makes it possible for Warqube to combine file-format validation,
WARC processing, content analysis and archived website playback in a single
local application.