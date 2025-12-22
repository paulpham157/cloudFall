# Repository Guidelines

## Project Structure & Module Organization
- `README.md` holds the project overview and problem statement.
- `LICENSE` contains licensing terms.
- `codex_gpt-5.2-codex/README.md` is currently empty; keep related assets close to their purpose and document any new directories here.
- No `src/` or `tests/` directories exist yet. If you add code, create `src/` for implementation and `tests/` for automated checks, and update this guide.

## Build, Test, and Development Commands
- No build or test scripts are configured in this repo yet.
- When adding tooling, document commands here and in `README.md` (e.g., `npm run build`, `npm test`, `make lint`) and ensure they run from the repo root.

## Coding Style & Naming Conventions
- For Markdown: use ATX headings (`#`), wrap prose at ~100 characters, and use backticks for commands and paths.
- For new code, follow the prevailing style in the file; if starting fresh, default to 2-space indentation for JS/TS, 4 for Python, and avoid mixed tabs.
- Naming: use `kebab-case` for new Markdown/docs, `camelCase` for variables/functions, `UpperCamelCase` for types/classes, `UPPER_SNAKE_CASE` for constants.

## Testing Guidelines
- No test framework is set up yet.
- If you add tests, place them in `tests/` or next to the module and name them `*.test.*` or `*.spec.*`. Document how to run them and keep critical logic covered.

## Commit & Pull Request Guidelines
- Git history shows `docs: modify README`; prefer a short, imperative subject and a `type: summary` prefix when applicable (`docs:`, `feat:`, `fix:`, `chore:`).
- PRs should include a concise description, testing notes (or "not run" with reason), linked issues when applicable, and screenshots for any UI changes.

## Security & Configuration
- Do not commit secrets. Use `.env` files for local configuration and document required variables in `README.md`.
