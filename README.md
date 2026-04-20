# OpenClaw Guard Skills

Shared guard skills for OpenClaw-based bots and runtimes.

This repository is the source of truth for reusable guard skills that should be
versioned and released independently from any single bot or channel plugin.

This repository can also be installed as an OpenClaw plugin-style skill bundle
because it ships `openclaw.plugin.json` with `skills/` as its exported skill
root.

## Included skills

- `internal-safety-guard`
- `file-send-safety-guard`
- `docx-output-guard`
- `zapry-chat-id-guard`
- `zapry-send-strict`

## Versioning

Each skill package carries its own frontmatter version so runtimes can pin a
specific repository ref and still inspect the packaged skill version.

## Release model

The intended production flow is:

1. Tag this repository or pin a commit SHA.
2. Let `openclaw-runtime` clone that fixed ref during image build.
3. Install the repository with `openclaw plugins install`.
4. Let the runtime load the bundled `skills/` directory globally.
