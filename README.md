# 🎙 Bricksper v1.3

**Hold a key, speak, let go: polished text appears wherever you type.**
Bricksper is a Mac menu-bar app that turns your voice into clean, well-punctuated text, using an AI model in **your own Databricks workspace**. Start with **"Genie…"** and it answers data questions out loud instead, pasting the answer from **Databricks Genie** right at your cursor.

---

## ✨ Features

- **Push-to-talk anywhere:** works in Slack, email, docs, browsers and code editors (default shortcut **⌥ Space**).
- **Polished output:** fixes grammar, spelling and punctuation, removes "um" and "uh", and formats lists, paragraphs and emails.
- **Sign in with Databricks:** one click, using your company login (SSO and MFA). No tokens to copy.
- **Fast:** speech is turned into text on your Mac while you talk, so results arrive in about a second.
- **Multilingual:** English, Hindi, Hinglish, Spanish, French, German and more.
- **Customizable:** edit the prompt and add your own vocabulary in the Prompt tab.
- **Never loses your words:** if the model is unavailable, your raw text is pasted instead.
- **Genie voice mode (new in v1.3):** say *"Genie, which city had the most late deliveries in October 2026?"* and the answer is pasted where you are typing, with follow-up questions that remember the conversation.
- **Right format for every app (new):** Genie answers paste as real tables in Google Docs, Word and Gmail, as cells in Sheets and Excel, as a slide outline in Slides and PowerPoint, and as Slack-style text in Slack. Links stay clickable.
- **Automatic charts (new):** when Genie answers with a table that suits a chart, your own model (through Unity Gateway) picks a bar, line or donut chart and writes an insight title. Bricksper draws it on your Mac and pastes it with the answer.
- **Live progress (new):** a seconds counter and Genie's latest step show in the menu bar while it works. Press the shortcut again to cancel.
- **Keeps your clipboard:** whatever you had copied (text, images, files) is restored after each paste.
- **Sound cues:** see [Sounds](#-sounds).

---

## 🏢 Why Databricks

- **Your data stays yours:** text goes only to your organization's Databricks workspace, with no third-party dictation service.
- **Governed AI:** requests run through Databricks model serving, where admins can track usage and cost, log requests, set rate limits and control access with Unity Gateway (AI Gateway) and Unity Catalog.
- **Your identity, your permissions:** each person signs in as themselves, and the sign-in expires after 24 hours.
- **Choice of models:** use any chat model your workspace offers and switch whenever you like.
- **Governed answers from Genie:** Genie uses your certified Genie spaces, metric views and business definitions, and only sees data you are allowed to see.
- **No new vendor:** it runs on the platform you already use.

---

## 🧭 Architecture

```
   Your Mac                                          Your Databricks workspace
 ┌───────────────────────────────┐               ┌────────────────────────────────────┐
 │ 1. Hold ⌥Space & speak        │               │                                    │
 │ 2. Speech → text (on device)  │               │                                    │
 │    Starts with "Genie"?       │               │                                    │
 │      no  ── text ───────────────────────────▶ │ 3a. AI model polishes the text     │
 │      yes ── question ───────────────────────▶ │ 3b. Genie One queries your data    │
 │                               │               │     (Genie spaces, metric views)   │
 │ 5. Formatted for the app      │ ◀── answer ── │ 4. Governed by Unity Gateway       │
 │    under your cursor & pasted │               │    + Unity Catalog permissions     │
 │    Sign-in kept in Keychain   │               │                                    │
 └───────────────────────────────┘               └────────────────────────────────────┘
```

- **Dictation:** `POST /serving-endpoints/{model}/invocations`, or `/ai-gateway/mlflow/v1/chat/completions` for Unity Gateway model services (`catalog.schema.name`).
- **Genie:** the generally available **Genie One MCP server** `system.ai.genie_one_mcp`, called through Unity Gateway at `/ai-gateway/mcp-services/system.ai.genie_one_mcp?o=<workspace-id>` (`genie_ask`, then `genie_poll_response` until the answer is ready). If that is refused, Bricksper falls back to the Beta REST API `POST /api/2.0/genie/one/responses`.

---

## 🚀 Quickstart (about 10 minutes)

**1. Install Apple's developer tools (one time).** Open Terminal and run:

```bash
xcode-select --install
```

**2. Run the installer.** Save `install_bricksper.sh` to your Downloads folder, then run:

```bash
cd ~/Downloads
bash install_bricksper.sh
```

**3. Allow the permissions.** Click **Allow** for Microphone and Speech Recognition (and for incoming connections, if asked).

**4. Sign in.** In the Bricksper window, enter your **Databricks workspace URL**, click **Sign in with Databricks**, and log in in your browser. Wait for **"✓ Ready"**.

**5. Turn on auto-paste.** Go to **System Settings → Privacy & Security → Accessibility**, switch **Bricksper** on, then run:

```bash
pkill -x Bricksper; open -a Bricksper
```

**6. Try it.** Click into any text box, hold **⌥ Space**, speak, and let go. ✅

**7. Try Genie.** In **Settings → Connection**, click **Test Genie**. Then click into a Google Doc, hold **⌥ Space** and say *"Genie, how many catalogs can I access?"*

> **Upgrading from v1.2?** Run the installer again, then **Sign out** and **Sign in with Databricks** once so your sign-in includes the `ai-gateway` scope that Genie needs. Reinstalls are now faster because the build cache is kept.

---

## 🧞 Genie voice mode

Start your dictation with **"Genie"** and Bricksper sends your question to **Databricks Genie One** instead of polishing it. Genie picks the right Genie space or table, runs the query and writes the answer, which Bricksper pastes at your cursor.

**How to ask:**

| Say | What happens |
|---|---|
| *"Genie, which supplier had the most late purchase orders in 2026?"* | Starts a new question. |
| *"Genie, and also, which categories do they supply?"* or *"Genie, follow up: …"* | Continues the last Genie conversation (within 5 minutes). |
| *"Genie, new question, …"* | Forces a fresh conversation. |
| *"Hi Genie, …"*, *"Hey Genie, …"*, *"Okay Genie, …"* | Greetings and filler words before "Genie" are ignored. |

**While Genie works (usually 20 to 45 seconds):**

- The menu-bar icon turns **purple** with a **seconds counter**.
- Hover over the icon, or open the menu, to see Genie's latest step (for example *"Querying the Dark Store Ops Copilot space"*).
- **Press your shortcut again** (or choose **Cancel Genie Question** in the menu) to cancel. You'll hear **Bottle** and nothing is pasted.
- If you switch to another app while waiting, the answer is **copied, not pasted** (Funk sound), so it never lands in the wrong window. Press ⌘V where you want it.

**Pasted in the right format for each app:**

| Where your cursor is | What gets pasted |
|---|---|
| Google Docs, Word, Pages, Gmail, Teams, most other apps | Rich text: headings, bold, bullet lists, real tables and clickable links |
| Google Sheets, Excel, Numbers | Only the table, one value per cell |
| Google Slides, PowerPoint, Keynote | A slide outline: headline, first paragraph, table and bullets |
| Slack (app or browser) | Slack-style text: `*bold*`, • bullets, tables as code blocks. Tables that contain links become bullet lists so the links stay clickable |
| Terminal, VS Code, Cursor, Xcode | Plain text |

Genie's internal citations ("Query Result", source footnotes) are removed in every format. **🎙 → Paste Last Genie Answer** pastes the last answer again in rich-text format.

**Automatic charts:**

When a Genie answer contains a table, Bricksper sends only the column names and up to 20 rows to the model you chose for dictation and asks it for the best chart, the columns to use, a one-line insight title and which bar to highlight. If the model is unavailable or its choice doesn't fit the table, built-in rules decide instead (dates → line, categories → bar, shares of a whole → donut, otherwise no chart). The chart is drawn on your Mac, so query results never go to a chart service.

| Where your cursor is | Chart behaviour |
|---|---|
| Google Docs, Word, Gmail, Teams | Chart image inserted above the table |
| Google Slides, PowerPoint, Keynote | Only the chart image is pasted (the title is in the image) |
| Slack | Text answer first, then the chart is pasted as an image attachment |
| Google Sheets, Excel, Terminal, code editors | No chart: the table only, so you can build a native chart |

Charts add about 2 seconds. Turn them off with **Settings → Connection → Turn Genie tables into charts**. The log shows `CHART bar chosen by model: …` or why a chart was skipped.

**Tips for good, fast answers:**

- Name the business area and a fixed time period, for example *"in October 2026"*, rather than *"last 30 days"*.
- Ask for *"rounded percentages"* if you want clean numbers in tables.
- Broad questions about the whole workspace (such as *"top tables by row count"*) take longer, because Genie has to search system tables.
- To turn Genie off, untick **Enable Genie voice mode** in **Settings → Connection**. "Genie" is then dictated like any other word.

---

## 🔐 Sign in with Databricks (OAuth U2M)

Bricksper uses Databricks **OAuth user-to-machine (U2M)** sign-in, so you never create, copy or store a personal access token.

**How it works:**

1. You click **Sign in with Databricks**. Bricksper opens your workspace's normal login page in your browser.
2. You log in as usual (SSO and MFA apply). Databricks sends a one-time code back to Bricksper on your Mac, through `http://localhost:8020`.
3. Bricksper exchanges the code for short-lived tokens, using PKCE so the code can't be reused by anyone else.
4. The tokens are stored securely in the **macOS Keychain**. The access token lasts about an hour and is refreshed automatically in the background.
5. After **24 hours**, Bricksper asks you to sign in again. One click.

**Good to know:**

- You act as **yourself**, so Bricksper can only use the models and data your Databricks permissions allow.
- Bricksper asks for the scopes `all-apis offline_access ai-gateway genie`: `ai-gateway` for the Genie One MCP server and `genie` for the Beta REST fallback. If your workspace rejects a scope, Bricksper signs in again automatically with fewer scopes (first without `genie`, then without both). Dictation keeps working either way.
- **Settings → Connection** and the menu bar show **"✓ Signed in as you@company.com until …"**. Click **Sign out** to end the session at any time.
- **Admins:** by default Bricksper uses the built-in `databricks-cli` client ID. For an organization-wide rollout, register your own OAuth app and set `CLIENT_ID` at the top of `install_bricksper.sh`.
- **Fallback:** if your workspace doesn't allow browser sign-in, tick **Advanced → Use a personal access token** in Settings.

---

## 📝 Prompt tab

Open **🎙 → Settings… → Prompt** to control how your speech is polished.

| Section | What it does |
|---|---|
| **System prompt** | The instructions sent to the AI model with every dictation. Edit them to change tone, formatting or language rules, then click **Save Prompt**. Keep the `<<<` and `>>>` markers, because Bricksper wraps your speech in them. |
| **Reset to Default** | Restores the built-in prompt. |
| **Custom vocabulary** | A comma-separated list of names and terms (e.g. `Databricks, Unity Catalog, Lakebase`). It helps your Mac hear these words correctly, and tells the model how to spell them. |
| **Last request sent (model or Genie)** | Shows exactly what was sent: the model endpoint, the settings and the full prompt, or for Genie the endpoint, your question and the conversation ID. Click **Refresh** after a dictation. |

**Ideas for the prompt:**

- **Tone:** add a line such as *"Write in a concise, professional tone suitable for customer emails."*
- **Team style:** add your formatting preferences, for example *"Use British spelling"* or *"Always use bullet points for lists."*

---

## 🔔 Sounds

| Sound | Meaning |
|---|---|
| **Tink** | Listening |
| **Pop** | Key released, working on it |
| **Glass** | Polished text pasted |
| **Morse** (or Purr) | Genie answer pasted |
| **Funk** | Copied to the clipboard but not pasted (Accessibility off, or you switched apps during a Genie question) |
| **Basso** | Something failed, so your raw text or question was pasted |
| **Bottle** | Genie question cancelled |

---

## 🛠 Troubleshooting

| Problem | Fix |
|---|---|
| Installer says "Command Line Tools are being installed" | Finish Apple's install, then run the installer again. |
| No 🎙 icon in the menu bar | It may be hidden behind the MacBook notch. Quit a few menu-bar apps, or open Bricksper from Applications. |
| Sign-in page shows an error | Use the workspace address only (e.g. `https://adb-123….azuredatabricks.net`) and try again. |
| "Could not listen on localhost:8020" | Another sign-in is running (e.g. `databricks auth login`). Close it and try again. |
| "HTTP 403" after signing in | You don't have access to AI models in this workspace. Ask your Databricks admin. |
| Text is copied but not pasted (Funk sound) | Turn on Accessibility (step 5) and restart Bricksper. |
| Basso sound and Settings opens | Your 24-hour sign-in expired. Click **Sign in with Databricks** again. |
| Shortcut does nothing | Close the Settings window, or choose a different shortcut. |
| Output ignores your prompt changes | Click **Save Prompt**, then check **Last request sent to the model** after your next dictation. |
| Words are missing or the output looks odd | Click 🎙 → **Open Log** to see what was heard and what the model returned. |
| Said "Genie…" but it was dictated as normal text | Make "Genie" the first word (greetings like "Hi" are fine). The log shows `GENIE not triggered (transcript starts with: …)`, which tells you what the Mac heard. Also check that **Genie: enabled** shows in the menu. |
| Genie answer is your question pasted back (Basso) | The log says why. `Genie scope not available` means you should **Sign out** and **Sign in** again; on some workspaces Genie One may not be enabled. |
| Test Genie shows "Genie is not reachable in this workspace" | Bricksper tried both the Genie One MCP server and the Beta REST API. First **Sign out** and **Sign in with Databricks** so your sign-in includes the `ai-gateway` scope. If it still fails, ask an admin to check that you can use `system.ai.genie_one_mcp` (**Unity Gateway → MCPs**) and have **CAN USE** on a SQL warehouse. The log shows `GENIE MCP HTTP …` with the exact reason. Dictation keeps working either way. |
| Genie takes a long time | 20 to 45 seconds is normal. Watch the counter on the icon, or press the shortcut again to cancel. Narrow questions with a fixed date range are fastest. |
| Genie answer was copied, not pasted (Funk) | You switched apps while Genie was thinking. Press ⌘V where you want the answer. |
| Slack shows raw `*` or backticks | Turn on **Slack → Preferences → Advanced → Format messages with markup**. |
| Table pasted as plain text in Google Docs | Press ⌘V (not ⌘⇧V, which pastes without formatting). |
| No chart appeared | Open the log: `CHART skipped` means the model (or the rules) decided a chart would not help, for example a single row or text-only columns. Also check that **Turn Genie tables into charts** is ticked and the cursor isn't in Sheets, Excel or a code editor. |
| Chart missing in Word or Teams but the table pasted | Some apps ignore images embedded in pasted rich text. Use **🎙 → Paste Last Genie Answer** in Google Docs, or paste the answer into Slides, where the chart is pasted as a plain image. |
| Shortcut does nothing after you changed it | Another app may use the same keys. The log shows `Shortcut … could not be registered`; pick a different shortcut. |

**Log file:** `~/Library/Logs/Bricksper.log`, rotated to `Bricksper.old.log` at 5 MB. It records what was heard, what was pasted and every Genie step.

**Uninstall:**

```bash
bash ~/Downloads/install_bricksper.sh --uninstall
```

---

> Bricksper is a field-built reference app, not an officially supported Databricks product.