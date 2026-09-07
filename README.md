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

## Telling it your own conventions

`AC.05` is free in every Johnny.Decimal system. `jdex` treats it as the place
for your own rules: how you name things, how you like a note laid out,
anything you want followed in that part of your system.

Write them as normal notes. The skill reads the narrowest one that applies,
then the wider ones:

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

## Licence

Use these however you like.
