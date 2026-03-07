# STEP 1: Initial Answer (Think)
initial_prompt = f"""
You are a personal productivity assistant.
Given this context, suggest what the user should focus on today.
Be concise and practical.

Context:
{mock_context}
"""
initial_answer = llm.invoke(initial_prompt)
print("=== INITIAL ANSWER ===")
print(initial_answer)

# STEP 2: Self-Critique (Reflect)
reflection_prompt = f"""
You are a critical reviewer. Examine this productivity suggestion and identify:
1. What's good about it?
2. What's missing or could be improved?
3. Is the prioritisation logical?

Original suggestion:
{initial_answer}
"""
critique = llm.invoke(reflection_prompt)
print("\n=== CRITIQUE ===")
print(critique)

# STEP 3: Improved Answer (Refine)
refinement_prompt = f"""
You are a personal productivity assistant.
You previously gave this answer:
{initial_answer}

A reviewer gave this critique:
{critique}

Now produce an improved, final recommendation. Be concise.
"""
final_answer = llm.invoke(refinement_prompt)
print("\n=== FINAL ANSWER ===")
print(final_answer)