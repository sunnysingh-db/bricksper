# 🎙 Bricksper

**Hold a key, speak, let go: polished text appears wherever you type.**
Bricksper is a Mac menu-bar app that turns your voice into clean, well-punctuated text, using an AI model in **your own Databricks workspace**.

---

## ✨ Features

- **Push-to-talk anywhere:** works in Slack, email, docs, browsers and code editors (default shortcut **⌥ Space**).
- **Polished output:** fixes grammar, spelling and punctuation, removes "um" and "uh", and formats lists, paragraphs and emails.
- **Sign in with Databricks:** one click, using your company login (SSO and MFA). No tokens to copy.
- **Fast:** speech is turned into text on your Mac while you talk, so results arrive in about a second.
- **Multilingual:** English, Hindi, Hinglish, Spanish, French, German and more.
- **Customizable:** edit the prompt and add your own vocabulary in the Prompt tab.
- **Never loses your words:** if the model is unavailable, your raw text is pasted instead.
- **Sound cues:** Tink (listening), Pop (released), Glass (pasted).

---

## 🏢 Why Databricks

- **Your data stays yours:** text goes only to your organization's Databricks workspace, with no third-party dictation service.
- **Governed AI:** requests run through Databricks model serving, where admins can track usage and cost, log requests, set rate limits and control access with Unity Gateway (AI Gateway) and Unity Catalog.
- **Your identity, your permissions:** each person signs in as themselves, and the sign-in expires after 24 hours.
- **Choice of models:** use any chat model your workspace offers and switch whenever you like.
- **No new vendor:** it runs on the platform you already use.

---

## 🧭 Architecture

```
   Your Mac                                         Your Databricks workspace
 ┌──────────────────────────────┐                ┌───────────────────────────────┐
 │ 1. Hold ⌥Space & speak       │                │                               │
 │ 2. Speech → text (on device) │ ── text ─────▶ │ 3. AI model polishes the text │
 │ 5. Text pasted at cursor     │ ◀─ polished ── │ 4. Governed by AI Gateway     │
 │    Sign-in kept in Keychain  │                │    + Unity Catalog            │
 └──────────────────────────────┘                └───────────────────────────────┘
```

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

- You act as **yourself**, so Bricksper can only use the models your Databricks permissions allow.
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
| **Last request sent to the model** | Shows exactly what was sent: the model endpoint, the settings and the full prompt. Click **Refresh** after a dictation. |

**Ideas for the prompt:**

- **Multilingual:** paste the multilingual prompt (see `docs/multilingual-prompt.txt`) so Bricksper replies in the language and script you spoke.
- **Tone:** add a line such as *"Write in a concise, professional tone suitable for customer emails."*
- **Team style:** add your formatting preferences, for example *"Use British spelling"* or *"Always use bullet points for lists."*

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

**Uninstall:**

```bash
bash ~/Downloads/install_bricksper.sh --uninstall
```

---

> Bricksper is a field-built reference app, not an officially supported Databricks product.