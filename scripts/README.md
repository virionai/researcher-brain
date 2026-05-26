# scripts/

Tools that operate on the research project's `raw/` and `wiki/`
folders. This file is created by `R8: build-wiki-tool` the first time
a tool is built; this version is the template starting point.

## Naming

The dispatcher at the root of this folder is named after the project
folder. If your project is in `MyResearchProject/`, the dispatcher
should be `scripts/myresearchproject` and the env var
`MYRESEARCHPROJECT_REPO`. Adjust the examples below accordingly.

## Invoking tools

Use the dispatcher at the root of this folder:

```
scripts/<project-name> list                          # show available tools
scripts/<project-name> <tool-name> [args]            # run a tool
scripts/<project-name> --help                        # usage
```

To make it global, alias or symlink it:

```bash
# Alias (per-shell)
alias <project-name>='/absolute/path/to/scripts/<project-name>'

# Symlink (system-wide)
ln -s "$(pwd)/scripts/<project-name>" /usr/local/bin/<project-name>
```

After that, routines can call tools as `<project-name> <tool-name>`
from any directory.

## Adding a tool

Per `program.md` R8 (`build-wiki-tool`), each tool lives in its own
folder:

```
scripts/<tool-name>/
├── SPEC.md              ← input / output / side effects / non-goals
├── <tool-name>.py       ← entry point (Python preferred, .sh allowed)
└── MANIFEST.md          ← (optional) tool-owned tracking file
```

Conventions:

- **Entry-point filename matches the tool name.** The dispatcher looks
  for `<tool-name>.py` first, then `<tool-name>.sh`. Don't call the
  entry point `main.py` — it makes the dispatcher's life harder and
  the catalog's life worse.
- **Repo root is passed via env var.** Tools read
  `os.environ["<PROJECT>_REPO"]` rather than hard-coding paths so
  they work regardless of the caller's cwd.
- **Dry-run is the default.** Tools must never modify `raw/` or
  `wiki/` on first invocation. A `--apply` flag enables write-mode.
  The operator approves the first apply.
- **Stdlib + ripgrep only** unless an external dep is justified in
  `SPEC.md`. Tools that need exotic dependencies bit-rot fastest; the
  ones that survive use what's already on every machine.
- **Document the tool in `program.md` R8 *Tool catalog*** so other
  routines can find it. The catalog is the contract; the script is
  invocable independent of it.

## Current catalog

See `program.md` R8 *Tool catalog* for status and callers. (Empty
until R8 produces its first tool.)
