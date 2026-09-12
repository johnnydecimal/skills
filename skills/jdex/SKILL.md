---
name: jdex
description: Read and write to the user's JDex — their Johnny.Decimal index. Use this skill whenever the user mentions their JDex, asks to check, fetch, or update their own documentation, references a Johnny.Decimal ID (e.g. 12.34, W0189), invokes you from a folder with an ID in its name, asks to find a file or document, asks where a document belongs in their JD system, or asks a factual question about their own life or business (e.g. "where does X money go?", "what's the process for Y?", "trace where Z ended up"). The JDex is their knowledge base — treat any investigative or research question about their own information as a JDex lookup first.
license: MIT
compatibility: Needs the jd MCP server at https://johnnydecimal.com/mcp, a ~/.jd/config.json, and a shell. Making new IDs needs the JD CLI. Content search needs the obsidian CLI.
---

# Prerequisites

- This skill requires the `johnnydecimal` skill for JD system context.
- This skill finds things and writes notes. It does not move files. If the user wants documents filed into their JD filesystem, tell them where each one belongs and let them move it, or ask before you move anything yourself.

# The jd MCP server

Johnny.Decimal knowledge comes from the `jd` MCP server, not from memory. The vault is the user's own overlay on it.

Call `get_account` first. It says which systems the account owns and whether it has Pro, so you know which tools will answer. The tool descriptions say what each tool does. Do not keep a second copy of them here.

Which source answers:

- Their notes, their files, their business. Answer from the vault. Do not go to the MCP for facts about the user's own content.
- How Johnny.Decimal itself works. Call the documentation tools. Do not answer JD concept questions from memory.
- The active ID. Read both, in the order below.

If the server is not connected, say so once, then continue vault-only. Say what is lost: without it you are answering Johnny.Decimal questions from memory, and that is where wrong answers come from. Connect with:

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

The filesystem always nests like this. The JDex does not, so establish its layout once, with a single `ls` of `$JD_JDEX`:

- **Nested** — the root holds area folders, `10-19 Life admin`. A note is at `$JD_JDEX/10-19*/13*/13.42*`. The JDHQ Obsidian downloads default to this.
- **Flat** — the root holds the notes themselves, `13.42 Some title.md`. A note is at `$JD_JDEX/13.42*`. A notes app with no folders gives you this, and it is offered as a download too.
- **One file** — the root is a single `.md`. Read it and work inside it.

Both of the first two are normal. Do not treat a flat JDex as a broken nested one.

So you can go straight there with a single glob:

- JDex note: `$JD_JDEX/13.42*` when flat, `$JD_JDEX/10-19*/13*/13.42*` when nested (the `.md` file)
- Filesystem folder: `$JD_ROOT/10-19*/13*/13.42*` (the actual files)
- Child entry: `$JD_ROOT/10-19*/13*/13.42*/+Savings/`

The numbers give you the path. Don't search for it.

# Which ID to use

- Your **active ID** is determined once per session, in this priority order:
  1. An ID at the start of the current folder's name, e.g. `12.34 My folder`, or `W0189~21.41 Some package`. This is the normal case. People open Claude directly in an ID folder, so check this first. Match `NN.NN` or `WNNNN` only. A category (`13 Money`) and an area (`10-19 Life admin`) are not IDs.
  2. A `.jd/config.json` naming the ID this folder is about: `{"id": "12.34"}`, plus `"sys": "D25"` when the user runs more than one system. Walk up from the working directory to the first one you find, stopping at the home folder. The nearest wins. Do not read `~/.jd/config.json` for this; that file has no `id`.
  3. An ID at the start of a parent folder's name, nearest first. This covers working in a subfolder, e.g. `12.34 Receipts/2026-03`.
  4. An ID the user explicitly provides when asked.
- Once an active ID is set, **stick with it**. All reads, writes, and documentation go to that ID's JDex entry.
- Do NOT browse, scan, or explore the JDex looking for other entries. The JDex is not a task list to trawl through. You work on the active ID.
- Only switch to a different ID if the user explicitly gives you a new one — e.g. "now look at 56.78" or "update W0189". Mentions of other IDs in note content, related links, or conversation context are NOT instructions to switch.
- If you're unsure whether the user wants a different ID, ask. Do not assume.

- If no folder in scope names an ID, there is no `.jd/config.json`, and the user hasn't provided one, STOP. Ask the user: "Which JDex ID should I use?" Do not search the JDex for a match, do not infer from the project name, and do not continue until the user gives you one.

## Read the published ID first

Once the active ID is set, call `get_id` for it, once. Then read their own JDex note for the same ID.

- `get_id` returns the published definition of that ID, plus any **supplements** that apply to it: further readings, and ops manuals.
- An **ops manual** is a step-by-step procedure. If one comes back, tell the user it exists and offer to follow it. Fetch its body with `get_id` only when they accept. Do not paste it into their note.
- Their note beats the scaffold on how they actually work. The scaffold beats their note on what the published system says.
- Skip the call when the server is not connected, or when the account does not own that system. Say so once and carry on.

## AC.05 is yours

`AC.05` is the one ID you control. It holds two things: your own notes, kept so a later session can read them back, and the user's standing instructions for this part of their system. Nothing in the published Johnny.Decimal systems uses `.05` for anything else.

Read them at the start of a session, narrowest first:

1. `<category>.05`. ID `13.33` gives `13.05`.
2. `<area management category>.05`. Area `10-19` gives `10.05`.
3. `00.05`, for the whole system.

