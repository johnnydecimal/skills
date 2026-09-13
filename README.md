# Johnny.Decimal skills

> Notice: Claude wrote some of this.

Skills that let an AI agent work with your [Johnny.Decimal](https://johnnydecimal.com) system. Read and write your JDex, find your files, and keep your notes up to date.

## Install

There are two routes. Pick one. Both together gives you every skill twice.

### As a Claude Code plugin

A read-only bundle that Claude Code updates for you.

```
claude plugin marketplace add johnnydecimal/skills
claude plugin install johnnydecimal-skills@johnnydecimal
```

Or, from inside a session, `/plugin marketplace add johnnydecimal/skills` and then `/plugin install johnnydecimal-skills@johnnydecimal`.

### As files you own

For Codex, Cursor, and every other agent, and for Claude Code if you want to edit the skills.

```
npx skills add johnnydecimal/skills
```

Or copy the folders in `skills/` into your agent's skills directory yourself. For Claude Code that is `~/.claude/skills/` for every project, or `.claude/skills/` for one.

## What you get

### `jdex`

Reads and writes your JDex, your Johnny.Decimal index. Ask it to find a file, look up an ID, trace how something works, or write a note. Invoke it by mentioning your JDex, for example "read my jdex and catch up".

To make a new ID or work package, it runs the JD CLI, `jd new`. It never writes a new ID by hand. If the CLI is not installed, it gets the install steps from the MCP server.

To put your existing files into your system, it calls the server's `move_in` tool and follows that process. The JD CLI moves each file, with `jd move`. The skill moves nothing itself. Neither the skill nor the CLI deletes a file.

### `johnnydecimal`

Reference material on how Johnny.Decimal works. You do not invoke this one. The `jdex` skill loads it when it needs it.

## Before you start

### 1. Tell the skills where your system is

Create `~/.jd/config.json`:

```json
{
  "version": 1,
  "systems": [
    { "root": "/path/to/your/filesystem", "jdex": "/path/to/your/jdex" }
  ]
}
```

- `root` is your Johnny.Decimal filesystem.
- `jdex` is your index, for example your Obsidian vault.
- With more than one system, mark the usual one `"default": true`, and give each a `"sys"` — its system identifier, the `SYS` in `SYS.AC.ID`.

If you skip this, the skill offers to find your locations and write the file for you.

### 2. Connect the MCP server

The skills get their Johnny.Decimal knowledge from the MCP server rather than carrying a copy, so they stay current.

```
claude mcp add --transport http jd https://johnnydecimal.com/mcp
```

Sign in when prompted. The documentation tools work with any account. The tools that serve a published system need an account that owns that system.

### 3. Install the JD CLI, if you want new IDs made or files moved

`jd new` makes IDs and work packages. `jd move` puts your existing files into your system. Ask your agent to install the JD CLI. The MCP server has the steps. Without it, the skill reads and writes notes, but it makes no new IDs and moves no files.

## Which ID am I working on?

`jdex` works on one ID at a time. It finds that ID in this order:

1. An ID at the start of the current folder's name, for example
   `12.34 My folder`. Open Claude in an ID folder and it knows the ID.
2. A `.jd/config.json` in the current folder, or the nearest one above it:
   ```json
   { "id": "12.34" }
   ```
   Add `"sys"` when you run more than one system.
3. An ID at the start of a parent folder's name, nearest first. This is for
   when you are in a subfolder, such as `12.34 Receipts/2026-03`.
4. It asks you.

It will not guess, and it will not go looking through your JDex for a match.

## AC.05, the agent's own ID

`AC.05` belongs to the agent. It keeps its notes there, so a later session can
read them back, and it is where you leave standing instructions for that part
of your system: how you name things, how you like a note laid out, anything you
want followed.

They are normal notes, so you can read and edit them yourself at any time. The
skill reads the narrowest one that applies, then the wider ones:

1. `13.05`, for anything in category 13.
2. `10.05`, for anything in area 10-19.
3. `00.05`, for the whole system.

A narrower note wins over a wider one, and any of them beats the skill's own
defaults.

You start with none of these. Tell the skill a rule and it writes the note for
you, named `AC.05 AI for <location> ✨` to match the `AC.01` inbox beside it,
for example `13.05 AI for category 13 ✨`.

## Two files called `config.json`

They answer different questions. Tell them apart by their keys.

| File | Question | Key |
|---|---|---|
| `~/.jd/config.json` | Which systems do I have, and where? | `systems` |
| `./.jd/config.json` | Which ID is this folder about? | `id` |

Neither overrides the other.

## Contributing

Each skill is a folder under `skills/` with a `SKILL.md`. Beside it, `agents/openai.yaml` gives Codex a display name and a short description. Add both to a new skill, and add the folder to the `skills` list in `.claude-plugin/plugin.json`.

The version number is in `.claude-plugin/plugin.json`. Bump it, and add a line to `CHANGELOG.md`, with every change that reaches a skill. Claude Code uses the version to decide when installed users get the update.

To release, merge to `main`, then tag the merge commit `vX.Y.Z` and make a GitHub release with the changelog entry as its body:

```
git tag -a v1.1.0 -m "1.1.0" && git push origin v1.1.0
gh release create v1.1.0 --title 1.1.0 --notes-file <the entry>
```

Check your work with:

```
claude plugin validate . --strict
claude plugin validate skills --strict
```

## License

MIT. See `LICENSE`.
