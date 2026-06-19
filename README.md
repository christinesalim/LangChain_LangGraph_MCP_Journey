# Learning LangChain, LangGraph, and MCP

A learning-in-public log of my journey through Roberto Infante's [*AI Agents and Applications: With LangChain, LangGraph, and MCP*](https://www.manning.com/books/ai-agents-and-applications) (Manning, 2026).

I'm working through the book chapter by chapter, writing up what I learn in my own words and building small experiments along the way. This repo is my notes, my exploratory notebooks, and any side projects that grow out of it.

## What's in here

- **`notes/`** — One markdown file per chapter. Concepts, gotchas, glossary, "when would I use this" notes.
- **`notebooks/`** — My own exploratory Jupyter notebooks. Not copies of the book's source — original experiments applying chapter ideas to different examples or my own data.
- **`projects/`** — Slightly bigger side projects that combine multiple chapters' ideas.

## Progress

| Chapter | Topic | Notes | Notebook | Post |
| --- | --- | --- | --- | --- |
| 1 | LangChain basics (PromptTemplate, chains) | [notes](notes/ch01-langchain-basics.md) | [notebook](notebooks/ch01-prompttemplate-exploration.ipynb) | 
| 2 | Prompt engineering | — | — | — |
| 3 | Summarization | — | — | — |
| 4 | Research engine (agents) | — | — | — |
| 5 | Agent frameworks | — | — | — |
| 6 | Vector stores (Chroma) | — | — | — |
| 7 | QA across documents | — | — | — |
| 8 | Advanced indexing | — | — | — |
| 9 | Query transformations | — | — | — |
| 10 | SQL query generation | — | — | — |
| 11 | LangGraph + MCP | — | — | — |
| Appendix E | Local LLMs | — | — | — |

## Book source code

The official source code for the book lives at https://github.com/roberto-inf/building-llm-applications. This repo is **not** a fork of it — it's my own learning artifacts. I'll quote small code snippets here for discussion (with attribution), but not republish chapters wholesale.

## Setup

```bash
python3.12 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Set your `OPENAI_API_KEY` in a `.env` file at the repo root (gitignored) — see [`.env.example`](.env.example).

## Why I'm doing this publicly

Writing things down forces me to actually understand them, and posting in public forces me to write things down. If any of it helps someone else along the way, even better.