Glob them like any other ID, following the JDex layout above: `$JD_JDEX/13.05*` when flat, `$JD_JDEX/10-19*/13*/13.05*` when nested. For a work package, use the ID it belongs to: `W0189~21.41` gives `21.05`, then `20.05`, then `00.05`.

- A narrower note beats a wider one on the same point. All three beat any general rule in this skill.
- Write to the one whose scope matches what you learned. Something true of the whole system goes in `00.05`, not in `13.05`.
- Anything the user tells you to follow is theirs. Record it as their instruction, and never edit or overrule it.
- Write what a later session needs and nothing more. This is a working note, not a log. Prune it when it goes stale.
- It is still their vault. `AC.05` is not a place to put anything you would not show them.

### Creating an AC.05 note

If the note you need isn't there, create it.

Name it `AC.05 AI for <location> ✨`. Copy `<location>` from the `AC.01` note in the same place. `AC.01` is the inbox, and it always exists.

| Read this | Write this |
| --- | --- |
| `13.01 Inbox for category 13` | `13.05 AI for category 13 ✨` |
| `10.01 Inbox for area 10-19` | `10.05 AI for area 10-19 ✨` |
| `00.01 Inbox for the Small Business System` | `00.05 AI for the Small Business System ✨` |

- Leave the ✨ off if the neighbouring notes carry no emoji. The user has them turned off.
- Create it when you have something to put in it, not on the chance you might.
- The JDex note is all you create here. The filesystem folder is not yours to make. This is the one note you write by hand. Every other new ID goes through the CLI, under Creating things below.

# Use of the JDex

- Each entry is a Markdown file. To reach one directly, glob using the ID's digits, as above.
- When the user says "document this" or "write it up" in the context of this skill, they mean write it in the JDex entry — not in a local CLAUDE.md, README, or any other file.
- Follow links in the `Related` metadata field when they're relevant to the task. Reading a linked entry for context is fine — but the active ID does not change. All writes go to the active ID's entry unless the user explicitly gives you a different ID.
  - Create links where relevant. Use the shortest `[[12.34 Title]]` wiki-link syntax.

# Writing things

- Before you write a note, read a neighbouring note in the same category. Match its layout — the user's own conventions beat any general rule.
- You are encouraged to update the JDex. The user MUST be able to find and review what you wrote. Mark it one of two ways.
  - If you wrote the file, or rewrote it in full, claim the whole file. Add an `Owner:` entry to the metadata block above the line. Use no inline tags.

    ```
    - Owner:
    	- #claude
    ```

  - If the file is the user's and you are changing part of it, tag only what you changed.
    - A tagged header covers everything under it, subheaders included. Tag the header, nothing else.
    - Outside a tagged section, tag each line you added or changed.
    - Never tag a line the user wrote.
  - Either way, a wikilink you add above the line needs no tag. Above the line is the metadata block before the `---` separator, where `Related:` and `URL:` live. Links there are navigation, not content.
- If you update something, follow wikilinks and check if anything in the linked pages needs to be updated.
  - If it seems obvious, just fix it.
  - If unsure, ask.

# Creating things

The JD CLI makes new IDs and work packages. Never write a new ID's note or folder by hand. The CLI picks the next free number, fills the note from the user's template, and makes the folder. Each of those is easy to get wrong by hand.

The program is `~/.jd/cli/bin/jd`. Nothing has to be sourced first. Check it with `~/.jd/cli/bin/jd help`.

- A new ID: `~/.jd/cli/bin/jd new id 21 A title`. This makes the next free ID in category 21. Give `21.34` instead of `21` for that exact ID. The user names the category. A new ID needs no active ID first.
- A new work package: `~/.jd/cli/bin/jd new wp 21.41 A title`. This makes the next free W number, and the package belongs to `21.41`.
- The title needs no quotes. It is every word up to the first word that starts with `--`.
- `--dry-run` says what it would make, and makes nothing. Use it when you are not sure.
- `--json` prints one object to branch on: `{ "ok": true, ... }` or `{ "ok": false, "code": "...", ... }`. Its `toFill` list names the template tokens you gave no value. Each token has a flag: `{{?SCOPE}}` is `--scope`. Ask the user for the values, or pass them when you know them.
- With more than one system, `--system` goes first: `~/.jd/cli/bin/jd --system P76 new id 21 A title`.
- `jd new` is a beta feature. If jd says beta is off, tell the user and ask before you run `jd beta on`.

The ID it made is your active ID from then on. Read its note before you write to it. The template shapes it, and the user's conventions are in there.

If the CLI is not installed, call `install_cli` on the server and follow its steps. If the server is not connected either, stop and say so. Do not make the ID by hand.

The one exception is the `AC.05` note, above. It has a fixed number, and its folder is not yours to make, so you write that note yourself.

# Finding things

**The JDex is the starting point for every question about the user's own information.** Whether they ask to find a file, trace a payment, understand a process, or answer any factual question about how things work — start with the index. Do not trawl the filesystem looking for a destination, and do not delegate to subagents: the skill context doesn't transfer. The index has the answer or links to it.

## Step 1: narrow by structure, then list

The numbering exists so you don't need the whole index. Walk down it.

1. `ls` the jdex root. That tells you the layout. A few lines when nested, one line per ID when flat. If it is a single file, read that file and stop here.
2. **Nested** — the root gave you the areas. List the categories inside the area the question points at, then the `.md` files in the likely category. If you can't tell which area yet, list the categories across all of them. Still small.
   **Flat** — the root already gave you every entry. Filter it by category, e.g. `ls "$JD_JDEX"/13.*`.
3. Those are the entry titles. Scan them for a match.

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
