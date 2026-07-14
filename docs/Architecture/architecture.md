---
title: Architecture
nav_order: 5
layout: default
---

# Architecture

Warqube is a local analysis and playback application for WARC files. R
coordinates the processing workflow and serves the Shiny dashboard. Java and
embedded Python run specialised processing and playback components. DuckDB
stores the resulting metadata and analysis data in a reusable Warqube
database.

## Design philosophy

Warqube follows several design principles:

- Local-first.
- Original WARC files remain untouched.
- Analysis results are stored separately from archived content.
- Established web archiving tools are reused for validation, WARC processing and playback.
- All processing is reproducible from the original WARC files.

## Scope and evidence

This page describes the architecture visible in the current project source.
It uses three kinds of statement:

- **Confirmed Warqube behaviour** describes a component call, data flow or
  stored result present in the source code.
- **Implementation choice** describes how the current project connects or
  configures its components.
- **General technology context** explains the usual purpose of an underlying
  technology. It does not establish why the Warqube project selected it.

The project source confirms which technologies Warqube uses, but it contains
no architecture decision records or other documented rationale for selecting
them. This page therefore does not attribute performance, preservation or
maintenance reasons to those choices unless the source states them.

## Architecture at a glance

Warqube separates processing, storage and presentation, while retaining the
original WARC files as the source for record inspection and playback.

```text
WARC directory
    |
    +--> JHOVE validation --------------------+
    +--> warcio record indexing --------------+
    +--> HTML text extraction ----------------+--> Warqube database (.duckdb)
    |        +--> Presidio and spaCy PII -----+          |
    |                                                    +--> Shiny dashboards
    +--> pywb CDXJ index --------------------------------+--> pywb playback
             |
             +-------------------------------------------> original WARC files
```

The two outputs serve different purposes:

- the Warqube database contains file metadata, record metadata, validation
  messages, extracted text, detected entities and collection settings; and
- the pywb collection contains a replay index and a reference to the original
  WARC directory.

The source does not copy WARC payloads into DuckDB.

## Processing, storage and presentation

| Layer | Responsibility | Main components | Persistent outputs |
| --- | --- | --- | --- |
| Processing | Validate files, index records, extract text, detect possible PII and prepare replay | R, Java/JHOVE, embedded Python, warcio, Presidio and spaCy | Rows in DuckDB, a pywb CDXJ index and pywb collection configuration |
| Storage | Retain derived analysis data and collection settings while leaving archived content in its source files | DuckDB, the original WARC directory and the pywb collection directory | A `.duckdb` file, original `.warc` or `.warc.gz` files and `index.cdxj` |
| Presentation | Query analysis results, display dashboards, inspect records and replay archived content | R/Shiny, DuckDB queries, the record viewer and pywb | Browser output; no separate presentation database |

This separation explains why most dashboards can reopen from a Warqube
database, while record payload inspection and playback still depend on the
original WARC files.

## End-to-end workflow

When you start a new analysis, Warqube performs the following confirmed
sequence.

1. **R starts the local application.** The Windows launcher checks for
   `Rscript`, and the R startup script selects the bundled Python executable
   before it launches the Shiny application in a browser.
2. **Warqube prepares the database.** It resolves the database name to a
   `.duckdb` file, creates the schema when required and opens a read-write
   connection pool. For a new processing run, it clears the principal analysis
   tables in the selected database before writing new results.
3. **JHOVE validates and characterises each WARC file.** R starts the bundled
   JHOVE command through Java with the WARC module and requests XML output.
   Warqube reads file size, format, version, status and validation messages
   from that output, stores them in DuckDB and deletes the temporary XML
   directory at the end of the step.
4. **warcio indexes WARC records.** R calls the Python `warcio` indexer through
   the embedded Python environment. The returned fields include record type,
   offsets, lengths, dates, target URIs, HTTP status, content type, record
   identifiers, revisit references and payload digests. Warqube stores these
   values as record-level metadata rather than archived payload content.
