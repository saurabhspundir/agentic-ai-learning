# Project 0.1 — Reflection Loop Agent | Complete Lesson

**Phase 0 | Week 0–1 | Deliverable:** `reflection_agent.ipynb`
**Time estimate:** 3 hours | **LLM:** Ollama (free, local)

---

## The Big Idea

You are implementing the **Reflection Pattern** — one of the 4 core agentic design patterns
identified by Andrew Ng. Instead of asking an LLM once and accepting the answer, you:

1. **Think** — Get an initial answer
2. **Reflect** — Have the LLM critique its own answer
3. **Refine** — Produce an improved answer using the critique

This pattern lives inside your **Orchestrator Agent**. When the orchestrator receives outputs
from 3 mini-agents, it uses reflection to score urgency before surfacing items to you.

---

## Why Ollama (Not OpenAI)?

We use Ollama for Weeks 0–8 because:
- **Zero cost** — runs on your local machine, no API key needed
- **No rate limits** — iterate freely while learning
- **Same code** — switching to Groq/OpenAI later is a 1-line change

The 4-tier LLM routing strategy in your plan starts at Tier 0 (Ollama).
You will add Groq and OpenAI in Week 9.

---

## Course Prerequisites

Watch these BEFORE or DURING this project:

**1. Coursera — Agentic AI Primer (Vanderbilt) — Modules 1 & 2**
- URL: https://www.coursera.org/learn/agentic-ai (free audit)
- Watch time: ~45 minutes
- Key takeaway: The perceive → plan → act → reflect loop

**2. Andrew Ng — Agentic AI Workflows (YouTube)**
- Search: `Andrew Ng agentic AI workflows DeepLearning 2024`
- Watch time: ~60 minutes
- Key takeaway: Reflection pattern explained with Python pseudocode (~25 min mark)

> Watch Module 1 of Coursera BEFORE Cell 1.
> Watch Andrew Ng BEFORE Cell 2 — he explains this exact pattern.

---

## Environment Setup

Run all commands in terminal before writing any code:

```bash
# 1. Pull the model (one-time download, ~2GB)
ollama pull llama3.2

# 2. Verify it works
ollama run llama3.2
# Type: "Hello, what is 2+2?" — verify it responds
# Exit with: /bye

# 3. Install Python dependencies
pip install langchain langchain-ollama notebook

# 4. Navigate to your project folder
cd C:\dev\agentic-ai-learning\deeplearning-agentic-ai

# 5. Launch Jupyter
jupyter notebook
# Create new Python 3 notebook → rename to reflection_agent.ipynb
```

---

## Cell 1 — Setup and Mock Data

**Purpose:** Connect to Ollama and create realistic fake calendar data.

```python
from langchain_ollama import OllamaLLM

# OllamaLLM wraps the locally running Ollama service
# model="llama3.2" tells it which downloaded model to use
llm = OllamaLLM(model="llama3.2")

# Mock data: simulates what the Day Brief Agent will eventually fetch
# from Google Calendar API + task management system
mock_context = """
Today's Calendar:
- 9:00 AM  : Team standup (15 min)
- 11:00 AM : Code review — PR #142 auth refactor
- 2:00 PM  : 1:1 with manager
- 4:00 PM  : Free block (no meetings)

Pending Tasks:
- Fix auth bug in login flow (HIGH priority — due TODAY, blocking 2 teammates)
- Write unit tests for payment module (MEDIUM — due end of week)
- Review Jira backlog for next sprint (LOW — no deadline)

Question: What should I focus on today?
"""

print("Setup complete — LLM connected, mock context ready.")
```

**Run this cell first.** You should see "Setup complete" with no errors.
If you get a connection error, run `ollama serve` in a separate terminal window.

---

## Cell 2 — Think → Reflect → Refine

**Purpose:** Implement the 3-stage reflection loop. This is the core agent logic.

