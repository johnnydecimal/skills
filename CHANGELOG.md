# Changelog

> Written by Claude.

The version number lives in `.claude-plugin/plugin.json`. Claude Code uses it to decide when installed users get an update, so bump it with every change that reaches a skill: 1.1.0, 1.2.0, and so on. Each release has a git tag, for example `v1.1.0`, and a GitHub release with the same body as its entry here.

## 1.1.0 – 2026-09-13

> AI generated. Reviewed by Johnny.

This release adds a Moving in section to the `jdex` skill. Moving in puts
your existing files into the system you have installed.

### Features

- `jdex` has a Moving in section. The server's `move_in` tool holds the
  process. The skill holds the local half: the JD CLI moves each file
  with `jd move`, the run's state lives in the `00.05` note, and the
  answer to the contents question is recorded once.
- The prerequisites line now says the skill moves no files itself, and
  points to Moving in for the CLI.

### Changes

- The README says what Moving in does, and that the JD CLI is also
  needed to move files.

## 1.0.0 – 2026-09-12

- First versioned release.
- Add `LICENSE`. The skills are MIT.
- Add the Claude Code plugin manifest, so `claude plugins install` works as well as `npx skills add`.
- Add `agents/openai.yaml` to each skill, so Codex shows a display name and a short description.
- Add `license` and `compatibility` to the skill frontmatter.
