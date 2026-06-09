# Contributing to QE-Copilot

Thank you for your interest in contributing! QE-Copilot is an open-source AI-native test automation framework and all contributions are welcome.

---

## Ways to Contribute

- **Report a bug** — open an issue with a clear title and reproduction steps
- **Suggest a feature** — open an issue describing the use case and expected behaviour
- **Add an example** — contribute a real-world example project (e.g. for a public demo app)
- **Improve documentation** — fix typos, clarify instructions, add diagrams
- **Improve templates** — better defaults in `templates/`, `actions/`, or `knowledge/`
- **Improve AGENT.md** — sharper prompts, better edge case handling, new MCP tool guidance

---

## Getting Started

```bash
# Fork the repository, then:
git clone https://github.com/<your-username>/qe-copilot.git
cd qe-copilot

# Create a feature branch
git checkout -b feature/your-feature-name
```

---

## Contribution Guidelines

### Templates and AGENT.md

- Keep all content **application-agnostic** — no hardcoded app names, URLs, or domain terms
- Use `_instructions` keys in JSON knowledge files to guide users
- Every template must work as a drop-in starting point with minimal editing

### Examples

- Each example lives in its own folder (e.g. `examples/saucedemo/`)
- Must be **fully self-contained** — its own `AGENT.md`, `configs/`, `actions/`, `testdata/`, `knowledge/`
- Must be **ready to run** with no setup beyond MCP configuration
- Must target a **publicly accessible** application (no private apps)
- Include an `expected-results/` folder with reference JSON per test case

### Pull Requests

1. One logical change per PR — keep PRs small and focused
2. Update `CHANGELOG.md` under `## [Unreleased]`
3. If changing `AGENT.md`, test your change by running at least one test case end-to-end
4. PR title format: `feat: add X`, `fix: correct Y`, `docs: improve Z`

### Issues

- Bug reports: include AI agent used, OS, MCP version, and the exact error or unexpected behaviour
- Feature requests: describe the use case first — what are you trying to test?

---

## Code of Conduct

Be respectful. Constructive criticism is welcome. Personal attacks are not.

---

## Questions?

Open a GitHub Discussion or an issue tagged `question`.