```python
# ═══════════════════════════════════════════════════════════
# STAGE 1: THINK
# Ask the LLM for an initial answer.
# ═══════════════════════════════════════════════════════════

initial_prompt = f"""
You are a personal productivity assistant.
Given the following context about a developer's day, suggest what they should
focus on today. Be concise and practical — output a short prioritised list.

Context:
{mock_context}
"""

initial_answer = llm.invoke(initial_prompt)

print("=== INITIAL ANSWER ===")
print(initial_answer)
print()


# ═══════════════════════════════════════════════════════════
# STAGE 2: REFLECT
# Have the LLM critique its own answer from a different angle.
# Key insight: changing the PERSONA from "helper" to "critic"
# forces the model to evaluate rather than just confirm.
# ═══════════════════════════════════════════════════════════

reflection_prompt = f"""
You are a critical productivity reviewer.
Examine the following suggestion and evaluate it honestly:
1. What is good about this recommendation?
2. What is missing or could be improved?
3. Is the prioritisation logical given blocking dependencies?
4. Is anything overlooked?

Suggestion to review:
{initial_answer}
"""

critique = llm.invoke(reflection_prompt)

print("=== CRITIQUE ===")
print(critique)
print()


# ═══════════════════════════════════════════════════════════
# STAGE 3: REFINE
# Use the critique to produce a better answer.
# Both the original AND the critique are passed in —
# this is what drives visible improvement.
# ═══════════════════════════════════════════════════════════

refinement_prompt = f"""
You are a personal productivity assistant.
You previously gave this recommendation:
{initial_answer}

A critical reviewer gave this feedback:
{critique}

Using the critique, produce an improved final recommendation.
Be concise, specific, and actionable. Output a clear prioritised focus list.
"""

final_answer = llm.invoke(refinement_prompt)

print("=== FINAL ANSWER ===")
print(final_answer)
```

**Read all 3 outputs.** Ask yourself:
- Is the FINAL answer more specific and actionable than the INITIAL?
- Did the CRITIQUE catch something the first answer missed?
- If outputs look identical, see troubleshooting in `project-0.1-hints.md`

---

## Cell 3 — Package as a Reusable Agent Function

**Purpose:** Wrap the 3-stage logic into a callable function. This is the agent interface pattern.

```python
def reflection_agent(user_context: str) -> dict:
    """
    Reflection Loop Agent.

    Implements: Think -> Reflect -> Refine
    Accepts any context string, returns all reasoning stages.

    Args:
        user_context: String containing calendar, tasks, and a question.

    Returns:
        dict with keys: initial_answer, critique, final_answer, reasoning_trace

    Note:
        reasoning_trace is currently a list of stage names.
        In Week 9, this becomes a structured LangSmith trace with
        timestamps, token counts, and model metadata per stage.
    """

    # Stage 1: Think
    think = llm.invoke(
        f"You are a productivity assistant. "
        f"What should the user focus on today? Be concise.\n\n{user_context}"
    )

    # Stage 2: Reflect
    reflect = llm.invoke(
        f"You are a critical reviewer. Evaluate this productivity suggestion. "
        f"What is good, missing, or illogical?\n\n{think}"
    )

    # Stage 3: Refine
    refine = llm.invoke(
        f"You are a productivity assistant. "
        f"Improve this recommendation using the critique. Be concise and actionable.\n\n"
        f"Original: {think}\n\nCritique: {reflect}"
    )

    return {
        "initial_answer"  : think,
        "critique"        : reflect,
        "final_answer"    : refine,
        "reasoning_trace" : ["think", "reflect", "refine"]
    }


# Run it with the mock context
result = reflection_agent(mock_context)

print("=" * 50)
print("AGENT RUN COMPLETE")
print("=" * 50)
print("\nFinal Answer:\n", result["final_answer"])
print("\nReasoning Trace:", result["reasoning_trace"])
```

---

## Optional Extension

Add a 4th stage for the voice brief endpoint (Week 20):

```python
summary = llm.invoke(
    f"Summarise this recommendation in a single sentence (max 20 words):\n\n{refine}"
)
# Add to return dict: "summary": summary
# Add to trace: "reasoning_trace": ["think", "reflect", "refine", "summarise"]
```

---

## Completion Checklist

- [ ] Cell 1 runs without errors, prints confirmation
- [ ] Cell 2 prints all 3 labelled sections (INITIAL / CRITIQUE / FINAL)
- [ ] FINAL answer is visibly more actionable than INITIAL answer
- [ ] `reflection_agent()` function is defined and callable
- [ ] Calling it with different input text still works correctly
- [ ] You can explain each stage without looking at the code

---

## Connection to Your Final Project

| This project | Week 13+ (Orchestrator) |
|---|---|
| `mock_context` string | Real output from Day Brief + GitHub mini-agents |
| `think` stage | Orchestrator drafts urgency scores for all items |
| `reflect` stage | Orchestrator critiques: "Am I surfacing the right things?" |
| `refine` stage | Orchestrator produces final brief for user |
| `reasoning_trace` list | LangSmith trace: token costs, latency, model used |

---

*Completed: Project 0.1*
*Next: Project 0.2 — Multi-Agent Handoff (Week 1–2)*
