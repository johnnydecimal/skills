# Changelog

> Written by Claude.

The version number lives in `.claude-plugin/plugin.json`. Claude Code uses it to decide when installed users get an update, so bump it with every change that reaches a skill.

## 1.1.0

- `jdex`: add a Moving in section. The server's `move_in` tool holds the process. The skill says that `jd move` moves the files, and that the run's state lives in the `00.05` note.

## 1.0.0

- First versioned release.
- Add `LICENSE`. The skills are MIT.
- Add the Claude Code plugin manifest, so `claude plugins install` works as well as `npx skills add`.
- Add `agents/openai.yaml` to each skill, so Codex shows a display name and a short description.
- Add `license` and `compatibility` to the skill frontmatter.
