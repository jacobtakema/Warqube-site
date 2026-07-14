# Warqube Documentation Style Guide

Use this guide when writing or reviewing user documentation for Warqube. Its
purpose is to keep the documentation consistent, practical and accessible to
archivists, records managers and other users who may not have a technical
background.

## Audience and purpose

Write for people who use Warqube to assess and explore web archives. Explain
what the user needs to do, what result to expect and how to recover from common
problems.

Answer the user's question before explaining the underlying implementation.
Explain what the user needs to do first. Explain why only when it helps the
user complete the task.

Do not write from the perspective of a developer. Include implementation
details only when they directly affect a user action, explain visible behaviour
or help the user resolve a problem. Put other technical information in the
Architecture documentation.

Base every statement about Warqube on the current application source code. If
the code does not confirm a detail, state that clearly. Do not fill gaps with
assumptions.

Document observable behaviour rather than internal implementation whenever
possible. Avoid documenting temporary implementation details that are likely
to change before the next public release.

## Standard page structure

Use the following structure where it applies:

1. **Title:** use the page title from the navigation.
2. **Introduction:** explain in one short paragraph what the page helps the
   user accomplish.
3. **Before you begin:** list prerequisites, required files or decisions.
4. **Procedure:** describe the task in numbered steps.
5. **Expected result:** explain what the user should see when the task
   succeeds.
6. **Troubleshooting:** quote relevant messages and explain the user action.
7. **Next steps:** link to the most likely next task or related page.

Omit a section when it does not help the user. Do not add empty sections as
placeholders. Every published user-documentation page must end with a
second-level heading named `Next steps`.

Example:

```markdown
# Load an existing analysis

Use an existing Warqube database to continue exploring a previously processed
web archive.

## Before you begin

Ensure that the `.duckdb` file is available on your computer.

## Open the database

1. Open **Load Content**.
2. Select **Choose Database File**.
3. Select the `.duckdb` file.

## Expected result

Warqube loads the database and makes the available dashboards ready for use.

## Next steps

Continue to [Understanding the Overview dashboard](overview.html).
```

## Tone of voice

Use a professional, calm and direct tone.

- Address the reader as **you**.
- Use active language: write “Select the WARC directory”, not “The WARC
  directory should be selected”.
- Start instructions with a clear verb such as **Select**, **Open**, **Review**,
  **Enter** or **Confirm**.
- Explain actions before background information.
- Use short sentences and short paragraphs.
- Be precise without sounding legalistic or academic.
- Do not use promotional claims, humour or conversational filler in procedures.
- Do not blame the user when something fails.

Use **must** for a requirement, **can** for an available option and **may** only
for a genuine possibility. Avoid **should** when an action is mandatory.

## British English

Use British English throughout the documentation. Examples include:

| Use | Do not use |
| --- | --- |
| analyse | analyze |
| behaviour | behavior |
| characterisation | characterization |
| initialise | initialize |
| licence (noun) | license (noun) |
| organisation | organization |

Keep official names, interface labels, commands, file names and quoted error
messages exactly as they appear in Warqube, even if they use a different
spelling convention.

## Terminology

Use the following terms consistently:

| Preferred term | Usage |
| --- | --- |
| Warqube | The product name. Do not shorten it. |
| web archive | A collection being assessed. Use two words. |
| WARC file | An individual `.warc` or `.warc.gz` file. |
| WARC directory | A directory selected for processing because it contains WARC files. |
| WARC record | An individual record stored in a WARC file. |
| Warqube database | A `.duckdb` file created or opened by Warqube. |
| analysis | The processing and assessment performed by Warqube. |
| dashboard | A screen that presents analysis results. |
| playback | Replaying archived web content through Warqube. |
| personally identifiable information (PII) | Use the full term on first use on each page, followed by `(PII)`. |

Use **directory** when referring to a location in the file system. Use
**folder** only when quoting an interface label such as **Select Folder**.

