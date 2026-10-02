# claude-log-viewer / claude-log-editor

Two single-file HTML tools for reading and editing conversation logs exported from claude.ai (`conversations.json`) in your browser.

![The viewer](images/viewer.png)

> The screenshots show fictional sample data (`sample-conversations.json`).

> [!NOTE]
> **The user interface is in Japanese only.** The tables in [4. Usage](#4-usage) list each Japanese label with its English meaning. In the hacker spirit, the HTML has not been translated. If you want an English UI, the labels are plain strings in the source; feel free to translate them yourself.

A Japanese version of this README is available: [README_JP.md](README_JP.md)

---

## 0. Warning

> [!CAUTION]
> - **This software comes with no warranty. The author accepts no liability for any damage, including information leaks, caused by using it.**
> - **What these tools check is only "which edits have been declared in the JSON you received."** Exports from claude.ai are not signed, so these tools cannot prove that a log is genuine. If you use an edited log as a submission, **keep the original ZIP untouched and record its hash (e.g. `sha256sum conversations-000.zip`).**
> - **The JSON format exported by claude.ai may change.** If it does, these tools may fail to load, save, display, or edit logs correctly.

---

## 1. Purpose

### claude-log-viewer.html (viewer)

A tool for reading exported conversation logs, with a look close to claude.ai.

- Conversation list, title search, and full-text search
- Switching between branches (where a prompt was edited or a response was regenerated)
- Showing or hiding thinking summaries, tool calls, and attachment contents
- Saving the displayed conversation as `.txt` or `.md`, or copying it to the clipboard
- Displaying the redactions, annotations, and cuts made with the editor

### claude-log-editor.html (editor)

A tool for preparing conversation logs to be shown to other people. For example, when a journal, a reviewer, or a conference asks you to submit a record of how you used AI.

- **Redact** messages that cannot be made public (the text is removed; only metadata such as timestamps and the reason remain)
- Add **annotations** to messages after the fact
- **Cut everything above (or below)** a message
- **Delete a whole conversation** (a record of the deletion is kept)

Every edit is written into the JSON as an edit log (`edit_log`), and marks — 伏 (redacted), 注 (annotated), 切 (cut) — appear in a narrow column to the left of each message. Readers can tell at a glance which parts are the original conversation and which are later edits.

![Marks in the left column](images/viewer-marks.png)

---

## 2. How to run

1. Open `claude-log-viewer.html` or `claude-log-editor.html` in your browser (double-click it, or drag and drop it into a browser window).
2. Choose `conversations.json` with the file button at the top left (labelled "Choose File" or similar, depending on your browser), or drop the file onto the page.

No installation, server, or network connection is needed. The data you load is processed entirely inside your browser and is never sent anywhere.

- Recommended browsers: recent versions of Chrome, Edge, Firefox, or Safari
- The editor's 「開く（上書き可）」 (Open, overwritable) button appears only in browsers that support the File System Access API, such as Chrome and Edge. In other browsers, saving downloads a new file.

To try it out, load the bundled `sample-conversations.json` (fictional conversations). `sample-conversations-edited.json` is the same data after editing it with the editor.

---

## 3. Getting your conversation log from claude.ai

### Request an export

Exports are requested from the claude.ai web app or the desktop app (not from the iOS or Android apps).

1. Click your initials (account icon) at the bottom left
2. Select **Settings**
3. Open **Privacy**
4. Click **Export data**

After a while, a download link is sent to your account's email address.

- The link expires after 24 hours. If it expires, simply request a new export.
- You must be signed in to claude.ai to download.
- This applies to individual accounts (Free, Pro, Max). For Team and Enterprise, organization admins handle exports.

Reference: [How can I export my Claude data? | Claude Help Center](https://support.claude.com/en/articles/9450526-how-can-i-export-my-claude-data)

### Find conversations.json

Unpacking the ZIP gives a layout like the following (as of September 2026; the format may change).

```
conversations-000.zip
conversations-000/
  conversations.json        ← load this file
frames-000/                 (artifacts created in conversations)
projects-000/               (projects)
light_metadata-000/
  users.json                (account information)
  login_history.json        (login history)
```

If the names differ, run this in the unpacked folder:

```bash
find . -name 'conversations*.json'
```

Notes:

- If you open `conversations.json` in a text editor, non-ASCII text (such as Japanese) appears as `数学...`. The file is not broken; these are JSON character escapes, and the tools display them correctly.
- `users.json` and `login_history.json` contain personal information such as your email address and login history. Do not include them when you share a conversation log.
- Images pasted into conversations are not included in the export (only their file names remain).

---

## 4. Usage

### Common to the viewer and the editor

| Action | Where (Japanese label) | Description |
|---|---|---|
| Open a file | File button at the top left, or drop onto the page | Load `conversations.json` |
| Title search | Search box at the top left | Filter conversations whose title contains the text |
| Full-text search | Prefix the query with `/` (e.g. `/quadratic`) | Search message text as well |
| Open a conversation | Click it in the list on the left | Show it on the right |
| Switch branches | 「‹ 分岐 2/3 ›」 (branch 2/3) next to a message | Show another version of an edited or regenerated message (the newest is shown by default) |
| Toggle display | 「思考」 (thinking), 「ツール」 (tools), 「添付」 (attachments), 「システム」 (system), 「注釈」 (annotations) at the top | Show or hide each element. Also applies to saved and copied text |
| Save as text | 「.txt 保存」, 「.md 保存」 (save) at the top | Save the displayed conversation (the displayed branch) to a file |
| Copy | 「コピー」 (copy) at the top | Copy the displayed conversation to the clipboard |
| View the edit log | 「編集記録 N 件」 (N edit records) at the top of a conversation | Show when and how each message was edited |
| Theme | 「◐」 at the top left | Switch between light and dark |

### Marks in the left column

| Mark | Meaning |
|---|---|
| 伏 (red) | Redacted message. The reason and metadata are shown instead of the text |
| 注 (amber) | Annotated message. The annotation is shown below the text |
| 切 (blue) | Cut range. Marks the rows "↑ この上にあった N 件…" (N messages above were removed) and "↓ この下にあった N 件…" (N messages below were removed) |
| no mark | Original conversation, unedited |

### Editor only

![The editor](images/editor.png)

| Action | Where (Japanese label) | Description |
|---|---|---|
| Delete a conversation | 「×」 on each item in the list | Removes the text and title, keeping only the uuid, timestamps, message count, and a deletion record. Pressing 「×」 again on a deleted conversation removes it completely, record and all |
| Annotate | 「注釈」 (annotate) at the top right of each message | Opens an input box (Markdown supported). 「注釈を保存」 saves, 「注釈を削除」 deletes |
| Redact | 「内容を伏せる」 (redact) at the top right of each message | Removes the text, attachments, and tool records, keeping only metadata |
| Edit the reason | Input box and 「理由を更新」 (update reason) on a redacted message | Change the displayed reason. Multiple lines are allowed |
| Restore | 「復元」 (restore) on a redacted message | Restore the content as it was before redaction. Possible only until you save the JSON |
| Cut above | 「↑ここより上を削除」 at the top right of each message | Delete this message and everything before it. The next message becomes the start of the conversation |
| Cut below | 「↓ここより下を削除」 at the top right of each message | Delete this message and everything after it (including other branches) |
| Undo | 「元に戻す」 (undo) at the top left | Undo the last edit. Any number of steps, until you save |
| Save JSON | 「JSON を保存」 (save JSON) at the top left | Download the edited JSON as `<original name>-edited.json` |
| Open (overwritable) | 「開く（上書き可）」 at the top left (Chrome and Edge only) | A file opened with this button can be overwritten in place when saving |

### Fields added to the JSON

The editor keeps the original JSON structure and adds the following fields.

| Field | On | Content |
|---|---|---|
| `redacted`, `redaction_note`, `redacted_at` | message | Whether it was redacted, the reason, and when |
| `annotation`, `annotation_updated_at` | message | Annotation text and when it was last updated |
| `cut_above_count`, `cut_below_count` | message | Number of messages removed above (below) this point (accumulates over repeated edits) |
| `edit_log` | conversation | Edit log (timestamp, action, target message, number removed, uuids of removed messages) |
| `deleted`, `deleted_message_count` | conversation | Whether the conversation was deleted, and its original message count |

### Notes

- The original content kept for 「復元」 (restore) lives only in browser memory. If you close or reload the tab before saving, it can no longer be restored.
- The default redaction reason is the Japanese sentence 「この入力・出力は審査の段階で不適切だと判定されたので除去しております」 ("This input/output was removed because it was judged inappropriate at the review stage"). Rewrite it to match what actually happened. To change the default itself, edit the constant `REDACT_NOTE` in the HTML source (it exists in both the viewer and the editor).
- Markdown rendering is minimal. Complex syntax may not display correctly.

---

## 5. About this project

The author made these tools while talking with Claude, to organize their own record of AI usage.

**The author has no plans to maintain this project, add features, or respond to inquiries.** Bug fixes, new features, a formal specification, support for other AI services, an English UI, turning it into an app: please do whatever you like. Forking, modifying, redistributing, and commercial use are all free under the MIT License below. There is no specification document, but each tool is a single HTML file, so the source code should tell you everything.

---

## 6. License

MIT License

Copyright (c) 2026 Anonymous

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

