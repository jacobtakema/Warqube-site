---
title: Replaying archived websites
parent: User Guide
nav_order: 9
layout: default
---

# Replaying archived websites

Use **Playback** to open an archived URL from the loaded web archive. Select a
suggested seed URL or enter an original target URL, wait until local playback
is active and then select **Open**.

## Purpose

Playback lets you inspect archived web pages as pages rather than as WARC
record metadata. Warqube displays the replay in the application and provides a
link for opening the same replay in a separate browser tab or window.

Playback is an inspection aid. A page that replays incompletely does not by
itself establish whether the relevant records are absent, inaccessible or
unsupported.

## Requirements

Before opening a replay, confirm that:

- you have completed an analysis or loaded a Warqube database with playback
  collection information;
- the original WARC directory remains accessible at the location recorded in
  the database; and
- **Playback status** reports that local playback is active.

Warqube starts the local playback service while processing a collection and
when restoring playback information from an existing database. The interface
may display the following message while this happens:

```text
pywb wordt gestart...
```

Wait for the status to change before selecting **Open**.

## Open a replay

To open a suggested archived page:

1. Open **Playback**.
2. Wait until **Playback status** displays:

   ```text
   pywb playback actief.
   ```

3. Select a URL under **Aangetroffen seed URL's**.
4. Confirm that the URL appears in **Seed URL / replay URL**.
5. Select **Open**.
6. Review the replay displayed below the controls.

Warqube suggests captured HTTP or HTTPS response URLs that have status 200 and
HTML recorded as their mimetype or content type. The list contains at most 200
candidate URLs and favours shorter, shallower URLs without query strings.

To open another captured target URL manually:

1. Enter the original HTTP or HTTPS target URL in **Seed URL / replay URL**.
2. Select **Open**.
3. Review the replay result.

The field is not validated before Warqube builds the local replay address. Use
an original captured target URL rather than assuming that every pasted replay
address will be interpreted correctly.

After a replay opens, select **Open huidige replay in browser** to open the
current local replay address outside the embedded frame. The pywb calendar is
available in this browser view, allowing you to choose between available
captures of the URL.

## Navigate the archived website

To continue browsing:

1. Follow a link presented within the replayed page.
2. Review whether the destination opens from the loaded playback collection.
3. To return to a known page, enter its original target URL in **Seed URL /
   replay URL** and select **Open** again.

Navigation remains within the local replay service. A linked destination can
only be replayed when the playback collection can resolve the requested
capture and read the associated WARC content.

Warqube does not provide its own capture calendar or date selector in the
embedded view. To use the pywb calendar, select **Open huidige replay in
browser**. The calendar and any other controls shown there belong to pywb, not
to the surrounding Warqube dashboard.

## Common issues

### No collection is loaded

Warqube displays:

```text
Geen collectie geladen.
```

This means that Playback has no active collection identifier.

1. Open **Load Content**.
2. Complete an analysis or load a Warqube database containing collection
   information.
3. Return to **Playback**.

Warqube can start the local collection when the required collection metadata
is available.

### Playback is not listening

Warqube displays:

```text
pywb-proces leeft mogelijk, maar poort 8080 luistert niet.
```

The application did not find a service listening on the local playback port.

1. Wait for the playback status to be checked again.
2. Retain the message and relevant console output if it persists.

The source code does not provide a user control for restarting the playback
service from this page.

During collection processing or database loading, Warqube can also display:

```text
pywb gestart maar healthcheck faalde (zie console).
pywb is niet correct gestart bij laden van bestaande database.
pywb is niet correct gestart; playback niet vernieuwd.
```

For any of these messages:

1. Retain the exact message and relevant console output.
2. Do not treat playback as available until **Playback status** reports:

   ```text
   pywb playback actief.
   ```

The source code does not provide a confirmed recovery procedure beyond the
automatic startup and status checks.

### No URL has been opened

Before you choose a URL, the playback area displays:

```text
Kies een seed URL en klik op Open om pywb playback te starten.
```

1. Select a suggested URL or enter an original captured target URL.
2. Select **Open**.

The embedded replay frame appears after Warqube has built a replay address.

### A page is missing or incomplete

The code does not define a specific Warqube message for a missing replay or
missing page resources.

1. Confirm that the entered URL matches an archived target URL.
2. Try a URL offered under **Aangetroffen seed URL's**.
3. Retain the replay address and relevant console output if the problem
   persists.

The source code does not identify whether an unsuccessful replay is caused by
missing captures, inaccessible WARC files or playback behaviour.

## Limitations

- Playback depends on the local playback service, its collection index and
  access to the original WARC files.
- The suggested URL list is not a complete list of archived URLs. It contains
  at most 200 status-200 HTML response candidates and excludes URLs ending in
  several common resource extensions.
- The manual URL field accepts any non-empty text and performs no format or
  capture-availability validation.
- Warqube uses direct replay mode. The embedded view provides no Warqube
  capture calendar or date selector; use **Open huidige replay in browser** to
  access the pywb calendar.
- The active status confirms that port 8080 is listening. It does not confirm
  that the selected collection or requested URL can be replayed successfully.
- Playback can only use content represented in the local collection index and
  readable from the configured archive location.
- Changing the loaded collection clears the active URL and embedded replay.
- The source code defines no criteria for judging replay completeness or
  fidelity.

## Next steps

Continue to [Identifying duplicate files](deduplicationanalysis.html) to review
repeated payloads and deduplication patterns in the analysed web archive.
