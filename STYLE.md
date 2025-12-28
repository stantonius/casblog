# STYLE.md

My coding style

## Philosophy

Human-in-the-loop workflow. I must understand every change before proceeding. Prioritize my comprehension over speed—I'm trying to learn, not just ship.

## Development: nbdev

- Notebooks are source of truth — never edit generated `.py` files
- One function/class per cell, followed by inline tests in the next cell
- Markdown cells explain *what* and *why* before code
- Include background, research notes, and context in notebook markdown cells
- No pytest/unittest — nbdev testing only

### Imports

All imports at the **top of the notebook**, not at the top of individual cells. Don't mix imports with code definitions.

```python
# WRONG - import in same cell as definition
from fastcore.basics import patch

@patch
def method(self: MyClass): ...

# RIGHT - imports at top of notebook, then use throughout
# (Cell 1 has all imports, Cell N uses them)
@patch
def method(self: MyClass): ...
```

### Class Development with `@patch`

Use fastcore's `@patch` to add methods to classes incrementally instead of writing massive classes in one cell. This enables inline testing after each method:

```python
# Cell 1: Define minimal class
class MyClass:
    def __init__(self, x): self.x = x

# Cell 2: Test it
obj = MyClass(5)
assert obj.x == 5

# Cell 3: Add method via @patch
from fastcore.basics import patch

@patch
def double(self: MyClass): return self.x * 2

# Cell 4: Test using SAME instance
assert obj.double() == 10  # reuse obj, don't create new instance
```

### Test Instance Reuse

**Important**: When testing patched methods or incremental changes, reuse existing test instances defined earlier in the notebook. Don't create new instances unless specifically testing instantiation.

```python
# WRONG - creates redundant instance
@patch
def new_method(self: MyClass): ...

test_obj = MyClass(5)  # unnecessary, we already have obj
assert test_obj.new_method() == ...

# RIGHT - reuse existing instance  
@patch
def new_method(self: MyClass): ...

assert obj.new_method() == ...  # use obj from earlier
```

### nbdev Commands

Run before every commit:
```bash
nbdev_prepare  # clean, export, and test in one command
```

Pre-commit hooks handle notebook cleanup automatically (stripping outputs, metadata). Don't manually clean notebooks—just commit and let the hooks run.

Key commands:
- `nbdev_export` — generates `.py` modules from notebooks. Run after any code changes to keep modules in sync.
- `nbdev_test` — runs all test cells. Run frequently during development.

### Cell Directives

- `#| export` — include in generated module
- `#| notest` — skip during `nbdev_test` (use for slow setup, interactive demos, or notebook-specific exploration)
- `#| hide` — exclude from docs

Use `#| notest` for:
- Expensive setup that shouldn't run on every test pass
- Interactive/exploratory cells not meant as tests
- Cells that only make sense when running the notebook manually

**Always run `nbdev_test` after changes** to ensure all tests pass. Don't assume passing cells in the notebook means `nbdev_test` will pass—they can differ.

## Commits

- **Max ~100 lines or 3 files per commit** — if larger, break it up
- One logical change per commit that I can explain in one sentence
- Format: `<type>: <one-sentence description>`
- Types: `feat`, `fix`, `refactor`, `docs`, `chore`, `test`

## Branches

- Confirm current branch before making changes
- Feature work on feature branches only
- No commits to `main` without explicit instruction
- Ask before creating new notebooks or switching branches
- Chore/cleanup work gets its own branch

### Scope Creep Prevention

This is critical — I have a tendency to go rogue and cross-contaminate branches.

- **Stay on task**: If work starts drifting from the branch's purpose, stop and flag it
- **One feature per branch**: If I ask for something unrelated to current branch, ask: *"This seems like a separate concern — should we do this on a different branch?"*
- **No drive-by fixes**: Resist fixing unrelated things noticed along the way; note them for later instead
- **Check before large changes**: If a task is growing beyond the original scope, pause and confirm: *"This is getting larger than expected (~X lines across Y files). Should we break this up?"*

## Checkpoints

Before proceeding to next task, require my confirmation that I:
1. Read the code
2. Ran it and saw output  
3. Can explain what it does

**Enforce this.** If I approve without demonstrating understanding (e.g., just "looks good" or "continue"), prompt: *"Can you briefly explain this change before we continue?"*

Don't let me rubber-stamp.

## Knowledge Transfer

Help me stay in the loop:

- **Show code before committing** — don't just describe what you'll do
- **Explain the "why"** — not just the "what"
- **Point out patterns** — help me recognize idioms I can reuse
- **Flag concepts I should understand** — if you use something non-obvious, briefly explain it or ask if I want elaboration
- **Summarize at natural breakpoints** — after completing a feature or before switching tasks, recap what was done

## Refactoring

- Suggest new notebooks when one exceeds ~500 lines or a distinct module emerges
- Ask me before refactoring — I want to understand the reasoning
- When refactoring, explain the before/after structure so I can follow the change

## FastHTML / Hypermedia

When building with FastHTML, follow hypermedia-driven design:

- **Small, single-purpose endpoints** — each route does one thing and returns HTML fragments
- **HTMX over JavaScript** — use `hx-get`, `hx-post`, `hx-swap`, etc. for interactivity
- **Tailwind + HTML first** — solve styling/layout with Tailwind classes and semantic HTML
- **JS only as last resort** — only use JavaScript when something is genuinely impossible with HTMX/Tailwind (rare)

```python
# GOOD - small endpoint returning HTML fragment
@rt('/todo/{id}/toggle')
def post(id: int):
    todo = toggle_todo(id)
    return todo_item(todo)  # returns just the <li> fragment

# AVOID - large endpoints, JSON APIs, client-side rendering
```

Keep components small. If a component is getting complex, break it into smaller partials that can be independently swapped.

## Don't

- Edit `.py` files directly
- Make large commits (>100 lines) without breaking them up
- Skip inline tests
- Proceed without my comprehension confirmation
- Combine multiple features on one branch
- Fix unrelated issues without flagging them first
- Let scope creep go unchecked