# Google Form — Field Schema

This documents the structure of the Google Form used to collect meeting transcripts.

---

## Form Title
**Meeting Notes Summarizer**

## Form Description
> Submit your meeting transcript below and receive an AI-generated summary in your inbox within minutes. You can either paste the transcript as text or upload a file (.txt, .docx, or .pdf).

---

## Fields

| # | Field Label | Type | Required | Notes |
|---|---|---|---|---|
| 1 | Meeting Title | Short answer | No | Used as the email subject line and summary header. If blank, Gemini infers from transcript. |
| 2 | Your Email Address | Short answer | Yes | Destination for the summary email. Validated as email format. |
| 3 | Paste your transcript here | Paragraph (long text) | No | For plain-text transcripts copied directly from a tool like Zoom, Teams, or Fathom. |
| 4 | Or upload your transcript file | File upload | No | Accepts .txt, .docx, .pdf. Max file size: 10MB. |

> **Note:** Fields 3 and 4 are both optional individually, but the form description instructs users to fill at least one. A future improvement would be to add server-side validation in Make.com that checks for this and sends an error email if both are empty.

---

## Make.com Field Mapping

| Google Form Field | Make.com Variable |
|---|---|
| Meeting Title | `{{1.answers.meeting_title.value}}` |
| Email Address | `{{1.answers.email.value}}` |
| Pasted transcript | `{{1.answers.transcript_text.value}}` |
| Uploaded file | `{{14.data}}` (binary, passed through `toString()`) |

---

## Handling Both Input Types

The Make.com scenario uses a **Router module** to handle the two transcript input types:

- **Path A — Text input:** If the pasted transcript field is non-empty, pass it directly to Gemini as a string.
- **Path B — File upload:** If a file is uploaded, use `toString(14.data)` to convert binary to text before passing to Gemini.

If both fields are filled, Path A takes priority (text is passed to Gemini, file is ignored).
