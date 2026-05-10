# AGENTS.md

## Cursor Cloud specific instructions

### Project Overview

This is a **Pine Script V5** project for TradingView. There are **no local dependencies**, no package managers, no build systems, and no test frameworks. All `.pine` files execute exclusively on TradingView's cloud runtime.

### Structure

- `TradingViewer/*.pine` — Pine Script source files (indicator + strategy scripts)
- `doc/estrategia_btc.md` — Strategy documentation (source of truth)
- `.agent/rules.md` — Project-level agent rules (Engram MCP, conventions, signal parameters)

### Development Workflow

- **No `npm install`, `pip install`, or build commands exist.** The update script is a no-op.
- **Editing:** Edit `.pine` files directly. Validate structure by checking `//@version=5` header and `indicator()`/`strategy()` declaration.
- **Testing:** Pine Script has no local test runner. Backtesting is done visually within TradingView's Strategy Tester. The only local validation is structural (correct headers, no V6 syntax).
- **Linting:** No local Pine Script linter exists. Follow conventions in `.agent/rules.md` section 3.

### Key Constraints

- All code must target **Pine Script V5** — do NOT use V6 syntax (`import`, `export`, `method` keyword).
- Always update `doc/estrategia_btc.md` when modifying signal logic.
- Commit messages follow Conventional Commits format (see `.agent/rules.md` section 5).
- The Engram MCP instructions in `.agent/rules.md` are project-specific tooling — they do not affect the cloud agent environment.

### Validation Command

To verify all `.pine` files have correct structure:

```bash
for f in TradingViewer/*.pine; do
  grep -q '^//@version=5' "$f" && grep -qE '^(indicator|strategy)\(' "$f" && echo "✓ $f" || echo "✗ $f INVALID"
done
```
