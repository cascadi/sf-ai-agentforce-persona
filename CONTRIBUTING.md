---
version: "1.0"
date: 2026-03-02
---

# Contributing

Contributions are welcome. The API surface (framework attributes, template structure, workflow steps) may change between versions.

## Reporting Issues

Open an issue on GitHub. Include:
- What you expected to happen
- What actually happened
- The step in the workflow where it occurred (if applicable)

## Pull Requests

1. Fork the repo and create a feature branch
2. Make your changes
3. Validate upstream compatibility: `python3 skill-builder/scripts/bulk_validate.py` (run in a fork of [jaganpro/sf-skills](https://github.com/jaganpro/sf-skills))
4. Open a PR with a clear description of the change

## AI Disclosure

If AI tools were used to generate or substantially edit content in your contribution, disclose this in the PR description. This is a requirement for upstream contribution to [jaganpro/sf-skills](https://github.com/jaganpro/sf-skills).

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
