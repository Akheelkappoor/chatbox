# README — Chatbox Workflow

## Overview
This repository contains a JSON export of an n8n workflow named **Chatbox**. The workflow implements a portfolio chatbot that receives messages via an HTTP webhook, fetches portfolio data from an API, formats it, passes it to an OpenRouter-backed chat model, and returns a JSON response to the caller.

> **File:** `/mnt/data/Chatbox.json`

## Quick start / import
1. Download the `Chatbox.json` file (path above) into your local machine or server where you run n8n.
2. In n8n, go to **Settings → Workflows → Import**, and import the JSON file.
3. Activate the workflow after reviewing credentials and environment variables.

## Requirements
- n8n (tested with n8n v0.230+)
- HTTP access to the API endpoint `https://akheelkappoor.mooo.com/api/chatbot/portfolio-data`
- Credentials configured in n8n for:
  - `Header Auth account` (used by the webhook and HTTP Request nodes)
  - `OpenRouter account` (used by the OpenRouter Chat Model node)

## Important configuration / credentials
- **Webhook path**: `POST /Portfolio` — the webhook node uses header authentication. Replace or configure the `Header Auth account` credentials in n8n.
- **HTTP Request node**: calls `https://akheelkappoor.mooo.com/api/chatbot/portfolio-data`. Ensure this URL is reachable from your n8n instance and that the header auth credentials are set.
- **OpenRouter Chat Model**: configure your OpenRouter API credentials in n8n as `OpenRouter account`.

## Nodes summary
- **Webhook1** — Receives incoming `POST /Portfolio` requests. Uses header auth and forwards body to the workflow.
- **HTTP Request** — Fetches portfolio data from the external API.
- **Clean** — Formats and prepares the portfolio data for the AI agent (JS code node).
- **Conversation Memory1** — Keeps a short memory window for context.
- **OpenRouter Chat Model1** — Sends prompt + context to the language model.
- **AI Agent** — Langchain agent node responsible for generating the chatbot response. Uses system instructions embedded in the workflow.
- **Format Response** — Extracts/cleans the AI's final output (JS code node).
- **Respond to Webhook** — Returns the cleaned JSON response back to the caller.
- **Sticky Note** — A visual note describing workflow behavior (non-functional).

## Prompt / system instructions
The workflow includes a system message that establishes the assistant role:
- Role: *Akheel's AI Portfolio Assistant* — friendly, professional, 2–4 sentence conversational style.
- Uses live profile data (full name, email, availability) pulled from the portfolio API.

## Example request/response
- **Request**: `POST /webhook/Portfolio` with JSON body containing `message` and optional `sessionId`.
- **Behavior**: The workflow fetches profile data, constructs the prompt for the AI agent, and returns a JSON response with the AI's message.

## Troubleshooting
- If responses are empty:
  - Verify the HTTP Request node can reach the portfolio API and returns valid JSON.
  - Check the `Clean` node (JS code) for runtime errors; open the node and test with sample input.
- If the workflow fails on the OpenRouter node:
  - Confirm OpenRouter API key/credentials are correct and not rate-limited.
  - Check model and token limits configured in the node.
- To debug, turn on **Execution Logging** in n8n and inspect node inputs/outputs.

## Security considerations
- Protect credentials in n8n (Header Auth, OpenRouter). Do not store secrets in the JSON file on public repositories.
- If exposing the webhook publicly, ensure proper authentication and rate-limiting.

## Editing the system prompt
To adjust the assistant's tone, open the **AI Agent** node and edit the `systemMessage` in the node parameters.

## Where to find the file
Local path: `/mnt/data/Chatbox.json` (uploaded to this environment). Use this path to import or download the JSON if needed.

## License
MIT — adapt as needed.

---
If you'd like changes (more detail, example payloads, troubleshooting steps, or a downloadable `.md` file), tell me what to add and I will update the README.

