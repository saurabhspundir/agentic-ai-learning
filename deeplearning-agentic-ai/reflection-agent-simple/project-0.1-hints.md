# Project 0.1 — Hints Guide

> Progressive hints — read Hint 1, try again, then Hint 2 if still stuck.
> Only open `project-0.1-answers.md` after all hints are exhausted.

---

## Section 0: Environment Setup

### `ollama` command not found
- **Hint 1:** Visit https://ollama.ai and download the installer for your OS.
- **Hint 2 (Windows):** After installing, restart your terminal. Ollama may not be on PATH until you do.
- **Hint 3:** Confirm with `ollama --version`.

### `ollama run llama3.2` says model not found
- **Hint 1:** You need to pull the model before running it.
- **Hint 2:** Pattern: `ollama pull <model-name>`
- **Hint 3:** Full command: `ollama pull llama3.2`

### `langchain_ollama` import error
- **Hint 1:** Run: `pip install langchain langchain-ollama`
- **Hint 2:** Make sure your virtual environment is activated first if you're using one.
- **Hint 3:** Verify installation: `pip show langchain-ollama`

### Jupyter won't launch
- **Hint 1:** Install with: `pip install notebook`
- **Hint 2:** Run from your project folder: `cd C:\dev\agentic-ai-learning\deeplearning-agentic-ai` then `jupyter notebook`

---

## Section 1: Cell 1

### What class do I import from `langchain_ollama`?
- **Hint 1:** You need a class that wraps a locally-running LLM (not a chat model).
- **Hint 2:** LangChain LLM wrapper classes usually end in `LLM`. Think: `Ollama___`.
- **Hint 3:** The class is `OllamaLLM`. Import line: `from langchain_ollama import OllamaLLM`

> Which unit covers this?
> Andrew Ng YouTube — he sets up a local LLM connection in the first 10 minutes.

### How do I instantiate the LLM?
- **Hint 1:** All LangChain LLM wrappers accept a `model` parameter.
- **Hint 2:** Single-line constructor: `llm = OllamaLLM(model="llama3.2")`
- **Hint 3:** If connection fails: run `ollama serve` in a separate terminal window.

### What should mock_context contain?
- **Hint 1:** Think about what the Day Brief Agent will eventually receive: calendar + tasks.
- **Hint 2:** Use triple quotes for a multi-line string: `mock_context = """..."""`
- **Hint 3:** Include specific times, event names, task priorities, and end with a question.

---

## Section 2: Cell 2

### How do I call the LLM with a prompt?
- **Hint 1:** LangChain LLM objects have a single method for getting a response.
- **Hint 2:** The method name is `.invoke()`. Pattern: `result = llm.invoke("your prompt")`
- **Hint 3:** Always store the return value — you need it in the next stage.

> Which unit covers this?
> **Coursera Agentic AI Primer — Module 1:** The perceive→act→reflect loop conceptually.
> **Andrew Ng YouTube (~20 min mark):** Shows this exact call pattern in Python.

### My prompt doesn't inject mock_context correctly
- **Hint 1:** You need an f-string to inject variables. The string must start with `f"""`
- **Hint 2:** Variables are injected with curly braces: `{variable_name}`
- **Hint 3:** Full pattern: `prompt = f"Your instructions here\n\n{mock_context}"`

### What persona should Stage 1 (THINK) use?
- **Hint 1:** Give it a helpful role matching the task.
- **Hint 2:** Example: "You are a personal productivity assistant..."
- **Hint 3:** Keep it one sentence. Goal is a concise prioritised list.

### What should the Stage 2 (REFLECT) prompt look like?
- **Hint 1:** Use a DIFFERENT persona — a critic, not a helper.
- **Hint 2:** The input to Stage 2 is `initial_answer`, not `mock_context`.
- **Hint 3:** Ask specific questions: "What's good? What's missing? Is prioritisation logical?"

> Which unit covers this?
> **Andrew Ng YouTube (~30 min mark):** He explains the reflection pattern directly and why
> self-critique improves output quality. Watch this before writing Stage 2.

### My FINAL answer doesn't look better than INITIAL
- **Hint 1:** Check Stage 3 — does it reference BOTH `initial_answer` AND `critique`?
- **Hint 2:** If only one is referenced, the model has nothing to improve against.
- **Hint 3:** Make it explicit: "Use the critique below to improve the original answer above."

---

## Section 3: Cell 3

### How do I define a function returning a dict?
- **Hint 1:** Python dict return: `return {"key1": value1, "key2": value2}`
- **Hint 2:** Function signature: `def reflection_agent(user_context: str) -> dict:`
- **Hint 3:** Type hints are optional but make your code self-documenting — keep them.

### What's the difference between `user_context` and `mock_context`?
- **Hint 1:** `mock_context` is a global variable with one specific fake scenario.
- **Hint 2:** `user_context` is the function parameter — it accepts ANY string.
- **Hint 3:** Inside the function body, always use `user_context`. Never `mock_context`.

### Why is `reasoning_trace` a list of strings?
- **Hint 1:** Right now it's just labels: `["think", "reflect", "refine"]`
- **Hint 2:** In Week 9, this becomes a structured LangSmith trace with timestamps and token counts.
- **Hint 3:** Think of it as a breadcrumb trail of every step the agent took.

### How do I call my function and access the result?
- **Hint 1:** Call like any Python function: `result = reflection_agent(mock_context)`
- **Hint 2:** The return value is a dict. Access with: `result["final_answer"]`
- **Hint 3:** Full print: `print("Final Answer:", result["final_answer"])`

---

## Course Reference Map

| Problem Area | Where to Look |
|---|---|
| Ollama setup / LLM connection | Andrew Ng DeepLearning.AI — Welcome lesson |
| Don't understand the reflect pattern | Andrew Ng DeepLearning.AI — Module 1 (Reflection) |
| Agent loop concept (perceive/plan/act) | Coursera Vanderbilt — Module 1 |
| Multi-agent design (Week 2 prep) | DeepLearning.AI AutoGen course |
| LangChain `.invoke()` API docs | https://python.langchain.com/docs |

**Andrew Ng course (your primary reference):**
https://learn.deeplearning.ai/courses/agentic-ai
Free to audit. Watch Welcome + Module 1 before Cell 2.

**Backup YouTube video (short, 8 min overview only):**
https://www.youtube.com/watch?v=sal78ACtGTc
Title: "What's next for AI agentic workflows" — concept overview, no code.

---

*All hints exhausted? → Open `project-0.1-answers.md`*
