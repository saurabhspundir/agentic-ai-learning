# Project 0.1 — Reflection Loop Agent | Student Worksheet

> **Your mission:** Build a working Reflection Loop Agent in a Jupyter notebook.
> This file has NO answers — only hints and scaffolding. Fill in every `___________`.
>
> Stuck on a cell? → Open `project-0.1-hints.md`
> Still stuck after hints? → Open `project-0.1-answers.md` (last resort)

---

## Before You Start — Environment Checklist

Run each command in your terminal. All must pass before writing any code.

| Check | Command | Expected Result |
|---|---|---|
| Ollama installed | `ollama --version` | Prints a version number |
| Model downloaded | `ollama run llama3.2` | LLM responds to a test message |
| Python packages | `pip show langchain langchain-ollama` | Shows package info |
| Jupyter works | `jupyter notebook` | Browser opens |

**Any failures? → See `project-0.1-hints.md` Section 0**

---

## File to Create

Open Jupyter and create:
```
C:\dev\agentic-ai-learning\deeplearning-agentic-ai\reflection_agent.ipynb
```

---

## Recommended Course Order

| Step | Course | What to Watch | Time |
|---|---|---|---|
| 1st | Coursera — Agentic AI Primer (Vanderbilt) | **Modules 1 & 2 only** | ~45 min |
| 2nd | Andrew Ng — Agentic AI Workflows (YouTube) | **Full video** | ~60 min |

> Watch Step 1 BEFORE Cell 1. Watch Step 2 BEFORE Cell 2.
> Search YouTube: `Andrew Ng agentic AI workflows DeepLearning 2024`

---

## ─────────────────────────────────────────
## Cell 1 — Setup & Mock Data
## ─────────────────────────────────────────

**Goal:** Import the LLM, connect to Ollama, create fake calendar data.

**Think first:**
- What is a "local LLM" vs a cloud API? Why use one during learning?
- What does "mock data" mean and why do we use it before building real integrations?

```python
# TASK 1: Import the LLM wrapper class from langchain_ollama
# Hint: You need a class that wraps a local Ollama model.
# Classes in LangChain that wrap LLMs often end in "LLM".
from ___________ import ___________

# TASK 2: Instantiate the LLM pointing at your local llama3.2 model
# Hint: The parameter that specifies which model to use is called "model"
llm = ___________(___________)

# TASK 3: Create a mock_context string containing ALL of:
#   - A fake calendar for today (at least 3 time-blocked events)
#   - A list of 2-3 pending tasks with priority levels
#   - The question at the end: "What should I focus on today?"
mock_context = """
___________
"""

# TASK 4: Print a confirmation message so you know Cell 1 succeeded
print(___________)
```

**Self-check before Cell 2:**
- [ ] `llm` created without errors
- [ ] `mock_context` has calendar events, tasks, and the question
- [ ] Confirmation printed in output

---

## ─────────────────────────────────────────
## Cell 2 — Think → Reflect → Refine
## ─────────────────────────────────────────

**Goal:** Implement the 3-stage reflection loop. This is the heart of your agent.

**Before coding:** Can you explain what each stage does in one sentence?
- THINK: ___________
- REFLECT: ___________
- REFINE: ___________

