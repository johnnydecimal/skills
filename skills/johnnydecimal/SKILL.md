---
name: johnnydecimal
description: Reference material describing the Johnny.Decimal system — its structure, notation, and conventions. Use this to understand JD concepts when working with JD-related skills.
user-invocable: false
---

# Where the knowledge lives

Johnny.Decimal documentation lives on the `jd` MCP server, not in this file. This file holds two things only: enough vocabulary to read a number, and the JDex note conventions the server does not publish.

Do not answer a question about how Johnny.Decimal works from memory. Call `list_documentation` once to see every page, then `get_documentation` with the slug you need. The pages cover areas and categories, IDs, headers, AC.ID notation, the standard zeros, the JDex, naming files, subfolder patterns, the inbox and archive, multiple systems, and extend-the-end.

If the server is not connected, say so once, then answer from what is below and mark anything beyond it as uncertain.

# Reading a number

- `SYS.AC.ID`, e.g. `D25.11.11`. `SYS` is the system identifier and is usually left off.
- `AC` is the area-category pair. `11` is a category. It sits in an area, a range of ten, `10-19`.
- `ID` is the two digits after the decimal. `11.11` is an ID. IDs hold the content.
- In the abstract, `AC.ID` means any ID, `11.ID` means any ID in category `11`, `1C.ID` means any ID in area `10-19`, and `A0` means any category ending in `0`.

Numbers ending in zero are reserved for system management. `00-09`, `A0`, and `AC.00` to `AC.09` are never filing destinations. This is why IDs start at `11.11`.

An ID can be extended with a `+`, e.g. `12.34+ Child's health`. Read it as a child of `12.34`, or as an item that repeats across the system.

# The JDex

The JDex is the index: one note per ID, the master record of every ID in a system. It is usually an Obsidian or Bear vault. Creating the note is what creates the ID.

## Note structure

This is the user's own convention. The server does not publish it. Assume a Markdown tool where the note's H1 comes from the filename.

1. An optional description on the first line. It starts with `>` and is followed by a blank line.
2. Metadata key/value pairs. The key, a colon, then the values on indented bullets below it.
3. A blank line, a Markdown `---`, and another blank line.
4. The user's freeform notes. This is where you write.

Some users prefer proper YAML frontmatter for the metadata. Follow whatever the neighbouring notes do.

## Standard metadata properties

- `Data:` — freeform text saying where the data for this ID lives.
- `Related:` — wiki-links to other notes. Link from the lower number to the higher, e.g. from `11.11` to `99.99`. The JDex software provides backlinks, so never write the reverse link.
- `URL:` — a URL.

## An example entry

```12.34 Title of the entry.md

> A short description of what this ID represents.

- Data:
  - Folder in Dropbox.
- Related:
  - [[56.78 Another relevant ID]]
- URL:
  - https://example.com

---

## My notes

- Freeform notes go here. This is the main body of the entry.
- Any format the user likes.
- Typically bullet points.
```
