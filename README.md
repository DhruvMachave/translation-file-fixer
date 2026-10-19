# Translation file fixer

A browser tool for repairing and checking translation export files before they are loaded
into a study. It repairs the defects that have one correct answer, checks the language of
the text against the language code on the file, and reports everything else as a query for
the study programmer or the Translation Vendor project manager.

**Open the tool:** https://dhruvmachave.github.io/translation-file-fixer/

Only the page itself is downloaded from the link. The files you process are read in your
browser, repaired in memory and downloaded back. They are never sent anywhere, and nothing
is stored in the browser.

## Quick start

1. Open the link and drop in the delivery: a ZIP taken straight from the study folder, or
   individual `.csv`, `.xlsx` or `.xls` files. Each file needs the columns `CODE`,
   `ORIGINAL_TEXT` and `TRANSLATED`. `CLIENT` and `LANGUAGE` are expected as well.
2. Answer any questions shown at the top of the page (module, language code), then click **Next**.
3. Select a file to see what was repaired and what was flagged. Mark anything that is
   expected as **Can be ignored**.
4. Use **Query list** to save the outstanding findings as a CSV to send on.
5. Use **Download** to save the corrected files.

**Start over** clears everything, including anything marked as ignorable.

A ZIP can come from macOS or Windows. The system files each leaves behind (`__MACOSX`, `.DS_Store`, `Thumbs.db`,
`desktop.ini` and the `~$` temporary files Office keeps beside an open workbook) are skipped, folders written with
a backslash are read as folders, and a file that cannot be read is reported on its own without stopping the rest
of the ZIP.

## What comes out

Every file is returned as CSV, encoded UTF-8 with a byte order mark so Excel opens
non-Latin scripts correctly. Names keep the original with `_updated` added before the
extension, so `pro_phq92001-a_es_US.csv` comes back as `pro_phq92001-a_es_US_updated.csv`.
Spreadsheets become `.csv`.

| You add | You get |
| --- | --- |
| A ZIP | A ZIP named after it, with `_updated` on each file and folder. Nested ZIPs become folders. |
| One file | A ZIP named after that file. **Download this file** gives the bare CSV. |
| Several loose files | `translation_files_updated.zip` |

## What the tool changes

| Issue | What happens |
| --- | --- |
| Text spilled past column E | Rejoined into column E, however many columns it ran across. |
| Extra columns or a title row | Only `CLIENT`, `CODE`, `ORIGINAL_TEXT`, `LANGUAGE` and `TRANSLATED` come back. Any row above the column names, such as the study name, is removed, so only the column names sit at the top. Any duplicate or invalid column is also removed. |
| Quotation marks around a cell | One at the very start and one at the very end of a `TRANSLATED` cell are removed. Quotes inside the sentence are kept. |
| Language code with a hyphen | `ta-IN` becomes `ta_IN`. |
| Untranslated validation message | A `TRANSLATED` cell holding one of the eight messages listed below, word for word, is made blank. |
| XLSX or XLS | The first worksheet is saved as CSV. |

