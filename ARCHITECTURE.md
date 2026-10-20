# AI Meeting Notes & Action Items — Architecture

![Architecture infographic](assets/architecture.png)


**Validate a transcript submission, ask Gemini for structured notes and action items, validate shape, and save the result to Notion.**

## 1. Workflow at a glance

```mermaid
flowchart LR
    A["1. MEETING REQUEST<br/>Title, date, transcript,<br/>request ID"] --> B["2. VALIDATE<br/>ID/title/date + 20–50k chars"]
    B -->|invalid| X["HTTP 400<br/>Invalid request"]
    B -->|valid| C["3. DEDUPE<br/>Request ID in Notion"]
    C --> D{"Already exists?"}
    D -->|yes| E["RETURN EXISTING<br/>No duplicate AI run"]
    D -->|no| F["4. GEMINI<br/>Summarize + extract actions"]
    F --> G{"Provider succeeded?"}
    G -->|no| H["AI ERROR RESPONSE"]
    G -->|yes| I["5. STRUCTURED OUTPUT<br/>Summary + up to 10 actions"]
    I --> J{"Schema valid?"}
    J -->|no| K["INVALID AI OUTPUT"]
    J -->|yes| L["6. SAVE TO NOTION<br/>Meeting-notes page"]
    L --> M{"Saved?"}
    M -->|yes| N["SUCCESS RESPONSE"]
    M -->|no| O["NOTION SAVE ERROR"]
    classDef input fill:#f3f1ff,stroke:#7256bd,color:#16112b,stroke-width:1.5px
    classDef validate fill:#eef5ff,stroke:#4b79c7,color:#102343,stroke-width:1.5px
    classDef ai fill:#eef8f1,stroke:#45895b,color:#17351f,stroke-width:1.5px
    classDef action fill:#fff5e8,stroke:#d98a36,color:#4e2c0c,stroke-width:1.5px
    classDef output fill:#eff9f6,stroke:#238a77,color:#123a32,stroke-width:1.5px
    classDef error fill:#fff0f2,stroke:#cb5367,color:#571d29,stroke-width:1.5px
    class A input
    class B,C,D,J,M validate
    class F,I ai
    class L action
    class E,N output
    class X,H,K,O error
```

## 2. Input and output

| Input | Generated output |
|---|---|
| Request ID, meeting title, ISO date, transcript (20–50,000 characters) | Summary with executive summary, discussion points, decisions, risks; up to 10 action items with person/task/deadline; Notion page |

There is **no human approval step** in the supplied workflow: schema-valid AI output is saved automatically. Schema validation does not establish factual accuracy or confirm that actions/owners were agreed.

## 3. Services used

`n8n` webhook and validation · `Google Gemini` summary/action extraction · `Notion` duplicate lookup and page storage.

## 4. Privacy and security notes

- Source webhook has no authentication configured. Secure it before accepting real transcripts.
- Transcripts may include confidential or personal data and are sent to Gemini, then generated notes are stored in Notion. Review consent/notice, provider terms, access, retention, and deletion.
- Prompt-injection instructions and a JSON schema do not guarantee accurate or safe output. Add human review before distribution/action.
- Protect the Notion database and n8n execution data. Use synthetic transcripts for demos.

> **Portfolio output:** 17 nodes and 10 connection groups. Do not use real meeting content in public demos.
