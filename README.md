# Translation file fixer

Browser-based tool for repairing and checking translation export files before they are
loaded into the study. Repairs the problems that are safe to repair, and raises everything
else as a query for the study programmer or the RWS PM.

**Open the tool:** https://dhruvmachave.github.io/translation-file-fixer/

Nothing is uploaded. Files are read in the browser, repaired in memory, and downloaded
back. There is no server, no network call and no storage, so clinical data never leaves
the machine it is opened on.

## What goes in

A ZIP straight from the study folder, or individual CSV or spreadsheet files. Each file
needs the columns `CLIENT`, `CODE`, `ORIGINAL_TEXT`, `LANGUAGE` and `TRANSLATED`.

## What comes out

CSV, named as it arrived with `_updated` added. A single file comes back as a ZIP named
after it; a ZIP comes back as a ZIP. Nested folders are preserved.

## Issues that are repaired

| Issue | What happens |
| --- | --- |
| Text spilled past column E | Rejoined into column E, however far it ran: F, G, or all the way to Z |
| Extra columns | Only the five expected columns come back, and the header is found even if a title row sits above it |
| Quotes wrapping a cell | One removed from the start and one from the end; quotes inside the sentence are kept |
| Hyphen in the language code | `ta-IN` becomes `ta_IN` |
| Untranslated validation messages | Blanked when they match the built-in list word for word |
| Spreadsheets | XLSX and XLS come back as CSV |

## Issues that are flagged

Nothing in the file is changed for these. Each one can be marked **Can be ignored** if it
is expected, which removes it from the query list.

| Issue | What is checked |
| --- | --- |
| Wrong language for the code | Text in the wrong script, such as Hebrew under a Korean code, or letters the language does not use, such as Czech letters under a Danish code |
| English left in a label | A label still in English; copyright lines are listed separately as they are often meant to stay |
| Pseudo language issues | `XXXX` text under a real language code, or a label still in English under `enIM` |
| Tags not closed | `<b>`, `<u>` and `<i>` counted against their closing tags |
| Module in the config | Must be `ecoa`, `diary`, `sitetasks` or `clinros` |
| Site task in the wrong language | Site tasks go to `zhCN`, `jaJP` and `enIM` only; CSSBS and CSSLA go to every language |
| Language code | Must be readable and one of the 68 approved codes |
| File shape | A column name used twice, `TRANSLATED` not last, or a config that cannot be read |

Every repair is applied across every row before any flag is judged, so nothing is flagged
for a problem that has already been fixed.

## Working through a batch

1. Drop the files in.
2. Answer the module questions at the top. Where several files carry the same wrong value,
   one answer can be applied to all of them at once.
3. Read the flagged findings, grouped by kind. Mark anything expected as **Can be ignored**.
4. **Query list** saves the remaining findings as a CSV, with a suggested owner for each, to
   send to the study programmer or the RWS PM.
5. **Download** saves the repaired files.

Ignored findings last for the session only and are cleared by **Start over**.

## Notes

- Everything is in the single `index.html`, including the ZIP and spreadsheet libraries, so
  it works with no internet connection.
- Latin-script languages that share an alphabet cannot always be told apart. Czech under a
  Danish code is caught, because Czech uses letters Danish does not. Mexican Spanish under a
  US Spanish code is not, because it is the same language.
- Marking a finding as ignored never changes the output file.

