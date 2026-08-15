# Python Best Practices for Skills

This guide applies to Python snippets embedded in skills under `skills/`, including scaffolding scripts (file renames, content substitutions, seed generation) and any inline `python3 - <<'PY' ... PY` heredocs invoked from a skill's instructions.

It does **not** apply to dbt SQL or Jinja — those have their own style guide. It also does not apply to ad-hoc one-liners typed directly into a terminal by an analyst.

The goal is predictable re-runs and snippets a reviewer can read in one pass. Skills run on developer machines (macOS) — there's no need to chase Linux portability, but we should still avoid shell utilities whose flags differ between BSD and GNU versions, because Stack Overflow answers and LLM suggestions often assume the GNU form and silently fail on macOS.

## Core principles

### 1. Use `pathlib.Path` for all filesystem work

Prefer `pathlib.Path` over `os.path`.

**Why this matters:** `os.path` requires manual string joins and explicit file handle management. `pathlib` objects compose cleanly with `/`, expose `.exists()` / `.is_file()` / `.rename()` as methods, and `.read_text()` / `.write_text()` handle the file handle for you — less boilerplate, fewer unclosed handles, and no malformed path strings from forgotten separators.

```python
# Preferred
from pathlib import Path

root = Path("models/staging") / market_name
for path in root.rglob("*.sql"):
    text = path.read_text(encoding="utf-8")
    ...
```

```python
# Not preferred
import os
root = os.path.join("models/staging", market_name)
for dirpath, _, filenames in os.walk(root):
    for fn in filenames:
        path = os.path.join(dirpath, fn)
        with open(path, "r", encoding="utf-8") as f:
            text = f.read()
```

### 2. Always pass `encoding="utf-8"` for text reads/writes

**Why this matters:** Python's default encoding is `locale.getpreferredencoding()`, which varies by machine configuration. On most macOS installs this is UTF-8, but Snowflake-exported CSVs or files touched by legacy tools can carry unexpected encodings. Being explicit makes the assumption auditable and turns a potential silent corruption into a loud failure.

```python
# Preferred
text = path.read_text(encoding="utf-8")
path.write_text(updated_text, encoding="utf-8")

# Not preferred
text = path.read_text()
path.write_text(updated_text)
```

### 3. Look Before You Leap (LBYL) for expected filesystem conditions

Check conditions proactively instead of relying on `try`/`except` to discover them. Exceptions should signal genuinely unexpected problems, not be used as branching logic.

**Why this matters:** Using `try/except` as an `if` statement hides the conditions under which the code actually runs. If a file truly shouldn't be missing, swallowing a `FileNotFoundError` makes the problem invisible. Pre-checks raise clearly with context and keep the happy path readable from top to bottom.

Check before reading, renaming, or writing:

```python
# Preferred — explicit checks
if source_path.exists() and source_path.is_file():
    original_text = source_path.read_text(encoding="utf-8")

if target_path.exists():
    raise RuntimeError(f"refusing to overwrite existing file: {target_path}")
source_path.rename(target_path)
```

```python
# Not preferred — exception as control flow
try:
    original_text = source_path.read_text(encoding="utf-8")
except FileNotFoundError:
    pass
```

When filtering files by content type, prefer **suffix allowlists** over probing for `UnicodeDecodeError`. A decode failure tells you nothing useful that the filename didn't already tell you, and silently swallowing it can hide a real bug in an unrelated file:

```python
# Preferred — explicit allowlist of text suffixes
TEXT_SUFFIXES = {".sql", ".yml", ".yaml", ".md", ".csv"}

for path in root.rglob("*"):
    if path.is_file() and path.suffix in TEXT_SUFFIXES:
        text = path.read_text(encoding="utf-8")
        ...
```

```python
# Not preferred — using a decode failure to detect binary files
for path in root.rglob("*"):
    try:
        text = path.read_text(encoding="utf-8")
    except UnicodeDecodeError:
        continue  # masks the real question: which files did we intend to touch?
```

### 4. Keep imports at module level

Put all imports at the top of the script or heredoc, even for short snippets.

**Why this matters:** A reader skimming a 30-line heredoc shouldn't have to hunt for imports scattered inside functions. Module-level imports declare the full dependency surface up front, so the snippet's requirements are visible in one place before any logic runs.

