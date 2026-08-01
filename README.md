# n8n-ai-chatbot
An n8n workflow that powers a conversational AI chatbot using the LangChain Agent node. The agent has chat memory, a language model (OpenAI), and access to a sub-agent "tool" that can clean and normalize lead form data.


## What it does

- Listens for incoming chat messages (via n8n's **Chat Trigger**)
- Routes the message to an **AI Agent** node powered by **OpenAI (gpt-5-mini)**
- Keeps short-term conversation context using a **Buffer Window Memory**
- Can call a secondary tool — itself an AI agent (powered by **Google Gemini** with **MongoDB**-backed long-term memory) — to clean and structure raw lead/form data into a consistent JSON format (name split, phone formatting, email validation, etc.)

## Workflow structure

| Node | Role |
|---|---|
| `When chat message received` | Entry point — triggers on incoming chat messages |
| `AI Agent` | Main agent that understands the user's message and decides what to do |
| `OpenAI Chat Model` | Language model powering the main AI Agent |
| `Simple Memory` | Short-term conversation memory for the main agent |
| `AI Agent Tool` | A sub-agent used as a callable **tool** — cleans/normalizes lead data into structured JSON |
| `Google Gemini Chat Model` | Language model powering the `AI Agent Tool` sub-agent |
| `MongoDB Chat Memory` | Persistent memory for the `AI Agent Tool` sub-agent |
| `Call n8n Workflow Tool` | Placeholder tool node for calling another n8n workflow (not yet configured — see Known limitations) |
| `Schedule Trigger` | Present in the workflow but currently **not connected** to anything (inactive path) |

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` → select `My_workflow.json`

2. **Add your own credentials** (the original credential IDs won't work for you — you must reconnect each one):
   - OpenAI API key → used by `OpenAI Chat Model`
   - Google Gemini (PaLM) API key → used by `Google Gemini Chat Model`
   - MongoDB connection → used by `MongoDB Chat Memory`

3. **Configure the unfinished tool node**
   `Call n8n Workflow Tool` needs a target workflow selected before it will work — currently it has no workflow configured.

4. **Activate the workflow**

5. **Get your chat link**
   Open the `When chat message received` node → it provides a public chat URL/webhook you can share or embed.

## Known limitations

- `Schedule Trigger` exists in the workflow but is **not wired to anything** — it currently does nothing.
- `Call n8n Workflow Tool` is a placeholder — you must point it at an actual sub-workflow.
- The lead-data-cleaning tool is implemented as a **full AI agent**, not a simple script — responses depend on the model and aren't guaranteed to be perfectly deterministic JSON. Add an output parser/validation step downstream if you need strict guarantees.

## Requirements

- An n8n instance (cloud or self-hosted) with the **LangChain (`@n8n/n8n-nodes-langchain`)** nodes available
- OpenAI API key
- Google Gemini API key
- A MongoDB database (for persistent sub-agent memory)

## License

MIT — feel free to use, modify, and adapt.
