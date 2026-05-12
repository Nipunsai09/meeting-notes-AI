# 🗒️ Meeting Notes AI — Automated Transcript Summarizer

An end-to-end automation that transforms raw meeting transcripts into structured, professional summaries and delivers them directly to your inbox — no manual note-taking required.

Built with **Google Forms**, **Make.com**, and **Google Gemini**.

---

## 📌 What It Does

Most meeting recordings produce long, messy transcripts. This tool takes that transcript — either pasted as text or uploaded as a file — runs it through a structured AI prompt, and emails back a clean, scannable summary within seconds of form submission.

**Input:** Raw transcript (timestamped, multi-speaker)  
**Output:** Formatted HTML email with meeting purpose, key takeaways, topic breakdown, action items, and highlighted moments

---

## 🎬 Demo

### Submission Form
> A simple Google Form accepts either a pasted transcript or a file upload (.txt, .docx, .pdf).

![Form Screenshot](assets/form-preview.png)

### Sample Output Email
> See [`sample-output/sample-summary.html`](sample-output/sample-summary.html) for a full rendered example.

---

## 🏗️ Architecture

```
Google Form Submission
        │
        ▼
   Make.com Scenario
        │
        ├─ Extract transcript text
        │   ├── If text input → use directly
        │   └── If file upload → parse binary → toString()
        │
        ├─ Send to Google Gemini API
        │   └── Structured prompt → returns HTML-formatted summary
        │
        └─ Gmail module
            └── Send summary email to submitter
```

**Modules used in Make.com:**
| Module | Purpose |
|---|---|
| Google Forms — Watch Responses | Trigger on new form submission |
| Tools — toString() | Convert file binary to readable text |
| Google Gemini — Generate Content | Run transcript through summarization prompt |
| Gmail — Send an Email | Deliver HTML summary to submitter |

---

## 📋 Output Format

The AI returns a structured HTML email containing:

| Section | Description |
|---|---|
| **Meeting Title & Date** | Extracted from transcript header if available |
| **Meeting Purpose** | One-sentence description of why the meeting occurred |
| **Key Takeaways** | 3–5 high-level outcomes or decisions |
| **Topics — Detailed Breakdown** | Bold-headed sections per major discussion topic |
| **Next Steps** | Actionable follow-up items identified in the transcript |
| **Annotations** | Moments flagged as `HIGHLIGHT` or `BOOKMARK` in the transcript |

---

## 🧠 The Prompt

The full system prompt used to instruct Gemini is documented in [`prompt.md`](prompt.md).

Key design decisions:
- Output is explicitly requested as **HTML** (not markdown) so it renders directly in Gmail without any post-processing
- Speaker names and timestamps are preserved in the context window so the model can attribute action items correctly
- `HIGHLIGHT` and `BOOKMARK` tags in the transcript are used as anchors for the Annotations section — useful for tools like Fathom.ai that export tagged moments

---

## 📁 Form Fields

See [`schema/form-fields.md`](schema/form-fields.md) for the full Google Form structure.

**Short version:**
- Meeting title (short text, optional)
- Submitter email (for delivery)
- Transcript — text input (long text, optional)
- Transcript — file upload (.txt / .docx / .pdf, optional)
- *At least one of the two transcript fields must be filled*

---

## 🔧 How to Replicate This

You can rebuild this automation in Make.com in under an hour:

1. **Create the Google Form** using the fields in [`schema/form-fields.md`](schema/form-fields.md)
2. **Create a new Make.com scenario** and add the modules listed in the Architecture section above
3. **Copy the prompt** from [`prompt.md`](prompt.md) into the Gemini module's prompt field
4. **Connect your Gmail account** in the Gmail module and map the submitter email field to the recipient
5. **Run a test submission** using the sample transcript in [`sample-output/sample-transcript.txt`](sample-output/sample-transcript.txt)

> **Note:** You will need a Make.com account (free tier works), a Google account, and access to the Gemini API (free tier available via Google AI Studio).

---

## 📂 Repository Structure

```
meeting-notes-ai/
├── README.md                        ← You are here
├── prompt.md                        ← Full Gemini system prompt
├── schema/
│   └── form-fields.md               ← Google Form field definitions
└── sample-output/
    ├── sample-transcript.txt        ← Fake transcript used for testing
    └── sample-summary.html          ← Rendered HTML output from the automation
```

---

## 💡 Potential Extensions

- **Slack delivery** — post the summary to a channel instead of (or in addition to) email
- **Google Docs archiving** — save each summary to a shared Drive folder for searchable history
- **Multi-language support** — add a language selector to the form and pass it as a parameter to the prompt
- **Calendar integration** — pull meeting title and date automatically from a Google Calendar event ID

---

## 👤 Author

**Nipun Sai Krishnamaneni**  
CS @ Drexel University | Concentrating in AI/ML  
[LinkedIn](https://www.linkedin.com/in/nipun-sai-krishnamaneni-b52a8823b/)
