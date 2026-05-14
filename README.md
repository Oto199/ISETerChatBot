# ISET AI Assistant Agent

**Course:** AI Agents · TSU · Final Project  
**Author:** Oto Kantidze  
**Submission:** 2026-05-13

---

## What this agent does

A student-focused **Practical Assistant Agent** built for ISET (International School of Economics at Tbilisi State University). The agent helps first-year students with everyday academic tasks through a conversational interface:

- **Answers economics and statistics questions** step-by-step using an LLM backbone.
- **Looks up the class schedule** — students can ask *"What do I have on Thursday?"* and get a structured answer pulled from the integrated timetable.
- **Fetches live exchange rates** (Frankfurter API) for macroeconomics context — e.g. *"What is GEL/USD today?"*
- **Fetches country or university data** (REST Countries / Universities API) to support research questions.
- **Holds a multi-turn conversation** using a LangGraph checkpointer and thread IDs.
- **Pauses for human confirmation** before any action flagged as destructive or irreversible (e.g. clearing session notes, confirming a calendar entry).

---

## Tech Stack

| Layer | Technology |
|---|---|
| Agent framework | LangGraph (`StateGraph`, `MemorySaver`, `interrupt`) |
| LLM | Google Gemini Flash (`@google/genai`) |
| Tools | LangChain `@tool` — custom + REST API wrappers |
| Structured output | Pydantic `BaseModel` (schedule query response) |
| Frontend | React, TailwindCSS, Lucide Icons, React Router |
| Backend | Node.js + Express (serves the React build, proxies LLM calls) |
| Build | Vite (frontend) + esbuild (server bundle) |

---

## Agent Architecture

```
User message
    │
    ▼
[agent node] ── calls tools? ──► [tool node] ── loops back ──┐
    │                                                         │
    └──────────────── no tools needed ◄──────────────────────┘
    │
    ▼
  interrupt? ── yes ──► [human_review node] ── approved? ──► run action
    │                                       └── denied?  ──► skip action
    │
    ▼
 final response → user
```

The graph is compiled with a `MemorySaver` checkpointer. Every conversation uses a unique `thread_id` so context is preserved across turns.

---

## Tools

| Tool | Type | Source |
|---|---|---|
| `get_class_schedule(day)` | Custom Python | In-memory timetable dict |
| `get_exchange_rate(base, target)` | REST API | Frankfurter (`frankfurter.app`) |
| `get_country_info(name)` | REST API | REST Countries (`restcountries.com`) |
| `add_study_note(text)` | Custom Python | In-memory list (destructive — triggers interrupt) |
| `clear_notes()` | Custom Python | Destructive — always triggers interrupt |
| `list_notes()` | Custom Python | Read-only |

---

## Prerequisites

- Python 3.11+
- Node.js v18+
- `pip` and `npm`
- A **Google Gemini API key** (free tier works)

---

## Setup — get running in under 10 minutes

### 1. Clone / download the repo

```bash
git clone <your-repo-url>
cd iset-ai-assistant
```

### 2. Python environment (agent / notebook)

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

`requirements.txt` includes:
```
langgraph
langchain
langchain-google-genai
langchain-community
pydantic
python-dotenv
```

### 3. Node environment (React UI + Express server)

```bash
npm install
```

### 4. Environment variables

Create a `.env` file in the project root:

```env
GEMINI_API_KEY="your_api_key_here"
```

The Python notebook reads this via `python-dotenv`. The Express server reads it at startup.

### 5. Run the Jupyter notebook

```bash
jupyter notebook final_project.ipynb
```

Run all cells top-to-bottom. The last section contains the 5 test queries and the interrupt demo.

### 6. Run the web UI (optional)

```bash
# Development
npm run dev
# App available at http://localhost:3000

# Production build
npm run build
npm start
```

---

## Project Structure

```
.
├── final_project.ipynb      # Main submission notebook
├── requirements.txt         # Python dependencies
├── package.json             # Node dependencies and scripts
├── server.ts                # Express backend — proxies Gemini calls
├── vite.config.ts           # Vite config with env variable injection
└── src/
    ├── App.tsx              # Router and layout
    └── components/
        ├── Home.tsx         # Landing page
        ├── Chat.tsx         # Chat UI — connects to agent backend
        ├── Schedule.tsx     # Class schedule viewer
        ├── About.tsx        # About page
        └── Navbar.tsx       # Navigation
```

---

## Notebook Sections

1. Title + description
2. Setup (installs, API key)
3. Tools (all `@tool` functions with docstrings)
4. State schema + `StateGraph` definition + Mermaid diagram
5. Five test queries (each cell prints full message trajectory)
6. Destructive call — **approved** (action runs)
7. Destructive call — **denied** (action skipped)
8. Eval table (5 entries, honest pass/fail)

---

## Eval Table

| # | Query | Expected | Pass? | Notes |
|---|---|---|---|---|
| 1 | "What classes do I have on Monday?" | Full Monday schedule | ✅ | Correct tool call, structured output |
| 2 | "What is the GEL to USD rate today?" | Live exchange rate | ✅ | Frankfurter API responded correctly |
| 3 | "Tell me about Georgia the country" | Capital, language, currency | ✅ | REST Countries returned correct data |
| 4 | "Clear all my notes" | Interrupt → user denies → skipped | ✅ | `interrupt()` fired, denial respected |
| 5 | "Quiz me on supply and demand" | Step-by-step explanation | ✅ | LLM-only, no tool needed |

---

## Human-in-the-Loop Demo

The agent calls `interrupt()` before executing any tool marked destructive. The graph pauses, prints a confirmation prompt, and waits. Resuming with `Command(resume="yes")` runs the action; `Command(resume="no")` skips it and tells the user.

```python
# Inside the tool node — simplified
if tool_name in DESTRUCTIVE_TOOLS:
    decision = interrupt(f"About to run '{tool_name}'. Confirm? (yes/no)")
    if decision.lower() != "yes":
        return "Action cancelled by user."
```

---

## Notes

- The web UI (`Schedule` page) mirrors the `get_class_schedule` tool — same timetable data, rendered as a visual table for quick reference without going through the chat.
- `MemorySaver` is used for the notebook demo. For persistent sessions across restarts, swap in `SqliteSaver` (see LangGraph docs).

---

*Developed for the AI Agents course — ISET / TSU, 2026.*
