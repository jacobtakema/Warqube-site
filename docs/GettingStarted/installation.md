---
title: Installation
parent: Getting Started
nav_order: 2
layout: default
---

# Installation

Warqube uses R as its main runtime and installs a private set of R and Python
dependencies inside the Warqube project directory. JHOVE is included with the
application, but requires Java to be available separately.

## Requirements

Before running the installer, ensure that the following requirements are met:

- **Windows.** The installer downloads the 64-bit Windows embedded distribution
  of Python and uses Windows batch files.
- **R 4.4.0 or later.** The installer stops if an older R version is detected.
- **`Rscript` available from the command line.** The installation batch file
  invokes `Rscript` directly.
- **Java available as `java`.** The included JHOVE batch file invokes the
  `java` command when Warqube analyses WARC files. The installer does not
  install Java or verify a particular Java version.
- **Internet access during installation.** The installer may download `renv`, R
  packages, embedded Python, `pip`, Python packages and the Dutch SpaCy model.

The code does not define minimum disk-space or memory requirements.

## Run the installer

Keep the Warqube directory structure intact. In particular, the project must
contain `renv.lock`, the `python` directory and `jhove/V1.30.1/jhove.bat`.

Run the installer from the root of the Warqube project: the directory that
contains `install_warqube.bat` and `install_warqube.R`.

On Windows, run:

```text
install_warqube.bat
```

The batch file runs:

```text
Rscript install_warqube.R
```

The installer uses the current working directory as the project root. Starting
it from another directory may therefore cause files to be created or resolved
in the wrong location.

## What the installer does

The installation proceeds in the following order.

### 1. Restore the R environment

If the `renv` package is not available, the installer first installs it. It
then runs:

```r
renv::restore(prompt = FALSE)
```

This restores the R packages recorded in `renv.lock` into the project-specific
`renv` environment.

### 2. Set up embedded Python

Warqube uses its own 64-bit embedded Python 3.10.5 installation under
`python_embed`. If `python_embed/python.exe` already exists, the installer
reuses it. Otherwise, it:

1. downloads the official Python 3.10.5 Windows embedded archive;
2. extracts it into `python_embed`;
3. enables Python's `site` module in `python310._pth`;
4. downloads and runs `get-pip.py`; and
5. verifies that `pip` can be invoked.

Warqube configures `reticulate` to use this interpreter. It does not use a
system Python installation or a managed virtual environment when the
application starts.

### 3. Install Python dependencies

The installer upgrades `pip` and installs the packages pinned in
`python/requirements-warq-embedded-lock.txt`. These dependencies include the
components used for WARC indexing, text extraction, playback and PII detection.

It also copies the `reticulate` support package `rpytools` into the embedded
Python environment and verifies that it can be imported.

### 4. Create working directories

The installer ensures that the following directories exist:

```text
data/
data/duckdb/
jhove/jhovewerkdir/
pywb_home/
pywb_home/collections/
```

### 5. Check installed components

The installer checks whether the bundled JHOVE launcher exists at
`jhove/V1.30.1/jhove.bat`. A missing launcher produces a warning rather than an
installation failure.

It then verifies that embedded Python can import:

- `warcio`;
- `pywb`;
- `spacy`;
- `presidio_analyzer`; and
- `trafilatura`.

Finally, it loads the `nl_core_news_lg` SpaCy model. Installation stops if a
required Python module, the model or `rpytools` cannot be loaded.

The installer does not run JHOVE or verify Java. Java and JHOVE are first used
when Warqube processes WARC files.

## Installation result

The batch file reports `Installation completed successfully` only when the R
installation script exits without an error. Warnings, including a missing
JHOVE launcher, do not necessarily cause the installer to fail.

After a successful installation, Warqube can be started from the project root
with:

```text
start_warqube.bat
```

At start-up, Warqube selects `python_embed/python.exe` explicitly. If the early
R packages `shiny` or `here` are unavailable, the start script attempts to
install them from CRAN before opening the application.
