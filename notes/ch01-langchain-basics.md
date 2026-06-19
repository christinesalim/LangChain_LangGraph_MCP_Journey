# Chapter 1 — LangChain basics

## What this chapter teaches

How to talk to an LLM through LangChain instead of calling the OpenAI SDK directly: the `ChatOpenAI` model wrapper, the `PromptTemplate` for reusable parameterized prompts, and how to compose them into a `chain` with the `|` operator.

---

## Concepts

### 1. The LLM wrapper — `ChatOpenAI`

```python
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o-mini")
response = llm.invoke("It's a hot day, I would like to go to the...")
print(response.content)
```

- `llm` is a callable object. `.invoke(text)` sends the text to the model and returns a response object.
- Access the model's reply via `response.content`.
- LangChain abstracts the provider — swap `ChatOpenAI` for `ChatAnthropic`, `ChatGoogleGenerativeAI`, etc., and the rest of your code is unchanged.

**When to reach for it:** any time you'd otherwise call OpenAI's Python SDK directly. The wrapper gives you a stable interface as you grow into chains and agents.

---

### 2. `PromptTemplate` — parameterized prompts

A `PromptTemplate` is a string with `{placeholders}` that get filled in at runtime.

```python
from langchain_core.prompts import PromptTemplate

prompt_template = PromptTemplate.from_template(
    "You are an experienced copywriter. "
    "Write a {num_words} words summary of the following text, "
    "using a {tone} tone: {text}"
)
```

- Defined once, reused with different inputs.
- Placeholders use `{name}` syntax. They become **input variables**.
- Keeps prompts out of f-strings scattered across your code → one place to tune wording.

**When to reach for it:** the moment you're building a prompt from variable input (user query, document content, parameters). Don't hand-concatenate strings — use a template.

---

### 3. Chains — composition with `|`

```python
chain = prompt_template | llm
response = chain.invoke({
    "text": segovia_aqueduct_text,
    "num_words": 20,
    "tone": "knowledgeable and engaging",
})
print(response.content)
```

- The `|` operator (LangChain Expression Language, **LCEL**) pipes one step's output into the next.
- Reads left-to-right: take the template → fill it → send to the LLM.
- `chain.invoke(dict)` passes the dict to fill the template's placeholders.

**Mental model:** a chain is just a function composed of smaller steps. `prompt | llm` is the smallest useful chain. Later chapters add retrievers, parsers, tools, etc., chained the same way.

**When to reach for it:** any time you have more than one step (template → LLM → parse output → ...). Chains make the steps inspectable and swappable.

---

## Minimal pattern to remember

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import PromptTemplate

llm = ChatOpenAI(model="gpt-4o-mini")
prompt = PromptTemplate.from_template("Summarize in {n} words: {text}")
chain = prompt | llm

result = chain.invoke({"n": 20, "text": "..."})
print(result.content)
```

That's the whole chapter in 6 lines. Everything later is variations on this shape.

---

## Gotchas I hit

- **`getpass.getpass(prompt)`** — the argument is a *label*, not the value. The function pops up a hidden input field; you type the key into it. Pasting the key as the argument leaks it into the notebook source.
- **Cell order matters** in notebooks. Variables persist across cells, but only if the defining cell has actually finished running (look for the `[N]` number, not `[*]` or `[ ]`).
- After a kernel restart, **nothing** is remembered — re-run all cells from the top.

---

## Try-this exercises

1. Swap `ChatOpenAI` for a different model (`gpt-4o-mini` → `gpt-5-nano` → `gpt-4o`) and compare outputs on the same prompt.
2. Add a new placeholder like `{audience}` ("a child", "a Roman historian") and see how tone affects the summary.
3. Build a chain that first translates a paragraph to French, then summarizes it. Hint: chain two `prompt | llm` pairs.

---

## Glossary

| Term | Meaning |
| --- | --- |
| **LLM wrapper** | LangChain class that provides a uniform `.invoke()` interface to a specific provider's model |
| **PromptTemplate** | A string with `{placeholders}` plus a list of input variable names |
| **Chain** | A pipeline of steps composed with `|`; the output of each step feeds the next |
| **LCEL** | LangChain Expression Language — the `|` syntax for declaring chains |
| **`.invoke(input)`** | Standard method on every LangChain runnable; runs the chain/model once on `input` and returns the result |
