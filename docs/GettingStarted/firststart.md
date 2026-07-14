---
title: First Start
parent: Getting Started
nav_order: 3
layout: default
---

# First Start

This page explains how to start Warqube for the first time and what to expect
before you begin an analysis.

## Before starting

Complete the [installation](installation.html) before starting Warqube. Keep
all files and subdirectories in the Warqube project directory together.

Start Warqube from the project directory: the directory containing
`start_warqube.bat`.

## Start Warqube

Run the following Windows batch file:

```text
start_warqube.bat
```

Warqube opens a command window and starts the application in your web browser.
Keep the command window open while using Warqube. It displays progress and
error messages and closes the application when the process ends.

## What to expect

The command window first displays:

```text
Starting Warqube...
```

Further messages appear while Warqube loads. When start-up succeeds, the
command window reports that Warqube has loaded and is ready, and the dashboard
opens in the default web browser.

The dashboard runs locally. The exact local address may differ between
sessions and is selected automatically.

Starting Warqube does not immediately analyse files or create a populated
database. Processing begins only after you select a WARC directory and choose
**Start Processing**, or after you select an existing Warqube database.

## The first screen

Warqube opens on the **Load Content** page. From here, choose one of two
workflows.

### Analyse a WARC directory

Use this option when the WARC files have not previously been processed by
Warqube.

1. Select **Select Folder**.
2. Choose the directory containing the WARC files.
3. Review the database name. The default is
   `default_database_name.duckdb`.
4. Select **Start Processing**.

If you enter only a database file name, Warqube stores the database in its
`data/duckdb` directory. Warqube adds the `.duckdb` extension if it is missing.

Processing can take time. Progress and error information is shown in the
dashboard and command window. The source code does not define an expected
processing time because this depends on the selected data and system.

### Open an existing Warqube database

Use this option for a `.duckdb` file previously created by Warqube.

1. Locate **Select Preloaded Database**.
2. Select **Choose Database File**.
3. Choose the `.duckdb` file.

Warqube opens the database and prepares the available dashboards. If the
database contains the required collection information, Warqube also attempts
to restore website playback.

## If Warqube does not start

### R cannot be found

If the command window displays:

```text
ERROR: Rscript.exe not found.

Please install R and make sure Rscript is available.
```

Warqube cannot locate the R installation. Confirm that R is installed and that
`Rscript` is available from the Windows command line.

### A component is missing

If a message refers to missing Python, R packages, Python modules or the SpaCy
language model, run `install_warqube.bat` again from the Warqube project
directory. The application cannot start until its installed components are
available.

### The application stops with an error

If start-up fails after R has been found, the batch window displays:

```text
Warqube terminated with an error.
```

Keep the preceding messages from the command window, as they contain the
specific error reported by the application.

### An existing database cannot be opened

If Warqube shows:

```text
Database file does not exist or is not accessible.
```

confirm that the selected `.duckdb` file still exists and is accessible to the
current Windows user.

## What a successful start confirms

A successful start confirms that the dashboard and the components loaded at
start-up are available. It does not yet verify:

- that Java and JHOVE can process a WARC file;
- that a selected directory contains usable WARC files;
- that sufficient disk space or memory is available; or
- that website playback can use its local port.

These parts are first exercised when an analysis is started or an existing
database is loaded. The source code does not provide a separate first-run
system test.
