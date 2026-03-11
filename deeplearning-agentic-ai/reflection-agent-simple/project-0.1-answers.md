# Project 0.1 — Complete Answers

> STOP. Try the worksheet first. Use hints second.
> Opening this without attempting the work first will slow your learning significantly.
> The goal is not working code — it's understanding WHY each piece works.

---

## Environment Setup

```bash
# Install Ollama: https://ollama.ai/download

# Pull the model
ollama pull llama3.2

# Verify
ollama run llama3.2
# Test it, then exit: /bye

# Install Python dependencies
pip install langchain langchain-ollama notebook

# Launch Jupyter from project folder
cd C:\dev\agentic-ai-learning\deeplearning-agentic-ai
jupyter notebook
```

---

## Cell 1 — Complete Answer

```python
from langchain_ollama import OllamaLLM

# Connect to Ollama running locally
llm = OllamaLLM(model="llama3.2")

# Mock data simulating what the Day Brief Agent will eventually provide
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

**Why this works:**
- `OllamaLLM` wraps the locally-running Ollama service — no API key, no cost
- `mock_context` simulates real calendar + task API output
- The question at the end is the agent's "trigger"

---

## Cell 2 — Complete Answer

```python
# STAGE 1: THINK
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


# STAGE 2: REFLECT
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


# STAGE 3: REFINE
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

**Why this works:**
- Each stage feeds the previous stage's output into the next prompt
- Persona shift (helper → critic → helper) forces evaluation from a different angle
- Stage 3 explicitly cites BOTH original AND critique — this is what drives improvement
- This is the reflection pattern Andrew Ng describes — proven to improve LLM output quality

---

## Cell 3 — Complete Answer

```python
def reflection_agent(user_context: str) -> dict:
    """
    Reflection Loop Agent.
    Implements Think -> Reflect -> Refine.

    Args:
        user_context: String containing calendar, tasks, and a question.

    Returns:
        dict with keys: initial_answer, critique, final_answer, reasoning_trace
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


# Run the agent
result = reflection_agent(mock_context)

print("=" * 50)
print("AGENT RUN COMPLETE")
print("=" * 50)
print("\nFinal Answer:\n", result["final_answer"])
print("\nReasoning Trace:", result["reasoning_trace"])
```

**Why this works:**
- Uses `user_context` (parameter), not `mock_context` (global) — makes it reusable
- Return dict is the "agent output interface" — the Orchestrator will consume this shape
- `reasoning_trace` list is designed to become a full LangSmith trace in Week 9
- This exact `context_in → dict_out` function signature is how your mini-agents will be composed

---

## Optional Extension — Complete Answer

```python
def reflection_agent_v2(user_context: str) -> dict:
    """Enhanced version with TL;DR summary stage."""

    think = llm.invoke(
        f"You are a productivity assistant. What should the user focus on today?\n\n{user_context}"
    )
    reflect = llm.invoke(
        f"Critique this suggestion. What is good, missing, or illogical?\n\n{think}"
    )
    refine = llm.invoke(
        f"Improve this using the critique. Be concise.\n\nOriginal: {think}\n\nCritique: {reflect}"
    )
    summary = llm.invoke(
        f"Summarise this recommendation in ONE sentence (max 20 words):\n\n{refine}"
    )

    return {
        "initial_answer"  : think,
        "critique"        : reflect,
        "final_answer"    : refine,
        "summary"         : summary,
        "reasoning_trace" : ["think", "reflect", "refine", "summarise"]
    }

result_v2 = reflection_agent_v2(mock_context)
print("One-line Summary:", result_v2["summary"])
```

---

## Key Concepts

| Concept | What it means | Where it appears in your product |
|---|---|---|
| Reflection pattern | LLM critiques its own output to improve | Orchestrator Agent scoring logic |
| Prompt persona | Role given to LLM shapes its behaviour | Every agent in the system |
| f-string injection | Dynamic data inserted into a prompt | All prompt templates throughout |
| `reasoning_trace` | Log of every step the agent took | LangSmith observability (Week 9) |
| Function-as-agent | Callable function wrapping agent logic | How mini-agents are composed |

---

## Troubleshooting

**Final answer looks same as initial?**
1. Check Stage 3 — are both `initial_answer` AND `critique` referenced?
2. Try a stronger critique prompt: "Be harsh. What is fundamentally wrong with this recommendation?"
3. Try a different model: `OllamaLLM(model="llama3.1")` — some models reflect better

**LLM not connecting?**
1. Open a separate terminal and run: `ollama serve`
2. Then retry your notebook cell
3. Verify: `ollama list` shows `llama3.2` in the list

**Import error: `No module named 'langchain_ollama'`?**
1. Run: `pip install langchain-ollama`
2. Restart your Jupyter kernel after installing

---

*Done. Return to `project-0.1-worksheet.md` and check off all items.*
*Next: Project 0.2 — Multi-Agent Handoff (Week 1–2)*
