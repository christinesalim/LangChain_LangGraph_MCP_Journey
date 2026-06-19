# LangChain invocation patterns

There are three ways to run a templated prompt through an LLM in LangChain. They're not alternatives — they're **layers**, each adding capability on top of the previous one.

---

## The three patterns

```python
# Pattern 1 — Format-then-invoke (lowest level)
rendered = prompt.format(**inputs)        # → str
result = llm.invoke(rendered)              # → AIMessage

# Pattern 2 — Runnable-style, decoupled (middle layer)
prompt_value = prompt.invoke(inputs)       # → PromptValue
result = llm.invoke(prompt_value)           # → AIMessage

# Pattern 3 — Piped, full LCEL (top layer)
chain = prompt | llm
result = chain.invoke(inputs)              # → AIMessage
```

All three produce the same final answer. The difference is the **intermediate type** and the **capabilities** you gain.

---

## What each gives you

| Pattern | Intermediate | What it's good for |
|---|---|---|
| `prompt.format()` | `str` | The debugger's friend. Print it, paste it into the OpenAI playground, log it to a file. |
| `prompt.invoke()` | `PromptValue` | Honest intermediate. Carries metadata: `.to_string()` for completion models, `.to_messages()` for chat models. Less common in practice. |
| `prompt \| llm` | `RunnableSequence` | The framework's currency. You get `.batch()`, `.stream()`, async, configs, callbacks, fallbacks — all for free. |

---

## The unifying idea: the Runnable protocol

In LangChain 1.0, **almost everything is a Runnable** — `PromptTemplate`, `ChatOpenAI`, output parsers, retrievers, even simple Python functions wrapped with `RunnableLambda`. Every Runnable implements the same five methods:

- `.invoke(input)` — single call
- `.batch([inputs])` — multiple inputs, parallelized
- `.stream(input)` — yields incremental output
- `.ainvoke(input)` — async single call
- `.abatch(...)` / `.astream(...)` — async versions

That's why the `|` operator works at all. It's not magic — `prompt | llm` returns a `RunnableSequence` whose `.invoke()` internally does:

```python
def invoke(self, inputs):
    prompt_value = self.prompt.invoke(inputs)   # Pattern 2 happens behind the curtain
    return self.llm.invoke(prompt_value)
```

So **Pattern 3 is just Pattern 2, automated**. Pattern 1 is the raw-string escape hatch underneath both.

---

## When to use which

- **Need to *see* the prompt before sending?** → `prompt.format(**inputs)` and `print()`. This is debug-time use.
- **Building anything you'll run more than once?** → `prompt | llm`. Once you opt into the Runnable protocol, you keep batch/stream/async/observability for the rest of the chain.
- **Need a typed intermediate but not a full pipe?** → `prompt.invoke(inputs)`. Rare. Mostly useful when you want to switch between `.to_messages()` and `.to_string()` representations of the same prompt.

---

## The pattern I actually use most

In real notebooks and production code, you want **both at once**: pipe form for execution, format form for inspection.

```python
chain = prompt | llm

# Inspect what's being sent (debug)
print(prompt.format(**inputs))

# Run it
result = chain.invoke(inputs)
```

Pipe form for the *how*. Format call for the *what was sent*. They're complementary, not alternatives.

---

## Signature gotcha

The two APIs take their inputs in **different shapes**:

| Call | Takes |
|---|---|
| `prompt.format(**inputs)` | keyword arguments (a dict, unpacked with `**`) |
| `prompt.invoke(inputs)` | a single dict argument |
| `chain.invoke(inputs)` | a single dict argument |

The Runnable API standardizes on "one dict in, one thing out" so the same shape flows through every step of a chain. The lower-level `format` API uses Python's normal keyword-argument convention.

If you're switching between them, this is the most common slip:

```python
# Wrong
chain.invoke(**inputs)        # TypeError — invoke() doesn't take kwargs

# Right
chain.invoke(inputs)          # pass the dict directly
```
