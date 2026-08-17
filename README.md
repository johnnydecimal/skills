# Johnny.Decimal skills

> Notice: Claude wrote some of this.

Skills that let an AI agent work with your [Johnny.Decimal](https://johnnydecimal.com) system. Read and write your JDex, find your files, and keep your notes up to date.

## Install

```
npx skills add johnnydecimal/skills
```

Or copy the folders in `skills/` into your agent's skills directory yourself. For Claude Code that is `~/.claude/skills/` for every project, or `.claude/skills/` for one.

## What you get

### `jdex`

Reads and writes your JDex, your Johnny.Decimal index. Ask it to find a file, look up an ID, trace how something works, or write a note. Invoke it by mentioning your JDex, for example "read my jdex and catch up".

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

## Which ID am I working on?

`jdex` works on one ID at a time. It finds that ID in this order:

1. A `.jd/config.json` in the current folder, or the nearest one above it:
   ```json
   { "id": "12.34" }
   ```
   Add `"sys"` when you run more than one system.
2. An ID in brackets in the folder name, for example `my-project [12.34]`.
3. It asks you.

It will not guess, and it will not go looking through your JDex for a match.

## Two files called `config.json`

They answer different questions. Tell them apart by their keys.

| File | Question | Key |
|---|---|---|
| `~/.jd/config.json` | Which systems do I have, and where? | `systems` |
| `./.jd/config.json` | Which ID is this folder about? | `id` |

Neither overrides the other.

## Licence

Use these however you like.
