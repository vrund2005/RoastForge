# 🔥 RoastForge — AI Resume Roaster & Rebuilder

> *Upload your resume. Get roasted. Get rebuilt.*

RoastForge is a **multi-node agentic AI system** built with LangGraph that takes a student or developer's resume, brutally roasts its weaknesses, scores it against ATS standards, generates detailed improvement feedback, reconstructs a stronger version, prepares interview questions based on the profile, exports a polished PDF and Generates role-specific interview questions from the rebuilt resume — all autonomously.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🔥 **Roast Engine** | Savage, accurate, mode-selectable roast (Mild / Savage / Nuclear) |
| 📊 **ATS Scorer** | Scores resume 0–100 against 2026 AI/ML hiring standards |
| 🧠 **Improvement Analyzer** | Identifies top 5 weak areas with specific, actionable fixes |
| 🔄 **Iterative Rebuilder** | Rewrites the resume and re-scores in a loop until ATS > 80 |
| 🎤 **Interview Prep Agent** | Generates role-specific interview questions from the rebuilt resume |
| 📄 **PDF Generator** | Exports a clean, ATS-optimized resume PDF via Playwright |
| ⚡ **Real-time Streaming UI** | SSE-powered frontend — roast appears while research is still running |

---

## 🏗️ Architecture



**Key design decisions:**
- `roast_node` and `ats_node` run in **parallel** (fan-out from `pdf_parser_node`)
- `ats_node` gates the loop — resume rebuilds until score exceeds 80 or 3 iterations complete
- Each node emits **SSE events** so the UI updates in real-time as nodes complete
- Structured output via Pydantic schema ensures the LLM returns valid, parseable resume data

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Agent Framework** | LangGraph (StateGraph, conditional edges, parallel nodes) |
| **LLM** | Groq API — `openai/gpt-oss-120b` / `openai/gpt-oss-20b` |
| **Structured Output** | Pydantic + `llm.with_structured_output()` |
| **PDF Parsing** | PyMuPDF (fitz) |
| **PDF Generation** | Playwright (headless Chromium) + Jinja2 HTML templates |
| **Backend** | FastAPI with SSE streaming (`StreamingResponse`) |
| **Frontend** | Vanilla HTML/CSS/JS — no framework, SSE-native |

---

## 📁 Project Structure

```
RoastForge/
│
├── RoastForge.ipynb       # LangGraph notebook — full agent development
├── requirements.txt
├── Workflow.png
└── README.md
```

---

## 🔄 Agent State

```python
class RoastForgeState(TypedDict):
    pdf_name: str
    pdf_text: str
    roast: str
    ATS_score: int
    improvements: str
    new_resume: dict
    interview_questions: list[str]
    iteration: int
    roast_mode: str                                    # mild | savage | nuclear
    all_pdf_texts:  Annotated[list[str], operator.add] # history across iterations
    all_ats_scores: Annotated[list[int], operator.add] # score progression
```

---

## 🎤 Interview Prep

After rebuilding the resume, `interview_node` generates **role-specific interview questions** based on the candidate's actual projects, tech stack, and experience level. Questions are categorized:

- **Technical Deep-Dives** — based on projects listed (e.g. RAG pipeline internals, LangGraph state management)
- **Behavioral** — drawn from internship and team experience
- **System Design** — appropriate to the candidate's level
- **HR/Culture Fit** — based on career narrative

This means every candidate gets questions unique to *their* resume, not generic lists.

---

## 📊 ATS Scoring Logic

The ATS scorer evaluates resumes against 2026 AI/ML Engineering hiring standards across:

- Keyword density and relevance
- Technical depth of project descriptions  
- Quantified impact (metrics, numbers, percentages)
- Action verb strength
- Structural clarity and section completeness
- Industry-expected skills presence

**Score tiers:**
| Score | Tier |
|---|---|
| 90–100 | S-Tier — Recruiters will fight over you |
| 80–89 | Solid Candidate — Getting callbacks |
| 70–79 | Needs Work — Mediocre but fixable |
| 60–69 | Touch Grass — Rough draft energy |
| 0–59 | Participation Trophy — Bless your heart |

---

## 🖥️ UI Preview

The frontend features:
- Drag-and-drop PDF upload
- 3 roast intensity modes (Mild / Savage ☢️ / Nuclear)
- Live pipeline step visualization — each LangGraph node lights up as it runs
- **Roast appears in real-time** while research node is still running (parallel SSE stream)
- Animated ATS score ring with before/after delta
- Interview questions panel
- One-click PDF download of rebuilt resume

---

## 💡 What Makes This Agentic

This is not a simple LLM call. RoastForge demonstrates:

1. **Multi-node orchestration** — 6 specialized nodes, each with a single responsibility
2. **Parallel execution** — `roast_node` and `ats_node` run simultaneously
3. **Conditional looping** — the graph re-runs the rebuild cycle until ATS threshold is met
4. **Structured state management** — annotated reducers accumulate history across iterations
5. **Tool integration** — PDF parsing, web-standard PDF rendering, streaming API
6. **Real-world output** — produces a downloadable, interview-ready PDF artifact

---

## 🤝 Contributing

PRs welcome. Ideas for improvement:
- Add LinkedIn URL scraping as an input source
- Add job description input for targeted resume tailoring
- Add voice output for interview questions (text-to-speech)
- Deploy to HuggingFace Spaces or Render

---

## 📄 License

MIT License — use it, fork it, roast your own resume with it.

---

*Built by [Vrund Patel](https://github.com/vrund2005) · Powered by LangGraph + Groq*
