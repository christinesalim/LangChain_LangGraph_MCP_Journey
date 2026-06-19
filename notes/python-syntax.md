# Python syntax notes

A growing reference for Python language details that come up while reading LangChain code. Notes here are language-level, not framework-level.

---

## Argument unpacking — `*` and `**`

### `**` unpacks a dict into keyword arguments

These two lines are identical:

```python
inputs = {"situation": "Tied 5-5", "n_words": 20}

prompt.format(**inputs)
prompt.format(situation="Tied 5-5", n_words=20)
```

The `**` "spreads" the dict's keys and values into named arguments. It's pure syntactic sugar — the function receives them exactly as if they'd been typed out.

**Why use it:** without `**`, every call would have to spell out the arguments explicitly:

```python
inputs = {"situation": "...", "tone": "playful", "n_words": 20}

# Without **
prompt.format(
    situation=inputs["situation"],
    tone=inputs["tone"],
    n_words=inputs["n_words"],
)

# With **
prompt.format(**inputs)
```

The unpacked form is shorter and survives any new keys being added to the dict.

### `*` unpacks a list/tuple into positional arguments

The single-star sibling:

```python
args = ["hello", 42]
some_function(*args)            # same as some_function("hello", 42)
```

### Quick table

| Operator | Unpacks | Into |
|---|---|---|
| `*args` | list / tuple | positional arguments |
| `**kwargs` | dict | keyword arguments |

### The other side: packing in function definitions

Inside a function *definition*, `*` and `**` do the **opposite** — they **collect** extra arguments instead of spreading them:

```python
def f(*args, **kwargs):
    print(args)      # tuple of positional args
    print(kwargs)    # dict of keyword args

f(1, 2, a=3, b=4)
# args = (1, 2)
# kwargs = {"a": 3, "b": 4}
```

So `*` and `**` **pack** when defining a function and **unpack** when calling one. The asymmetry catches everyone once — after that it clicks.

### Where you'll see `**` in this repo

- `PromptTemplate.format(**inputs)` — spread a dict of variables into the template's named slots
- `chain.invoke(inputs)` — note: LCEL `Runnable.invoke()` takes a *single dict*, not unpacked. LangChain's pipe-style API deliberately uses one positional dict so the same shape flows through every step of a chain.

That's actually a useful contrast to remember:
- **Lower-level template API** (`format`) uses `**dict` → keyword arguments.
- **Runnable API** (`invoke`) uses a plain `dict` → single positional argument.

Same data, two conventions, chosen for different reasons.
