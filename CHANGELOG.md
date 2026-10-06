# Changelog

> Written by Claude.

This repo uses [semantic versioning](https://semver.org). The version number lives in `.claude-plugin/plugin.json`. Claude Code uses it to decide when installed users get an update, so bump it with every change that reaches a skill. Each release has a git tag, for example `v1.1.0`, and a GitHub release with the same body as its entry here.

## 1.4.0 – 2026-10-06

> AI generated. Reviewed by Johnny.

Version 4.0 of the JD CLI no longer keeps its files in `~/.jd`. With
this release, the `jdex` skill finds the configuration file and the
CLI at their new paths. Update the skills before you move your
configuration file. The preferred way to install the skills is
`npx skills add johnnydecimal/skills`. If you installed the skills
that way, run `npx skills update` to get this release. Refer to the
[README](https://github.com/johnnydecimal/skills#install) for the
instructions.

### Changes

- `jdex` looks for the configuration file at
  `~/.config/johnnydecimal/config.json`. If you have the JD CLI,
  `jdex` gets the path from `jd paths config`.
- `jdex` looks for the JD CLI at
  `~/.local/share/johnnydecimal/cli/bin/jd`.
- If your configuration file or your JD CLI is still in `~/.jd`,
  `jdex` continues to use it. `jdex` tells you one time that you can
  move it, and asks before it does anything.
- The README gives the new path of the configuration file. It also
  says that `npx skills add johnnydecimal/skills` is the preferred
  way to install, and that `npx skills update` updates the skills.

## 1.3.0 – 2026-09-15

> AI generated. Reviewed by Johnny.

This release gives the configuration file one home, the JD CLI, and
makes the `jdex` skill load for a move in.

### Changes

- `jdex` no longer finds the user's systems or writes
  `~/.jd/config.json` itself. When the config is missing, it runs
  `jd agent-setup`, the CLI's own prompt, and follows it. If the CLI
  is not installed, it calls `install_cli` on the server first. The
  search steps and the JSON example are gone from the skill.
- The skill loads when the user asks to move in to their system or to
  put their existing files into it, so the Moving in section is in
  context before `move_in` is called.
- `jd move` is named next to `jd new` as a beta feature the user
  turns on.
- The README says the CLI writes the config when you skip it.

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
- The move is one `jd move` call. It renames the file and makes the
  subfolder as it goes. The agent reads `jd move --help` for the
  syntax, so the skill carries no copy of it.
- The skill loads when the user asks to file, name, or rename a file.
- Moving in points at Filing for names and subfolders.

### Changes

- The README says the skill proposes a name for each file, and a
  subfolder when a batch has a group.
- The prerequisites line says the JD CLI moves files under Moving in
  and Filing. Before, it said the agent moves no file outside Moving in.

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