Use **select** for interface controls and choices. Use **enter** for text fields
and **open** for pages, files and applications. Do not use **click** unless the
action specifically requires a mouse.

Use the exact capitalisation of visible interface labels and format them in
bold, for example **Load Content**, **Database Name** and **Start Processing**.

## Heading hierarchy

Use one first-level heading for the page title:

```markdown
# Page title
```

Use second-level headings for the main sections:

```markdown
## Before you begin
## Start an analysis
## Expected result
## Troubleshooting
## Next steps
```

Use third-level headings only to divide a substantial second-level section:

```markdown
### R cannot be found
```

Do not skip heading levels. Avoid headings deeper than level three. Write
headings in sentence case, except for official product names and interface
labels. Do not number headings manually.

## Procedures

Use a numbered list for every procedure, including procedures with only one
step. Put one user action in each step. State the location before the action
when it prevents ambiguity.

Good:

1. Open **Load Content**.
2. Select **Select Folder**.
3. Choose the directory containing the WARC files.
4. Select **Start Processing**.

Avoid combining multiple decisions and actions in one step. If the interface
shows a result between actions, describe that result after the relevant step.

Use bullet lists for requirements, options and non-sequential information. Do
not use bullet lists for tasks that users must complete in order.

## Error messages and troubleshooting

Quote error messages exactly as they appear in the application. Do not correct
their spelling, punctuation, capitalisation or encoding in the quotation.
Present a message in a fenced text block:

```text
Database file does not exist or is not accessible.
```

After the quotation:

1. explain in plain language what the message means;
2. give the user a numbered recovery procedure; and
3. state what to expect after the problem is resolved.

Do not invent recovery steps that the source code does not support. If the
cause or recovery cannot be confirmed, say so and advise the user to retain the
message and relevant console output.

## Interface elements, files and code

- Use **bold** for buttons, tabs, fields and other visible interface labels.
- Use backticks for file names, directory names, extensions, commands, paths,
  configuration values and database table names.
- Use fenced code blocks for commands and multi-line messages.
- Add a language identifier such as `text`, `powershell`, `r` or `yaml` when it
  accurately describes the content.
- Do not use quotation marks as a substitute for bold or code formatting.

Use forward slashes in project-relative paths unless the user must enter a
Windows path exactly. Never include a contributor's personal absolute path in
published documentation.

## Links and cross-references

Use descriptive link text that names the destination or task. Do not use
“click here” or expose a URL when a readable label is available.

Good:

```markdown
Continue to [First Analysis](firstanalysis.html).
```

Link to prerequisite information instead of repeating it. Ensure that the
final `Next steps` section contains at least one relevant destination whenever
such a page exists.

## Notes and limitations

State limitations directly and explain their effect on the user. Distinguish
between:

- behaviour confirmed by the source code;
- behaviour observed during a test; and
- information that could not be verified.

Use wording such as:

> The source code does not define an expected processing time.

Do not present a likely explanation as a confirmed fact.

## Screenshots and examples

Use screenshots only when they make a task or result easier to recognise.
Screenshots must reflect the current interface and must not expose personal,
sensitive or collection-specific information.

Provide alternative text that describes the useful content of the image. Do
not use file names such as “screenshot” as alternative text.

Mark sample names and values clearly as examples. Do not imply that example
processing times, file sizes or results apply to every collection.

## Review checklist

Before publishing or updating a page:

1. compare every product statement with the current source code;
2. confirm that the page addresses an end-user task;
3. replace passive constructions with active language where possible;
4. check British English spelling;
5. verify interface labels against the application;
6. convert every procedure to numbered steps;
7. confirm that error messages are quoted literally;
8. remove unnecessary implementation detail and jargon;
9. test all links and build the Jekyll site; and
10. confirm that the page ends with `## Next steps`.

## Next steps

Use this guide when drafting the next user-documentation page. Review existing
pages against the checklist as they are updated; do not make unrelated bulk
changes solely to enforce the guide.
