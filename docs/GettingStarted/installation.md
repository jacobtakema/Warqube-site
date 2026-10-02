---
title: Installation
parent: Getting Started
nav_order: 2
layout: default
---

# Installation

Warqube uses R as its main runtime and installs a private set of R and Python
dependencies inside the Warqube project directory. The installer also
downloads and installs JHOVE locally. Java must be available separately.

## Requirements

Before running the installer, ensure that the following requirements are met:

- **Windows.** The installer downloads the 64-bit Windows embedded distribution
  of Python and uses Windows batch files.
- **R 4.5.3.** The Windows launcher requires the exact R version recorded in
  `renv.lock`. It does not automatically select an older or newer R release.
- **Windows PowerShell.** The launcher uses the Windows PowerShell installation
  included with Windows to locate and validate R. It does not change the saved
  PowerShell execution policy.
- **Java available as `java`.** The installer uses Java to install and validate
  JHOVE. It does not install Java or require a particular Java version.
- **Internet access during installation.** The installer may download JHOVE,
  `renv`, R packages, embedded Python, `pip`, Python packages and the Dutch
  spaCy model.

The code does not define minimum disk-space or memory requirements.

## Run the installer

Keep the Warqube directory structure intact. In particular, the project must
contain `renv.lock` and the `python` directory. A JHOVE installation is not
included in a clean copy of Warqube; the installer creates it.

Run the Windows installer by opening `install_warqube.bat` in the extracted
Warqube directory, or invoke it from a Command Prompt:

```text
install_warqube.bat
```

The batch file uses `select_r.ps1` to find and validate the required R version,
then uses `run_with_r.bat` to run `install_warqube.R`. R does not need to be on
`PATH`. The launcher checks standard system and per-user installation folders,
R registry entries, other R installation folders and executables on `PATH`.
It sets the Warqube directory as the working directory automatically, including
when the batch file is started from another directory or a UNC location.

Normally, no R setting is required: the launcher finds a matching R
installation automatically. If R is installed in a portable or non-standard
location, you can explicitly select it from **Command Prompt (CMD)**. In the
same Command Prompt window, set `WARQUBE_RSCRIPT` to the full path of its
`Rscript.exe`, then run the installer:

```bat
set "WARQUBE_RSCRIPT=D:\Portable R\R-4.5.3\bin\Rscript.exe"
install_warqube.bat
```

This setting is temporary and applies only to that Command Prompt window. It
does not modify Warqube's scripts, source code or configuration. The selected
`Rscript.exe` must report the exact R version recorded in `renv.lock` (R 4.5.3
for Warqube v1.0.0). If it reports another version or cannot be used, the
launcher continues searching other locations for a matching installation.

## What the installer does

The installation proceeds in the following order.

### 1. Install and validate JHOVE

Warqube uses JHOVE 1.34.0. If a valid local installation already exists, the
installer reuses it. Otherwise, the installer:

1. verifies that Java is available;
2. downloads the official versioned JHOVE installer;
3. installs JHOVE into `jhove/V1.34.0`;
4. adjusts the installation so that it remains portable with the Warqube
   directory; and
5. verifies both the JHOVE version and the availability of its WARC module.

An existing installation that fails validation is moved aside with an
`.invalid-<timestamp>` suffix before a replacement is installed. Installation
stops if JHOVE cannot be downloaded, installed or validated.

### 2. Restore the R environment

If the `renv` package is not available, the installer first installs it. It
then runs:

```r
renv::restore(prompt = FALSE)
```

This restores the R packages recorded in `renv.lock` into the project-specific
`renv` environment.

### 3. Set up embedded Python

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

### 4. Install Python dependencies

The installer upgrades `pip` and installs the packages pinned in
`python/requirements-warq-embedded-lock.txt`. These dependencies include the
components used for WARC indexing, text extraction, playback and PII detection.

It also copies the `reticulate` support package `rpytools` into the embedded
Python environment and verifies that it can be imported.

### 5. Create working directories

The installer ensures that the following directories exist:

```text
data/
data/duckdb/
jhove/jhovewerkdir/
pywb_home/
pywb_home/collections/
```

The installer does not create `pywb_home/config.yaml`. Warqube generates that
configuration when it prepares a pywb collection for a new analysis, or when
it restores playback for an existing analysis with collection metadata.

### 6. Check installed components

The installer verifies that embedded Python can import:

- `warcio`;
- `pywb`;
- `spacy`;
- `presidio_analyzer`.

Finally, it loads the `nl_core_news_lg` spaCy model. Installation stops if a
required Python module, the model or `rpytools` cannot be loaded.

## Installation result

The batch file reports `Installation completed successfully` only when the R
installation script exits without an error. At that point the local JHOVE
installation and the required R and Python components have been validated.

After a successful installation, start Warqube by opening:

```text
start_warqube.bat
```

At start-up, Warqube selects `python_embed/python.exe` explicitly. If the early
R packages `shiny` or `here` are unavailable, the start script attempts to
install them from CRAN before opening the application.

## Installation troubleshooting

### No suitable R installation is found

The launcher prints every accessible R version it detects and only selects the
exact version recorded in `renv.lock`. If selection fails, install R 4.5.3 or
set `WARQUBE_RSCRIPT` as described above. Adding R to `PATH` is not required.

### PowerShell is blocked

The launcher starts Windows PowerShell without loading a profile and applies an
execution-policy setting only to that process. An organisation-wide policy can
still prevent the selection script from running; in that case, contact the
system administrator responsible for the device.

### Installation stops after R is selected

Keep the messages shown before `Installation failed`; they contain the specific
error from JHOVE, `renv`, Python or another installation step. Confirm that
Java is available as `java` and that the computer can reach the required
download services, then run `install_warqube.bat` again.
