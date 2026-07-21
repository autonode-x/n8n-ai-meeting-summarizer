# 🤖 AI Meeting Summarizer with n8n

> Production-ready AI Meeting Assistant built using **n8n**, **Groq Llama 3.3**, **Structured Output Parsing**, and **Notion**.

Automatically convert meeting transcripts into structured meeting notes, executive summaries, and actionable tasks using AI.

---

## 🚀 Features

- AI-powered Meeting Summarization
- Action Item Extraction
- Structured JSON Outputs
- Notion Database Integration
- Webhook API
- Parallel AI Processing
- Input Validation
- Production-ready Workflow
- Retry & Error Handling
- Easy Deployment

---

## 🏗 Workflow Architecture

```
Webhook
    │
    ▼
Validate Request
    │
    ▼
──────────── Parallel Processing ────────────
│                                         │
▼                                         ▼
Meeting Summary Agent          Action Items Agent
│                                         │
────────────── Merge Results ──────────────
                │
                ▼
        Save into Notion
                │
                ▼
        Return API Response
```

---

## 🛠 Tech Stack

| Tool | Purpose |
|------|----------|
| n8n | Workflow Automation |
| Groq Llama 3.3 70B | AI Processing |
| Structured Output Parser | Reliable JSON Output |
| Notion API | Knowledge Base |
| Webhook | API Endpoint |

---

## 📥 API Request

### Endpoint

```
POST /meeting
```

### Sample Request

```json
{
  "meeting_title": "Weekly Sales Meeting",
  "date": "2026-07-21",
  "transcript": "John discussed sales targets. Sarah will prepare the report by Friday. Rahul will update the CRM."
}
```

---

## 📤 API Response

```json
{
  "success": true,
  "meeting": "Weekly Sales Meeting",
  "date": "2026-07-21",
  "status": "Saved to Notion"
}
```

---

## 📂 Project Structure

```
05.n8n-ai-meeting-summarizer
│
├── workflow.json
├── README.md
├── LICENSE
├── .gitignore
│
├── assets
│   ├── workflow.png
│   └── architecture.png
│
├── screenshots
│   ├── workflow.png
│   ├── webhook-test.png
│   ├── ai-summary.png
│   ├── action-items.png
│   ├── notion-page.png
│   └── success-response.png
│
└── sample
    ├── request.json
    └── response.json
```

---

## 📸 Screenshots

| Workflow | Notion |
|----------|---------|
| Workflow Overview | Generated Meeting Notes |
| AI Summary | Action Items |
| Webhook Test | API Response |

---

## 💡 Example Output

### Executive Summary

- Meeting objectives achieved
- Sales performance reviewed
- Next sprint planned

---

### Action Items

| Person | Task | Deadline |
|--------|------|----------|
| Sarah | Prepare Report | Friday |
| Rahul | Update CRM | Tomorrow |

---

## 🔒 Production Features

- Request Validation
- Structured Outputs
- Retry Logic
- Error Handling
- Clean API Responses
- Modular Workflow
- Easily Extendable

---

## 🔮 Future Improvements

- Google Calendar Integration
- Zoom Meeting Integration
- Microsoft Teams Support
- Slack Notifications
- Email Reports
- PDF Meeting Minutes
- Multi-language Support
- Speaker Identification

---

## 👨‍💻 Author

**Piyush Kumar**

AI Automation Engineer

- GitHub: https://github.com/autonode-x
- LinkedIn: https://www.linkedin.com/in/piyush-ai

---

## ⭐ Support

If you found this project helpful, please consider giving it a ⭐ on GitHub.