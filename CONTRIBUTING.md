# Contributing to EMERGENCE

## Philosophy First

Before writing any code, read [`SAFETY.md`](SAFETY.md). The safety protocol is not a feature — it is the foundation. Any contribution that weakens, bypasses, or undermines the five guardrails will not be merged.

## How to Contribute

1. Fork the repo
2. Create a feature branch (`git checkout -b feature/my-feature`)
3. Commit your changes (`git commit -m "Add: my feature"`)
4. Push to your branch (`git push origin feature/my-feature`)
5. Open a Pull Request

## Commit Conventions

- `Add:` — new feature or file
- `Fix:` — bug fix
- `Refactor:` — code restructuring (no behavior change)
- `Docs:` — documentation only
- `Safety:` — anything related to the guardrail system
- `Test:` — adding or updating tests

## Code Style

- Python: Google-style docstrings, type hints on public signatures
- ASCII only in strings and comments (no accented characters in code)
- All scripts must include error handling and logging
- No hardcoded paths — use configuration files

## What We Need Most Right Now

This project is in manifesto phase. The most valuable contributions are:

1. **Technical critique** — Is the architecture sound? What's missing?
2. **Safety review** — Are the guardrails sufficient? What failure modes are we missing?
3. **Hardware benchmarks** — If you have an AMD APU system, run Qwen3 and report performance
4. **Philosophical feedback** — Does the framework make sense? What are the blind spots?

## Bug Reports

Open a GitHub Issue with:
- Steps to reproduce
- Expected vs actual behavior
- OS + Python version + hardware specs
