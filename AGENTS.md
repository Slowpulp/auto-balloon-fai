# Repository guidance

## Versioning

- The release version is stored in `VERSION`; release history is recorded in `CHANGELOG.md`.
- Keep V1 recoverable through the `v1.0.0` tag. The `main` branch packages the current V2 release, while the installed local V1 may remain on its own branch or checkout.
- Each validated release is one Git commit with an annotated `v<major>.<minor>.<patch>` tag.

## Validation

- Run the Skill Creator `scripts/quick_validate.py` against this directory in Python UTF-8 mode.
- Parse `agents/openai.yaml` as YAML and verify that `default_prompt` mentions `$auto-balloon-fai-v2`.
- Check that every relative Markdown link resolves and that `git diff --check` reports no whitespace errors.
- Confirm the project-mode decision matrix remains unambiguous: no named baseline means `independent`; only an explicitly named and authorized baseline enables `revision`.

## Release contents

- Include `SKILL.md`, `agents/openai.yaml`, `references/`, `README.md`, `VERSION`, `CHANGELOG.md`, and this file.
- Exclude `.git`, caches, temporary validation dependencies, generated FAI work products, source drawings, templates, credentials, and user data.