Two changes are made only when you choose them: the module in the questionnaire
configuration, and the `LANGUAGE` value in every row. Both are described under
[Questions the tool asks](#questions-the-tool-asks).

Every repair is applied to every row before any check is judged, so nothing is flagged for
a problem that has already been repaired.

## What the tool does not change

- `CLIENT`, `CODE` and `ORIGINAL_TEXT` are never altered. No row is added, removed or
  reordered, apart from completely blank rows.
- No translation is written, corrected, improved or moved. Text left in English, text in
  the wrong language and wording that differs from the source are reported, not edited.
- Quotation marks inside a sentence are left alone.
- A formatting tag with no closing tag is reported, not repaired.
- The questionnaire configuration is not rewritten unless you answer the module question,
  and the configuration of individual questions is never touched.
- The files you upload are never overwritten. Corrected copies are produced for download.

## What it flags

Nothing in the file is changed for these. Each is a query to raise with the study
programmer or the Translation Vendor project manager, unless you mark it as expected.

| Finding | What it means |
| --- | --- |
| English text in a label | A label is still in English. Copyright and trademark lines are listed separately as **Legal notice still in English**, because they are often left that way on purpose. |
| Wrong language for this code | The text is not in the language the code names. See [Language checking](#language-checking). |
| Language code not recognised | The `LANGUAGE` column holds something that is not a code, such as `Spanish (US)`, or is empty. This is flagged even when the file name carries a usable code. |
| Language code not on the approved list | A well-formed code that is not one of the 68 below. Flagged only. |
| Formatting tag not closed | `<b>`, `<u>` or `<i>` is not matched by its closing tag. The counts on each side are given. |
| Module needs checking | The module in the configuration is not one of `ecoa`, `diary`, `sitetasks` or `clinros`. |
| Config could not be read | The configuration cannot be read, even allowing for quotation marks inside labels, so the module cannot be checked. Check the module in the configuration by hand. |
| Site task not localised into this language | A site task in a language other than `zhCN`, `jaJP` or `enIM`. `CSSBS` and `CSSLA` are expected in every language. |
| Pseudo language findings | Under `enIM`: a label that still reads as English, or digits that differ from the source. Under any other code: labels made of `XXXX`, meaning the pseudo build went out under the wrong code. The finding says how many labels are affected and, when only some of them are, names them. |

## Questions the tool asks

Questions appear in a box at the top of the page. They never block anything: **Download**
works whether or not they have been answered.

### Choosing, saving and ignoring

A choice in the box is only a choice until you click **Next**. **Next** is the one forward button:
it saves everything you have chosen, across every file, and moves on to the next question that is
still open. Nothing reaches a file before then, so you can change your mind, go back with
**Previous**, or start again. With nothing chosen, **Next** simply moves on and saves nothing.

- **Choose** a value by clicking it (or typing it, or picking it from the list). Click the chosen
  value again to take it back. The box says what is chosen and that it is not saved yet.
- **Ignore**, beside **Previous**, leaves the tech file as delivered for that question. The
  finding stays in the query list. Click **Ignored** to undo it; **Next** then closes the question.
  **Bring back** reopens it afterwards.
- **Next** with a choice waiting updates the tech file, so the files you download carry the
  answer. A green note says what was saved.
- **Undo** takes the last save back. Its answers come out of the tech file, so the files you
  download are as they were before it, and its questions are asked again with your earlier
  choices selected. Change them if you like and click **Next** to save again: only then does the
  output carry the new answers. Each save can be undone in turn, and files added since are kept.
- **Discard choices** clears everything that has not been saved.
- Choices that wait to be saved are counted as **unsaved** in the summary and marked **choice not
  saved** beside the file. They are not in the files you download, and the browser asks before
  you leave the page with choices waiting.

The box is for changes to the tech file. The **Can be ignored** buttons on the findings further
down the page are a different thing: they only take a finding off the query list, and never
change the tech file.

### Module

When a file's module is not one of the four accepted values, choose `ecoa`, `diary`,
`sitetasks` or `clinros`, or type another value. Where several files declare the same
value, one answer can be applied to all of them. This is checked by default; clear it to
answer for that file only. A file with a choice of its own, or one you ignored, is never
changed by someone else's answer. When you click Next, the `modules` entry of the top-level
configuration is rewritten, and nothing else in it.

If the configuration itself cannot be read, the module cannot be checked, so no question is
asked. The file is flagged as **Config could not be read** and the configuration is left as
it was delivered.

### Language code

A question is raised when the `LANGUAGE` value cannot be read, or when the text clearly
does not match the code. The dropdown offers the 68 approved codes, each with its language
and country.

- A **Suggested** row of buttons comes first when the tool can guess the right code: the code
  in the file name, or the codes of the language it recognized in the text. One click chooses it.
  The list starts on a prompt, so nothing is chosen until you choose it. Typing in the search box
  narrows the list; when what you type is exactly one code (such as `ko_KR`, `es us` or `korean`),
  that code appears in the dropdown, selected and chosen. With several matches nothing is chosen
  for you. Change or clear the text and a code chosen by typing is taken back; press Enter to keep it.
- The search box narrows the list by code, language or country. `span`, `es us`, `mexico`
  and `czechia` all work.
- When you click **Next**, the chosen code is written into every row of `LANGUAGE`, and the
  file is checked again against it. Flags that depended on the old code disappear and the rest
  remain. If the code you choose still does not fit the text, the question comes back.
- If a file also has a module question, both choices are applied by the same Next.
- Answers already given to a module question are kept, and findings marked as ignorable
  stay ignored.

Only the 68 approved codes can be chosen. A code outside them is flagged, as above, but
does not raise a question.

## Language checking

Text is accepted under a code only when it is in that code's language.

- **Languages with a script of their own** (Korean, Hebrew, Greek, Tamil, Telugu and the
  other Indic scripts) are settled by script. Text in another script is flagged, and so is
  Latin text under one of these codes unless it is the English source copied across.
- **Latin, Cyrillic and Devanagari languages** are compared by counting how many of the
  file's words are among each language's most common function words. A file is flagged when
  another language is a clearly better match than the declared one. Pairs that are very
  close (Danish and Norwegian, Czech and Slovak, and the Cyrillic Russian, Ukrainian,
  Bulgarian and Serbian) are compared on the words only one of them uses.
- **Chinese and Japanese** are told apart by Japanese kana, **Arabic and Urdu** by the
  letters only one of them uses, and **Bengali and Assamese** likewise.
- Letters that do not belong to the declared language, such as Czech letters in a file
  coded Danish, are reported with the labels that carry them.

The checks look at the file as a whole. A few labels in another language do not make the
file wrong, and a file that is just the English source copied across is reported as English
text, not as a wrong code.

## Findings and the query list

Findings are grouped by kind for each file. **Can be ignored** marks one finding, or a whole
group, as expected. Ignored findings leave the outstanding count and the query list, and can
be brought back. Ignoring never changes the output file, and it lasts for the session only.

**Query list** saves a CSV with one row per outstanding finding:

`FILE`, `LANGUAGE`, `QUERY`, `CODE`, `SOURCE`, `TRANSLATED`, `DETAIL`

A finding that names labels lists every one of them in `DETAIL`. The screen shows the first
ten.

## Reference

**Modules:** `ecoa`, `diary`, `sitetasks`, `clinros`

**Site tasks** are expected only in `zhCN`, `jaJP` and `enIM`, except `CSSBS` and `CSSLA`,
which are expected in every language.

**Pseudo language:** `enIM`. Every letter should be `X`, and digits carry through unchanged.

**Validation messages that are blanked** when they appear in `TRANSLATED` exactly:

- This is a required question
- You need to select at least one answer
- You cannot select more than one answer
- You cannot provide a value below zero
- You cannot select more than six answers
- You can only add one answer
- One answer must be selected
- This question must be answered

<details>
<summary>The 68 approved language codes</summary>

First priority: `arIL` `asIN` `bnIN` `csCZ` `daDK` `deAT` `deDE` `elGR` `enAU` `enCA` `enGB`
`enIN` `esAR` `esES` `esMX` `esPR` `esUS` `frBE` `frCA` `frFR` `guIN` `heIL` `hiIN` `huHU`
`itIT` `jaJP` `knIN` `koKR` `mlIN` `mrIN` `nlBE` `nlNL` `orIN` `paIN` `plPL` `ptBR` `roRO`
`ruIL` `ruRU` `ruUA` `skSK` `taIN` `teIN` `trTR` `ukUA` `urIN` `zhCN` `zhTW`

Second priority: `bgBG` `deBE` `deCH` `enIE` `enMY` `esCO` `frCH` `hrHR` `itCH` `ltLT`
`lvLV` `msMY` `nbNO` `ruLT` `ruLV` `srRS` `sbRS` `taMY` `zhMY`

Pseudo: `enIM`

`srRS` is Serbian in Cyrillic and `sbRS` is Serbian in Latin. Codes are matched whether
written `esUS`, `es_US` or `es-US`.

</details>

## Known limits

- Regional variants of one language are not told apart. Spanish text is accepted under any
  `es_` code, English under any `en_` code, and Simplified and Traditional Chinese are not
  distinguished. Croatian and Serbian in Latin script are treated as one language.
- A language is only judged when the file holds about 100 words of translated text.
- Languages outside the 68 are not identified, so text in one of them under a similar code
  could be accepted.
- Languages that share a script with another can only be separated by the methods above.
  Very short answer-style labels carry few distinguishing words.
- Malformed markup is reported where it is detected but is not corrected, because the
  correct position cannot be determined.