5. **Warqube records file and collection metadata.** It stores WARC file paths,
   sizes and selected `warcinfo` values. It also records the source WARC
   directory, a collection identifier, a selected seed URL and the pywb port
   in the Warqube database.
6. **Warqube prepares the pywb collection.** It gives pywb access to the source
   WARC directory, normally through a Windows directory junction, and runs a
   pywb reindex operation. This creates the collection's `index.cdxj` outside
   the Warqube database. If junction creation fails, the current code falls
   back to the source directory directly.
7. **Python extracts text from HTML responses.** The current extractor reads
   response records whose HTTP content type contains `html`, removes scripts,
   styles and HTML tags, normalises whitespace and stores the resulting text
   and basic record context in DuckDB.
8. **Presidio and spaCy identify possible PII.** The detector analyses stored
   text as Dutch, combines spaCy-backed analysis with configured pattern
   recognisers and stores candidate entity types, text fragments, positions,
   scores and recogniser names. The [PII Detection page](../UserGuide/piidetection.html)
   documents the current coverage limits.
9. **Warqube creates derived database views.** These views support WARC naming
   checks and URL-depth analysis without copying another full result set.
10. **Warqube switches to presentation.** It opens a read-only database pool
    for dashboard queries, starts or restarts pywb on the local playback port
    and reports that processing has completed.

The steps write results progressively. The source contains no transaction
around the complete workflow. If a later step fails, earlier steps may already
have written data to the database or pywb collection.

## Major components

### R and Shiny

**Confirmed Warqube role:** R is the coordinator and presentation runtime. It
starts the application, manages DuckDB connections, invokes Java and Python
processing, controls pywb, and supplies the queries and data used by the
dashboard. Shiny serves the local browser interface and updates dashboards in
response to user selections.

**General technology context:** Shiny is a framework for browser-based
applications driven by R. In Warqube, the browser is the user interface; the
analysis and orchestration remain in the local R process.

### DuckDB

**Confirmed Warqube role:** DuckDB is the persistent analysis store. Warqube
uses a read-write pool while processing and a read-only pool for most dashboard
queries. A database name without a directory resolves under `data/duckdb`, and
Warqube adds the `.duckdb` extension when it is absent.

**General technology context:** DuckDB is an embedded SQL database. Warqube
uses that embedded model directly: the database is a local file rather than a
separate database server.

### Embedded Python

**Confirmed Warqube role:** Warqube sets `python_embed/python.exe` as its
Python runtime before importing Python modules. That environment supplies
warcio, pywb, Presidio, spaCy and the HTML text extractor. R calls Python in the
same application workflow rather than requiring the user to start a separate
Python service.

### warcio

**Confirmed Warqube role:** warcio reads WARC records for two processing paths.
Its indexer returns the record metadata stored in `WarcInhoud`, and the text
extractor iterates through HTML response records. Separate warcio-based code
also reads the first `warcinfo` record for selected crawl metadata.

**General technology context:** warcio is a Python library for reading and
indexing WARC content. In Warqube it provides structured access to the WARC
record stream; DuckDB receives selected fields from that stream.

### JHOVE and Java

**Confirmed Warqube role:** Warqube invokes the bundled JHOVE command with the
`WARC-kb` module. JHOVE runs on Java and produces XML reports. Warqube converts
selected report fields and messages into file-level and validation-message
rows in DuckDB. No other Java role is confirmed by the source.

**General technology context:** JHOVE is a file-format validation and
characterisation tool. Warqube retains the parsed findings, not the temporary
XML report files.

### HTML text extraction and Trafilatura

**Confirmed Warqube role:** the current runtime extractor uses warcio to select
HTML response records and then removes scripts, styles and markup with Python
text operations before storing the text in `HtmlText`.

The R processing step and its source file still use the name Trafilatura, and
the installer includes the `trafilatura` Python package. However, the Python
extractor called by the current workflow does not import or call Trafilatura.
An active Trafilatura role in text extraction therefore cannot be confirmed
from the current source.

### Presidio and spaCy

