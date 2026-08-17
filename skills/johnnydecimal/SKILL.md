---
name: johnnydecimal
description: Reference material describing the Johnny.Decimal system — its structure, notation, and conventions. Use this to understand JD concepts when working with JD-related skills.
user-invocable: false
---

# The basics

- Johnny.Decimal is a system to help you manage information. That might be notes, files, or anything else.

- At the first level, the user defines **areas**. These are broad 'areas of life'.
  - E.g. a typical area is `10-19 Life administration`.
  - Areas should be broad. The goal is to have as few areas as possible: less choice is less ambiguity.

- Areas contain **categories**.
  - E.g. area `10-19` contains category `11 Me & other living things`.
  - Similarly we make them broad and use as few as possible.

- Categories contain **IDs**.
  - E.g. category `11` contains ID `11.11 Birth certificate & proof of name`.
  - IDs tend to be granular. They should relate to a specific concept, item, etc.

- The `AC.ID` notation is used to refer to any item in the abstract.
  - `AC.ID` means any ID.
  - `A0` means any category whose number ends `0`.
  - `1C.ID` means any ID in area `10-19`.
  - `11.ID` means any ID in category `11`.

- We skip the zeros, leaving them 'up front' for system management and metadata.
  - These are the 'standard zeros'.
  - `00-09` is reserved.
  - `A0` is reserved.
  - `AC.00` through `AC.09` is reserved.
  - Optionally, IDs ending in `0` are used as **headers**.
    - They group the IDs they contain.
    - This explains why we start our IDs at `11.11` and not `11.10`.
    - They should only be used in static systems where the ID layout is known ahead of time; otherwise they lock you in to a structure, which is undesirable in flexible systems.
  - This is an advanced concept that you should skip if unsure.

# Extend-the-end

- Optionally, an ID can be extended using a `+`.
- E.g. `12.34 Health` can have a child, `12.34+ Child's health`.
  - As in literally you use this to manage the health of your child.
- Can also be used to describe repeating items across the system, e.g. `56.78+ Work log` where `+ Work log` is a thing you could attach to any ID.
- The user may choose to keep these extended entries in a subfolder.
- Use cautiously. As a heuristic, think "might this extended item be its own ID?" or "is this thing likely to repeat across the system?"

# The Johnny.Decimal index or JDex

- The central record of these IDs is the user's **JDex**.
  - This is typically a notes app such as Obsidian.
  - Each note e.g. `11.11 Birth certificate & proof of name.md` is a JDex entry.

## JDex structure

- We assume a Markdown-based tool such as Obsidian, where the note's H1 is provided by the title of the file.

- The first line in each note is optionally a description.
  - It starts with a `>` and is followed by a blank line.

- Metadata key/value pairs follow.
  - Unless specified by the user, these are structured as bullet-lists with the key name, a colon, then the value(s) on an indented bullet on the next line(s). E.g.

```
- Related:
  - [[11.12 Some other ID]]
  - [[11.13 Yet another ID]]
```

- Users may prefer to use properly formed YAML.
- The metadata is followed by a blank line, a Markdown <hr> `---`, and another blank line.
- The user's freeform data follows.
  - You may be asked to update the note. This is where you start.

## Standard metadata properties

- `Data:`
  - Freeform text explaining in which external repository data for this ID might be found.
- `Related:`
  - Typically a wiki-link to another note.
  - As a rule, link _from_ the ID with the lowest number (11.11) _to_ the ID with the highest (99.99).
  - Assume backlinks are provided by the JDex software; do not explicitly form two-way links.
- `URL:`
  - A URL.

## An example JDex entry

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
