# Changelog

> Written by Claude.

This repo uses [semantic versioning](https://semver.org). The version number lives in `.claude-plugin/plugin.json`. Claude Code uses it to decide when installed users get an update, so bump it with every change that reaches a skill. Each release has a git tag, for example `v1.1.0`, and a GitHub release with the same body as its entry here.

## 1.2.0 – 2026-09-15

> AI generated. Reviewed by Johnny.

This release adds a Filing section to the `jdex` skill. Filing puts a
file into an ID, under a name that sorts.

### Features

- `jdex` has a Filing section. It holds the agent-side rules: the
  proposed name, the subfolder decision, the note line, and a folder
  that moves whole. The published rules for names and subfolders come
  from the server, with `get_documentation` for `naming-files` and
  `subfolder-patterns`.
- The skill loads when the user asks to file, name, or rename a file.
- Moving in points at Filing for names and subfolders.

### Changes

- The README says the skill proposes a name for each file, and a
  subfolder when a batch has a group.

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
