---
name: gum-tui-clis
description: Create small CLIs and scripts that use Charmbracelet Gum TUI components (choose, input, confirm, spin, style) with solid shell practices and non-interactive fallbacks.
license: Apache-2.0
---

This skill guides building small, pragmatic CLIs and scripts using the `gum` command (https://github.com/charmbracelet/gum) for TUI prompts and polished terminal output.

## When To Use
Use this skill when the user wants:
1. A small CLI or script (usually Bash) with interactive prompts.
2. A TUI flow for choices, confirmations, forms, progress spinners, or styled output.
3. A fast "ops" tool that’s nicer than raw `read`/`select`.

Do not force TUI if:
1. The command must run in CI/non-interactive mode by default.
2. Output must be machine-parseable (prefer `--json` / plain output).
3. The environment likely lacks a TTY.

## Defaults
1. Shell: `bash` with `set -euo pipefail`.
2. Provide `--help`, `--version`, and `--no-interactive` when relevant.
3. Respect `NO_COLOR=1` (and/or `GUM_NO_COLOR=1` if the project uses it).
4. Allow non-interactive input via flags or environment variables.
5. Fail fast with clear errors if `gum` is missing, plus install hints.

## Gum Components To Prefer
1. Selection: `gum choose`, `gum filter`
2. Input: `gum input`, `gum write` (multiline)
3. Confirmation: `gum confirm`
4. Feedback: `gum spin`, `gum log`
5. Presentation: `gum style`, `gum table`, `gum pager`, `gum format`, `gum join`

## Interaction Rules
1. Never prompt if stdin is not a TTY unless the user explicitly wants that behavior.
2. If a value is provided by flag/env, do not prompt for it.
3. Provide safe defaults; show the default in the prompt.
4. Make cancel behavior explicit. If the user cancels, exit with code `130`.

## Script Skeleton (Copyable)
Use this as the default structure unless the repo already has conventions:

```bash
#!/usr/bin/env bash
set -euo pipefail

VERSION="0.1.0"

usage() {
  cat <<'EOF'
Usage:
  mytool [options]

Options:
  --name <name>         Name to use
  --no-interactive      Never prompt; fail if required inputs are missing
  -h, --help            Show help
  -v, --version         Show version
EOF
}

die() { printf "error: %s\n" "$*" >&2; exit 1; }

have_tty() { [[ -t 0 && -t 1 ]]; }

need_cmd() {
  command -v "$1" >/dev/null 2>&1 || die "missing dependency: $1"
}

NO_INTERACTIVE=0
NAME="${NAME:-}"

while [[ $# -gt 0 ]]; do
  case "$1" in
    --name) NAME="${2:-}"; shift 2 ;;
    --no-interactive) NO_INTERACTIVE=1; shift ;;
    -h|--help) usage; exit 0 ;;
    -v|--version) printf "%s\n" "$VERSION"; exit 0 ;;
    *) die "unknown argument: $1 (use --help)" ;;
  esac
done

if [[ "$NO_INTERACTIVE" -eq 0 ]] && have_tty; then
  need_cmd gum
fi

if [[ -z "$NAME" ]]; then
  if [[ "$NO_INTERACTIVE" -eq 1 || ! have_tty ]]; then
    die "missing --name (or NAME env) in non-interactive mode"
  fi
  NAME="$(gum input --prompt "Name: " --placeholder "Jane Doe")" || exit 130
fi

if [[ "$NO_INTERACTIVE" -eq 0 ]] && have_tty; then
  gum confirm "Proceed with name '$NAME'?" || exit 130
fi

run() {
  printf "Hello, %s\n" "$NAME"
}

if [[ "$NO_INTERACTIVE" -eq 0 ]] && have_tty; then
  gum spin --title "Working..." -- run
else
  run
fi
```

## Patterns To Apply
1. Choose menus: use `gum choose` for small lists; `gum filter` for long lists.
2. Multi-step flows: keep data in variables; validate after each step.
3. Progress: wrap long-running commands with `gum spin -- <cmd>`.
4. Styled summaries: use `gum style` to render a final "review" screen before executing destructive actions.
5. Exit codes: `0` success, `1` errors, `2` usage, `130` user cancel.

## Safety And UX
1. Destructive operations require explicit confirmation (`gum confirm`).
2. Print what will happen before doing it.
3. Avoid hiding stderr; show errors plainly.
4. Support `--dry-run` when actions change state.

## What To Produce For The User
1. A single script file (or minimal small set) that runs end-to-end.
2. Clear usage text and examples.
3. A short "Dependencies" note: `gum` required for interactive mode.
4. A non-interactive path suitable for CI.

## Quick Reference Commands
1. `gum input --prompt "X: " --value "$X"`
2. `gum choose "a" "b" "c"`
3. `gum filter --placeholder "Search..." < <(printf "%s\n" "${items[@]}")`
4. `gum confirm "Are you sure?"`
5. `gum spin --title "Doing thing..." -- <cmd>`
6. `gum style --border rounded --padding "1 2" "Title" "Body"`
7. `gum table < file.tsv`
8. `gum pager < file.txt`
