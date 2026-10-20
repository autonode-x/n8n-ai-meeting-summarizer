# AI Meeting Notes & Action-Item Extraction

> **Architecture:** See [ARCHITECTURE.md](ARCHITECTURE.md) for the infographic, detailed flow, and trust boundaries.

An n8n workflow template that accepts a meeting title, date, and transcript; checks a request ID for duplicates; asks Google Gemini to produce structured meeting notes and action items; validates the output shape; and saves the result to a configured Notion database.

> **Sensitive-data warning:** Meeting transcripts may contain confidential business information, employee/customer details, and personal data. The source webhook export does **not** show authentication configured. Do not expose or activate it until access control, privacy, retention, and human review requirements are addressed.

## Project snapshot

- **Category:** n8n workflow automation, AI summarization, meeting operations
- **Workflow size:** 17 nodes, 10 connection groups in the supplied export
- **AI provider in source:** Google Gemini via n8n LangChain
- **Persistence:** Notion database
- **Input size cap:** Transcript must be 20–50,000 characters
- **Human approval:** None in the source flow; valid model output is saved automatically

## What it does

1. Receives a POST request with `request_id`, `meeting_title`, `date`, and `transcript`.
2. Normalizes and validates the request ID, title length, strict `YYYY-MM-DD` date, and transcript length.
3. Queries a Notion database for the request ID. A duplicate returns an existing-record response; a lookup failure returns an error branch.
4. Sends the meeting title/date/transcript to Gemini with instructions to treat the meeting record as data and return the connected JSON schema.
5. Validates the summary structure, limits actions to 10, and bounds action person/task/deadline fields.
6. Saves the generated summary and actions as a Notion database page; invalid AI output, provider errors, and Notion failures have separate response paths.

## Request fields

| Field | Required | Validation in source template |
|---|---:|---|
| `request_id` | Yes | 8–100 characters: letters, digits, `_`, `-` |
| `meeting_title` | Yes | 2–120 characters |
| `date` | Yes | Valid calendar date in `YYYY-MM-DD` format |
| `transcript` | Yes | 20–50,000 characters |

## Output format

- **Summary:** Includes `Executive Summary`, `Key Discussion Points`, `Decisions Taken`, and `Risks` sections.
- **Actions:** Up to 10 entries, each with a person, task, and deadline. Unspecified values should remain explicitly unspecified rather than guessed.

The schema checks structure and length; it does not verify the factual accuracy of the summary or whether the action owner/deadline was actually agreed in the meeting.

## Import and configure

1. Import `workflow.json` into a test n8n workspace.
2. **Before activation, configure strong webhook authentication.** The export has no authentication field despite the original node name. Add rate limits, request-size caps, replay protection, and abuse monitoring.
3. Create a least-privilege Notion integration, grant it access only to the intended private database, and bind the credential in n8n.
4. Replace `REPLACE_WITH_YOUR_NOTION_DATABASE_ID` after import.
5. Configure n8n execution-data retention so transcripts are not retained longer than necessary. Limit access to executions, Notion pages, and backups.
6. Test invalid payloads, duplicate request IDs, malformed AI output, provider errors, Notion permission failures, and concurrent duplicate submissions using synthetic transcripts.
7. For real meetings, provide appropriate notice/consent and review model-provider data use, privacy obligations, retention, access, and deletion processes before processing transcripts.
8. Add a human review/approval step before distributing notes or assigning actions. Never treat generated notes as an authoritative record without review.

## Security and privacy

The sanitized public export removes credential bindings, the Notion database ID/URL, webhook IDs, and the source n8n instance ID. The endpoint path is changed and the missing-auth issue is called out in-flow. This cleanup does not revoke credentials or undo any prior disclosure.

- **Webhook security:** No authentication is configured in the source. A public endpoint could allow anyone to submit transcripts for processing and Notion storage. Secure it before activation.
- **Confidentiality:** The transcript is sent to Gemini and the resulting notes are saved in Notion. Review provider terms, retention/training settings, data residency, legal basis, and meeting-participant notice before using real data.
- **Data minimization:** The current flow sends the full transcript up to 50,000 characters and stores generated notes/actions. Consider redaction of names, contact information, credentials, client details, and other unnecessary sensitive content before model processing.
- **Prompt injection:** The prompt treats meeting text as data, but prompt instructions are not a guarantee. Do not expose tools, credentials, or external actions to the model.
- **No human gate:** AI output is saved automatically after schema validation. Review summaries and action assignments before sharing, publishing, or executing them.
- **Duplicate race:** Notion lookup followed by create is not necessarily atomic. Add a unique constraint/lock or idempotency strategy if simultaneous identical requests are possible.
- **Credential hygiene:** Keep Notion and model credentials in n8n's vault. Rotate any credentials ever shared publicly and review repository history.

## Known limitations

- Output can hallucinate, omit nuance, misattribute speakers, or invent action owners/deadlines despite instructions and schema checks.
- There is no source-audio verification, speaker diarization, language detection, or human approval step.
- Notion schema must match the page/database properties configured in the workflow.
- The flow does not provide transcript redaction or a dedicated status-query API.
- No security certification, legal compliance, or transcription accuracy guarantee is implied.

## Suggested freelancing portfolio description

> Built an n8n meeting-notes workflow that validates transcript submissions, deduplicates requests, generates structured summaries and action items with Google Gemini, validates output shape, and stores notes in Notion. Includes separate error paths and configurable private integrations. The public template documents transcript privacy, missing-auth hardening, human review, and idempotency limitations.

## License

Publish only if you own or have permission to redistribute the workflow and prompts. Review n8n, Notion, Gemini, and third-party node terms before use.


## Synthetic request and response examples

- [Sample request](sample/request.json)
- [Sample successful response](sample/response.json)

These examples use synthetic demonstration data and show the successful route only. HTTP status codes are returned separately by the workflow. Configure authentication and credentials in your own n8n instance before activation.
