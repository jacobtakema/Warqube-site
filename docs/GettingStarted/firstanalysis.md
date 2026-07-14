---
title: First Analysis
parent: Getting Started
nav_order: 4
layout: default
---

# First Analysis

Your first analysis creates a Warqube database from one or more WARC files.
Once processing has completed, you can begin exploring the results in the
dashboard.

## Before you begin

Ensure that:

- Warqube starts successfully;
- the WARC files are available in one directory; and
- you have chosen a name for the analysis database.

Warqube searches the selected directory for files ending in `.warc` or
`.warc.gz`. The source code does not confirm that files in subdirectories are
included.

{: .warning }
> Use a new database name for a new analysis. If you start processing with the
> name of an existing Warqube database, Warqube clears its existing analysis
> data before processing the selected WARC directory.

## Start your first analysis

1. Start Warqube with `start_warqube.bat`.
2. Wait for the dashboard to open in your web browser.
3. Open **Load Content** if it is not already selected.
4. Select **Select Folder**.
5. Choose the directory containing the WARC files.
6. Confirm that the selected path appears below **Select Folder**.
7. Review **Database Name**. Replace
   `default_database_name.duckdb` with a meaningful, unused name if required.
8. Select **Start Processing**.

If the database name does not end in `.duckdb`, Warqube adds the extension
automatically. If you enter only a file name, Warqube stores the database in:

```text
data/duckdb/
```

This directory is inside the Warqube project directory.

## While processing is running

Keep Warqube, the browser and the command window open. Do not move or remove
the selected WARC files while they are being processed.

Warqube displays a progress window headed:

```text
Processing WARC Files
```

The detail beneath the progress bar changes as Warqube works. The visible
stages are:

1. `Managing database`
2. `Clearing database tables...`
3. `Running JHOVE validation...`
4. `Indexing WARC files...`
5. `Extracting Text content...`
6. `Identifying PII in text`
7. `Finishing up...`

These stages are progress labels, not separate actions for the user. The
command window displays additional processing messages.

The source code does not define an expected processing time. The duration
depends on the selected WARC files and the system running Warqube.

## Recognise a successful analysis

When the main processing workflow completes, Warqube displays a notification
in this form:

```text
Processing completed in [minutes] minutes and [seconds] seconds!
```

The values in brackets depend on the actual processing time. The analysis
database is then available at the name and location selected before
processing.

Open **Overview** to confirm that Warqube can present the results. The page
shows summary information such as the total number of WARC files and a calendar
of crawl dates when the required data is available.

## Troubleshooting

### Start Processing does not begin the analysis

Warqube requires both a selected WARC directory and a database name before it
starts processing. If no progress window appears:

1. Confirm that a path appears below **Select Folder**.
2. Confirm that **Database Name** is not empty.
3. Select **Start Processing** again.

The source code does not define a specific error message for either missing
value.

### No usable WARC files are found

1. Confirm that the selected directory directly contains files ending in
   `.warc` or `.warc.gz`.
2. Keep the command-window output if processing stops or produces no results.

The source code does not define a dedicated dashboard message for an empty
directory or unsupported file extension.

### Processing stops with an error

Keep the command window open and retain the messages shown immediately before
the error. The source code does not provide one general recovery procedure for
all processing failures.

1. Note the processing stage shown in the progress window.
2. Retain the complete message from the command window.
3. Confirm that the selected WARC files still exist and are accessible.
4. Do not report the analysis as complete unless Warqube displays the
   `Processing completed` notification.

## Next steps

Continue to [Understanding the Overview dashboard](../UserGuide/overview.html)
to review the main results of the analysis.
