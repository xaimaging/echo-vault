# Repository Guidelines

## Project Structure & Module Organization

The repository is the `uliya-skill-kit/` directory. Its root `SKILL.md` is the only required entry point for the Uliya Skill. Use `references/` for progressively disclosed workflows, rules, and examples; keep reusable examples in `references/examples/`. JSON contracts live in `schemas/`, the public capability catalog is in `data/`, and `assets/skill-template/` contains the starter template. Node-based validation code is under `scripts/`. Documentation standards belong in `docs/`.

## Build, Test, and Development Commands

Run commands from the `uliya-skill-kit/` directory:

```powershell
node scripts/check-skill.mjs path\to\skill
node scripts/check-skill.mjs .
```

The checker validates a target Skill directory and emits a JSON result. It exits nonzero for an unuploadable or invalid target. There is no package manifest, build system, or local development server; changes are primarily Markdown, JSON, and small Node modules.

## Coding Style & Naming Conventions

Use UTF-8 Markdown with concise headings and actionable instructions. Preserve the existing frontmatter style in `SKILL.md`; Skill names must be lowercase, numeric where needed, and hyphenated (for example, `invoice-audit`). Use two-space indentation in JavaScript and JSON, semicolons in JavaScript, and double-quoted JavaScript strings. Keep schema and catalog keys stable unless the related validation logic is updated.

## Testing Guidelines

The repository uses the custom static checker rather than a test framework. Run it against every changed Skill directory before submitting changes, and inspect both the JSON diagnostics and exit status. When changing schemas or checker behavior, also validate representative valid and invalid fixtures and update relevant documentation.

## Commit & Pull Request Guidelines

No Git history is present in this checkout, so no repository-specific commit convention can be inferred. Use short imperative commit subjects such as `docs: clarify skill validation`. Pull requests should explain the affected workflow, list validation commands and results, identify changed schemas or public contracts, and include example output when checker behavior changes.

## Security & Configuration Tips

Do not add credentials, private NAS data, or claims of runtime execution. Follow `references/safety-and-approval.md`; preserve approval boundaries and never bypass the documented delegation or capability checks.