**Confirmed Warqube role:** Presidio supplies the entity-analysis framework.
Warqube configures it for Dutch and adds pattern recognisers for several Dutch
formatted identifiers. spaCy supplies the Dutch `nl_core_news_lg` language
model used by the analyser. Warqube stores the returned candidates in
`pii_entities` and adds summary fields to `HtmlText`.

**General technology context:** entity recognition identifies candidate spans
of text and assigns types and scores. Those results require interpretation;
the architecture does not make them confirmed personally identifiable
information.

### pywb

**Confirmed Warqube role:** pywb provides replay. Warqube creates a collection
configuration, generates a CDXJ index from the original WARC files and starts
pywb through embedded Python on `127.0.0.1`, normally using port `8080`. The
Shiny playback page builds local pywb URLs for embedded replay and for opening
the pywb interface in a browser.

**General technology context:** a replay index locates captures; it does not
contain the full archived responses. pywb uses the index to find content in the
original WARC files and rewrites archived responses for browser playback.

## How analysis results are stored

The Warqube database contains these principal data groups:

| Stored data | Purpose |
| --- | --- |
| WARC file data | File path, size, format, version, validation status, crawl date and selected `warcinfo` fields |
| Validation messages | JHOVE findings associated with a WARC file |
| WARC record index | Record type, offsets, lengths, dates, HTTP and content metadata, identifiers, references and payload digests |
| Extracted HTML text | Target URL, record date, plain text and WARC file association |
| PII candidates | Entity type, text fragment, position, score and recogniser for detected candidates |
| Collection metadata | Original WARC directory, pywb collection identifier, seed URL and playback port |
| Derived views | Calculated WARC-name checks and URL-depth values used by dashboards |

Dashboard filters and summaries generally query these stored values when the
user opens a page. They do not reprocess the WARC files. This means the
database is a snapshot of the completed processing run, not a live view of
subsequent changes to the source directory.

The database does not contain the complete archived payloads. The record
viewer uses the stored file path, byte offset and length to read headers and
payload bytes from the original WARC file when requested.

## Playback and the original WARC files

Playback has its own data path alongside the analytical database:

```text
Warqube database metadata
        |
        +--> collection identifier and original WARC directory
                         |
                         v
pywb index.cdxj --> locate capture --> read original WARC --> replay in browser
```

For a new analysis, Warqube derives a collection identifier from the selected
WARC-directory path. It configures pywb to reach that directory, builds the
CDXJ index and starts a local pywb process. The Shiny dashboard does not itself
reconstruct archived pages.

When Warqube opens an existing database, it reads the stored source directory,
collection identifier and playback port. It then attempts to restore the pywb
configuration and restart playback for that collection.

This design has two practical consequences:

- moving or deleting the original WARC directory can leave database-backed
  dashboards available while breaking record payload inspection and playback;
  and
- copying only the `.duckdb` file does not create a self-contained replayable
  project. The corresponding WARC files and pywb index relationship remain
  necessary.

The current processing path uses a directory junction or direct source path;
it does not create an independent archive copy for playback.

## Confirmed design boundaries

- Warqube is a local application. Shiny opens the interface in a local browser,
  and pywb binds to the loopback address rather than an external host.
- Analysis data and replay data are related through collection metadata but
  are not stored in one file.
- Most dashboards operate on derived metadata in DuckDB. Playback and detailed
  record reading return to the original WARC files.
- Reprocessing with a selected Warqube database clears its principal analysis
  tables before the new run writes results.
- The current source defines component behaviour but does not document why R,
  DuckDB, JHOVE, warcio, Presidio, spaCy or pywb were selected over
  alternatives.
- The source confirms that Trafilatura is installed, but not that the current
  text-extraction runtime uses it.

## Next steps

Read [Loading a web archive](../UserGuide/loadcontent.html) to understand how
the processing and storage paths appear in the interface. Read
[Replaying archived websites](../UserGuide/playback.html) for the user-facing
playback workflow and its requirements.