```python
# ═══════════════════════════════════════
# STAGE 1: THINK
# Purpose: Get an initial answer from the LLM
# ═══════════════════════════════════════

# TASK 1: Write a prompt that:
#   - Gives the LLM a persona ("You are a productivity assistant...")
#   - Injects mock_context using an f-string
#   - Asks what the user should focus on today
initial_prompt = f"""
___________
{mock_context}
"""

# TASK 2: Call the LLM with this prompt
# Hint: LangChain LLM objects have a method called .invoke() that takes a string
initial_answer = llm.___________(initial_prompt)

print("=== INITIAL ANSWER ===")
print(initial_answer)
print()


# ═══════════════════════════════════════
# STAGE 2: REFLECT
# Purpose: Have the LLM critique its OWN answer
# ═══════════════════════════════════════

# TASK 3: Write a reflection prompt that:
#   - Gives the LLM a DIFFERENT persona — a critical reviewer, not a helper
#   - Passes in initial_answer (NOT mock_context) as the thing to evaluate
#   - Asks: what's good? what's missing? is the prioritisation logical?
reflection_prompt = f"""
___________
{___________}
"""

# TASK 4: Call the LLM with the reflection prompt
critique = llm.___________(___________)

print("=== CRITIQUE ===")
print(critique)
print()


# ═══════════════════════════════════════
# STAGE 3: REFINE
# Purpose: Produce an improved answer using BOTH original + critique
# ═══════════════════════════════════════

# TASK 5: Write a refinement prompt that:
#   - References BOTH initial_answer AND critique
#   - Asks for a final, improved, actionable recommendation
refinement_prompt = f"""
___________
Original answer: {___________}
Critique received: {___________}
"""

# TASK 6: Call the LLM and store the improved final answer
final_answer = llm.___________(___________)

print("=== FINAL ANSWER ===")
print(final_answer)
```

**After running — answer these:**
1. Is the FINAL answer more specific than the INITIAL answer? In what way?
2. How is Stage 2 (Reflect) different from just asking the question again?
3. What software process is this similar to? (Hint: think about how code gets reviewed)

---

## ─────────────────────────────────────────
## Cell 3 — Package as a Reusable Function
## ─────────────────────────────────────────

**Goal:** Wrap the 3-stage logic into a callable function. This is the "agent interface" pattern.

```python
# TASK 1: Define a function called reflection_agent
# - Input: user_context (a string)
# - Output: a dict with EXACTLY these 4 keys:
#     "initial_answer", "critique", "final_answer", "reasoning_trace"

def ___________(user_context: str) -> dict:
    """
    Reflection Loop Agent.
    Pattern: Think -> Reflect -> Refine
    Returns all stages so the reasoning trace is inspectable.
    """

    # TASK 2: Re-implement the 3 LLM calls
    # CRITICAL: Use user_context (the parameter), NOT mock_context (the global)
    # This is what makes the function reusable with ANY input

    think  = llm.invoke(f"___________\n\n{___________}")

    reflect = llm.invoke(f"___________\n\n{___________}")

    refine  = llm.invoke(
        f"___________\n\nOriginal: {___________}\nCritique: {___________}"
    )

    # TASK 3: Return all 4 keys in a dict
    # reasoning_trace is a list of stage name strings — this becomes LangSmith later
    return {
        "initial_answer"  : ___________,
        "critique"        : ___________,
        "final_answer"    : ___________,
        "reasoning_trace" : ["think", "reflect", "refine"]
    }


# TASK 4: Call your function with mock_context, print the final answer
result = ___________(mock_context)
print("Final Answer:", result[___________])
```

**Optional challenge:**
- Add a 4th LLM call: `"summary"` — a single-sentence TL;DR of the final answer
- Print all 3 stages in a formatted, readable way with separators

---

## Completion Checklist

Check every item before calling this project done:

| Requirement | Done? |
|---|---|
| Cell 1 runs without errors, prints confirmation | [ ] |
| Cell 2 prints all 3 labelled sections | [ ] |
| FINAL answer is visibly better than INITIAL answer | [ ] |
| `reflection_agent()` function is defined and callable | [ ] |
| Calling it with DIFFERENT input text still works | [ ] |
| You can explain each stage without looking at the code | [ ] |

---

## Why This Matters for Your Final Project

> The think → reflect → refine loop you built **is the core of your Orchestrator Agent**.
>
> In Week 13, the orchestrator receives outputs from 3 mini-agents.
> It uses this exact pattern to decide what is urgent before surfacing it to you.
>
> You are not doing a practice exercise — you are building your product from Day 1.

---

*Stuck? → `project-0.1-hints.md` | Last resort → `project-0.1-answers.md`*