```python
# Preferred
import re
from pathlib import Path

def rewrite(path: Path) -> None:
    text = path.read_text(encoding="utf-8")
    ...
```

```python
# Not preferred
def rewrite(path):
    import re                # hides the dependency
    from pathlib import Path
    ...
```

### 5. Use clear, descriptive variable names

Name variables for what they hold, not for their position in a diff or loop.

**Why this matters:** Scaffolding scripts perform multiple file operations in a loop — a variable called `old` or `new` is ambiguous between a path, a filename, and a file's text content. Vague names are the leading cause of off-by-one substitutions (e.g., accidentally applying a rename to the source path instead of the target).

| Preferred | Avoid |
|---|---|
| `source_path`, `target_path` | `src`, `dst`, `p`, `path` (when there are multiple) |
| `original_text`, `updated_text` | `txt`, `old`, `new`, `s` |
| `iso_old`, `iso_new` | `a`, `b` |
| `text_suffixes` | `exts` |

### 6. Prefer Python over macOS-fragile shell utilities

**Why this matters:** Several shell utilities have a BSD form (macOS default) that differs from the GNU form documented in most online examples. Suggestions copied from Stack Overflow or generated by an LLM will often assume the GNU form and fail silently or produce wrong output on macOS. Python implementations sidestep this entirely. Common offenders:

- `sed -i 's/.../.../' file` — fails on macOS; BSD requires an empty string arg (`sed -i '' ...`). Use `path.write_text(...)` instead.
- `find -exec rename` — `rename` is not installed on macOS by default. Walk with `pathlib` and call `.rename()` in Python.
- `xargs` flags (`-r`, `-d`) are GNU-only. Prefer a Python loop.
- `realpath` is GNU-only on macOS unless coreutils is installed. Use `Path.resolve()`.

The add-market skill rewrites filenames and file contents entirely in Python for this reason.

### 7. Make snippets idempotent, or explicitly guard against re-running

Snippets should either be safe to run twice, or refuse to run twice with a clear error.

**Why this matters:** Skills are frequently interrupted mid-run (a failed `dbt parse`, a typo in a country code) and re-run from the top. A snippet that assumes it runs only once will either fail loudly on re-run (best case) or silently double-apply substitutions and corrupt file contents (worst case).

```python
# Preferred — idempotent: only writes when content actually changed
updated_text = original_text
for find, replace in substitutions:
    updated_text = updated_text.replace(find, replace)
if updated_text != original_text:
    path.write_text(updated_text, encoding="utf-8")
```

```python
# Preferred — guarded: refuses to clobber existing scaffolding
if target_dir.exists():
    raise RuntimeError(
        f"{target_dir} already exists — remove it or run a different step"
    )
target_dir.mkdir(parents=True)
```

### 8. Do not overwrite existing files silently in scaffolding scripts

Scaffolding scripts should treat existing target paths as an error condition by default.

**Why this matters:** A silent overwrite destroys any manual edits made after the first run. The user won't notice until they diff — if at all. A loud failure at the point of conflict is cheaper than silent data loss discovered later.

```python
# Preferred
for target_path in generated_files:
    if target_path.exists():
        raise RuntimeError(f"refusing to overwrite: {target_path}")
    target_path.write_text(content, encoding="utf-8")
```

If overwriting is genuinely desired (e.g. regenerating a header-only seed CSV), make it explicit with a top-of-snippet `OVERWRITE = True` flag so the intent is visible in the diff.

### 9. Prefer explicit file lists or extension allowlists when rewriting recursively

A find/replace across a market folder should declare **which** files it intends to touch.

**Why this matters:** Without an allowlist, the scope of a recursive rewrite is invisible at code review time. Seeing `TEXT_SUFFIXES = {".sql", ".yml", ...}` makes the intent auditable — a reviewer can immediately verify what file types will be modified without having to reason about what the entire directory tree contains.

```python
# Preferred
TEXT_SUFFIXES = {".sql", ".yml", ".yaml", ".md", ".csv"}

for path in root.rglob("*"):
    if not path.is_file() or path.suffix not in TEXT_SUFFIXES:
        continue
    original_text = path.read_text(encoding="utf-8")
    updated_text = apply_substitutions(original_text)
    if updated_text != original_text:
        path.write_text(updated_text, encoding="utf-8")
```

If the set of target files is small and known up front, list them explicitly rather than walking — an explicit list is easier to audit during code review.
