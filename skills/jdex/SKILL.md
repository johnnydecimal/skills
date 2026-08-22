---
name: jdex
description: Read and write to the user's JDex — their Johnny.Decimal index. Use this skill whenever the user mentions their JDex, asks to check, fetch, or update their own documentation, references a Johnny.Decimal ID (e.g. 12.34, W0189), invokes you from a folder with an ID in its name, asks to find a file or document, asks where a document belongs in their JD system, or asks a factual question about their own life or business (e.g. "where does X money go?", "what's the process for Y?", "trace where Z ended up"). The JDex is their knowledge base — treat any investigative or research question about their own information as a JDex lookup first.
---

# Prerequisites

- This skill requires the `johnnydecimal` skill for JD system context.
- Do NOT load the `jdhq` skill unless the user explicitly asks you to document JDHQ source code.
- This skill finds things and writes notes. It does not move files. If the user wants documents filed into their JD filesystem, tell them where each one belongs and let them move it, or ask before you move anything yourself.

# The jd MCP server

Johnny.Decimal knowledge comes from the `jd` MCP server, not from memory. The vault is the user's own overlay on it.

- `get_account` — which systems the account owns and whether it has Pro. Call it once, before the first system or position tool, so you know which tools will answer.
- `list_documentation`, `get_documentation` — how the Johnny.Decimal system works. Works with any account.
- `get_system_outline`, `get_id` — the published LAS or SBS scaffold. Needs an account that owns that system.
- `search_johnnys_positions`, `get_johnnys_position` — Johnny's own views, for judgement calls the documentation does not settle. Needs Pro. Opinion, not specification: the documentation tools hold the official text.

Which to use:

- Anything about the user's own content — their notes, their files, their business — is a vault question. Do not call the MCP for it.
- Anything about how Johnny.Decimal itself works, where you are unsure, is a documentation call. Do not answer JD concept questions from memory.
- Where both can answer, the vault note numbered `13.42` is the user's record of published ID `13.42`. Prefer the vault for how THEY use an ID, the MCP for what the published system says.

If the server is not connected, say so once and continue vault-only. Connect with:

```
claude mcp add --transport http jd https://johnnydecimal.com/mcp
```

# The JDex and the filesystem

There are two parallel structures. Both use the same JD hierarchy. Their paths come from `~/.jd/config.json`, the canonical Johnny.Decimal client config — read it once at the start. Each entry in its `systems` list has:

- `jdex` — the index. Markdown notes, usually an Obsidian vault. This is where you look things up and write documentation.
- `root` — the filesystem. The actual files (PDFs, images, documents) in JD-structured folders.

With several systems in the config, use the entry marked `default: true` unless the user names another.

If the config is missing, or the system you want has no `jdex` path, find what's missing and offer to save it:

1. Look first. Areas are folders named `NN-NN Title`, e.g. `10-19 Life admin`. A shallow search of `~/Documents`, `~/Dropbox`, `~/Library/Mobile Documents` and similar usually finds the filesystem root. The index is often an Obsidian vault — look for a `.obsidian` folder.
2. Confirm with the user. Show what you found and ask if it's right. If you found nothing, ask them for the two paths.
3. Offer to write `~/.jd/config.json` so nothing has to ask again. Ask before writing.

```json
{
  "version": 1,
  "systems": [
    {
      "sys": "D25",
      "title": "Johnny.Decimal",
      "root": "/path/to/the/filesystem",
      "jdex": "/path/to/the/index",
      "default": true
    }
  ]
}
```

- `root` is the only required key. `jdex` is required for anything in this skill.
- `sys` is the system identifier, the `SYS` in `SYS.AC.ID`. Most people have one system and don't need it.
- `default: true` marks the usual system. It only matters when there is more than one.

If the file already exists, add to its `systems` list and leave the other entries alone. Never overwrite it.

Two files share the name `config.json` and answer different questions. Tell them apart by their keys:

- `~/.jd/config.json` has a `systems` list. It says which systems exist and where they are. One per machine.
- `.jd/config.json` in a working folder has an `id`. It says which ID that folder is about. It never carries a `systems` list.

Neither overrides the other. If a folder one ever carries `root` or `jdex`, those win for that folder only.

In the examples below, `$JD_JDEX` and `$JD_ROOT` stand for these two paths. Substitute the real values — shell variables don't persist between your Bash calls.

The JD numbering makes paths deterministic. The digits of the ID give you the folders:

- ID `13.42` → `10-19*/13*/13.42*`
- ID `63.14` → `60-69*/63*/63.14*`
- Work package `W0189` → `W0000-9999*/W0189*`

Work packages are an optional extra area, `W0000-9999`, holding IDs of the form `W0189`. They sit directly in that area with no category level. The name usually carries a `~` and a normal ID — `W0189~21.41` — meaning that package belongs to `21.41`. Glob on the number alone, `W0189*`, because the rest of the name varies. An entry may be a single `.md` or a folder of them.

So you can go straight there with a single glob:

- JDex note: `$JD_JDEX/10-19*/13*/13.42*` (the `.md` file)
- Filesystem folder: `$JD_ROOT/10-19*/13*/13.42*` (the actual files)
- Child entry: `$JD_ROOT/10-19*/13*/13.42*/+Savings/`

The numbers give you the path. Don't search for it.

# Which ID to use

- Your **active ID** is determined once per session, in this priority order:
  1. A `.jd/config.json` naming the ID this folder is about: `{"id": "12.34"}`, plus `"sys": "D25"` when the user runs more than one system. Walk up from the working directory to the first one you find, stopping at the home folder — the nearest wins. Do not read `~/.jd/config.json` for this; that file has no `id`.
  2. A bracketed ID in the current folder name, e.g. `my-project [12.34]`.
  3. An ID the user explicitly provides when asked.
- Once an active ID is set, **stick with it**. All reads, writes, and documentation go to that ID's JDex entry.
- Do NOT browse, scan, or explore the JDex looking for other entries. The JDex is not a task list to trawl through. You work on the active ID.
- Only switch to a different ID if the user explicitly gives you a new one — e.g. "now look at 56.78" or "update W0189". Mentions of other IDs in note content, related links, or conversation context are NOT instructions to switch.
- If you're unsure whether the user wants a different ID, ask. Do not assume.

- If there is no `.jd/config.json`, no bracketed ID, and the user hasn't provided one, STOP. Ask the user: "Which JDex ID should I use?" Do not search the JDex for a match, do not infer from the project name, and do not continue until the user gives you one.

# Use of the JDex

- Each entry is a Markdown file. To reach one directly, glob using the ID's digits, as above.
- When the user says "document this" or "write it up" in the context of this skill, they mean write it in the JDex entry — not in a local CLAUDE.md, README, or any other file.
- Follow links in the `Related` metadata field when they're relevant to the task. Reading a linked entry for context is fine — but the active ID does not change. All writes go to the active ID's entry unless the user explicitly gives you a different ID.
  - Create links where relevant. Use the shortest `[[12.34 Title]]` wiki-link syntax.

# Writing things

- Before you write a note, read a neighbouring note in the same category. Match its layout — the user's own conventions beat any general rule.
- You are encouraged to update the JDex, but you MUST annotate the parts you updated with the tag `#claude`, so the user can find and review them.
  - A tagged header covers everything under it, subheaders included. Tag the header, nothing else.
  - Outside a tagged section, tag each line you added or changed.
  - Never tag a line the user wrote.
- If you update something, follow wikilinks and check if anything in the linked pages needs to be updated.
  - If it seems obvious, just fix it.
  - If unsure, ask.

# Finding things

**The JDex is the starting point for every question about the user's own information.** Whether they ask to find a file, trace a payment, understand a process, or answer any factual question about how things work — start with the index. Do not trawl the filesystem looking for a destination, and do not delegate to subagents: the skill context doesn't transfer. The index has the answer or links to it.

## Step 1: narrow by structure, then list

The numbering exists so you don't need the whole index. Walk down it.

1. `ls` the jdex root. That gives you the areas, a handful of lines. It is normally a folder of area folders — if it turns out to be a single file, read that file and stop here.
2. List the categories inside the area the question points at. If you can't tell yet, list them across all areas. Still small.
3. List the `.md` files in the likely category. Those are the entry titles. Scan them for a match.

Entries are named `AC.ID Title.md`, or `W0189~21.41 Title.md` for a work package. `AC.00` to `AC.09` are system management, not filing destinations — ignore them. Do not read the note bodies at any step.

If the question points at no category at all, list every `.md` below the root in one pass:

```
find "$JD_JDEX" -name '*.md' -not -path '*/.obsidian/*'
```

That is the expensive option — on a large vault it runs to thousands of lines. Narrow first when you can.

Present the result as a JD path, e.g. `13 Money > 13.42+ Savings`.

If nothing matches on titles, fall back to a content search: `obsidian search query="..." format=json`.

## Step 2: go to the filesystem (if needed)

Only when the user wants the actual files, not just the JDex note. Go straight there with the glob:

```
ls $JD_ROOT/10-19*/13*/13.42*             # the ID folder
ls $JD_ROOT/10-19*/13*/13.42*/+Savings/  # a child entry
```

Not needed when they're just asking "where would I find X?".

# Obsidian

- The JDex is usually an Obsidian vault. There will be a `.obsidian` folder at the root.
- Check for the `obsidian` command with `command -v obsidian`. It queries the vault directly.
- `obsidian help` lists every command. The useful ones here:
  - `obsidian search query="..." format=json` — searches content as well as filenames.
  - `obsidian backlinks file="12.34 Some title.md"` — what links here.
  - `obsidian links file="12.34 Some title.md"` — what this links to.
