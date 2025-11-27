# Contributing Guide

This document describes how to update and maintain the safe-to-eat Hugo podcast project.

## Project Overview

safe-to-eat is a podcast appreciation project - a curated collection of podcast episodes worth sharing. Think Google Reader shares (RIP), but for podcasts.

## Build Information

This project is built on GitHub using GitHub Actions. The `public/` directory is generated during the GitHub Actions build process and should NOT be committed to the repository.

**Important:** If you run `hugo` locally to test the build, delete the `public/` directory before committing:

```bash
rm -rf public/
```

## Adding a New Episode

### 1. Create the Episode File

- Files go in `content/posts/`
- Format: `episode-XXX.md` (zero-padded to 3 digits)
- Examples: `episode-001.md`, `episode-045.md`

### 2. Use Today's Date

**Important:** The `date` field should be TODAY'S DATE (when you're adding the episode), NOT the original publication date of the podcast. This is a curation project - the date reflects when we added it to our collection.

### 3. Frontmatter Structure

Every episode file uses YAML frontmatter between `---` delimiters:

```yaml
---
title: Episode XXX
date: Day, DD Mon YYYY 08:00:00 +0000
eptype: full
episode_number: XX

# provide these
alm_description:

# find these
show_source: "Source Name"
original_title: "Episode Title"
original_subtitle: ""
original_description: "Description text here"
podcast_url: "https://example.com/audio.mp3"
audio_type: "audio/mpeg"
duration: HH:MM:SS
---
```

### 4. Field Reference

| Field | Required | Quotes | Notes |
|-------|----------|--------|-------|
| `title` | Yes | No | Format: `Episode XXX` |
| `date` | Yes | No | **Today's date** in RFC 2822 format: `Day, DD Mon YYYY HH:MM:SS +0000` |
| `eptype` | Yes | No | Always `full` |
| `episode_number` | Yes | No | Integer, no leading zeros |
| `alm_description` | No | No | Your personal notes (can be empty) |
| `show_source` | Yes | Optional | Source podcast name |
| `original_title` | Yes | Yes | Episode title from source |
| `original_subtitle` | No | Yes | Can be empty `""` |
| `original_description` | Yes | Yes | Episode description from source |
| `podcast_url` | Yes | Yes | Direct link to audio file |
| `audio_type` | Yes | Yes | Usually `audio/mpeg` or `audio/mp4` |
| `duration` | Yes | No | Various formats accepted: seconds, `MM:SS`, `HH:MM:SS` |

## Quoting Rules

### When to Use Double Quotes

**ALWAYS** wrap these fields in double quotes `"..."`:
- `original_title`
- `original_subtitle`
- `original_description`
- `podcast_url`
- `audio_type`

**OPTIONAL** but recommended for `show_source` if it contains special characters.

### When Quotes Are NOT Needed

- `title` - simple format, no special chars
- `date` - standard date format
- `eptype` - single word
- `episode_number` - integer
- `duration` - numeric format

## Character Handling

### SAFE Characters (no special handling needed)

Inside double-quoted strings, these are safe:
- Letters (a-z, A-Z)
- Numbers (0-9)
- Spaces
- Periods `.`
- Commas `,`
- Semicolons `;`
- Colons `:`
- Hyphens/dashes `-`
- Underscores `_`
- Forward slashes `/`
- Question marks `?`
- Equals signs `=`
- Ampersands `&`
- Parentheses `()`
- Standard apostrophes `'` (ASCII 0x27)
- En-dash `–` (generally safe, see episode-020)

### PROBLEMATIC Characters - Remove or Replace

**Important:** Do NOT use URL encoding (like `%5B`) for display text. URL-encoded characters will display literally on the page. Only use URL encoding in actual URLs.

| Character | Problem | Solution |
|-----------|---------|----------|
| `[` | YAML parsing issues | Remove entirely |
| `]` | YAML parsing issues | Remove entirely |
| `"` (inside string) | Breaks YAML string | Remove entirely |
| `—` (em-dash) | May cause issues | Replace with `–` (en-dash) or `--` |
| `'` (curly apostrophe) | Non-ASCII encoding | Replace with `'` (straight) |
| `'` (curly apostrophe) | Non-ASCII encoding | Replace with `'` (straight) |
| `"` `"` (curly quotes) | Non-ASCII encoding | Remove entirely |

### Quick Reference: Character Replacement

| Character | Replace With |
|-----------|--------------|
| `[` | (remove) |
| `]` | (remove) |
| `"` (inside string) | (remove) |
| `—` (em-dash) | `–` (en-dash) or `--` |
| `'` `'` (curly apostrophes) | `'` (straight) |
| `"` `"` (curly quotes) | (remove) |

### HTML in Descriptions

Some source descriptions contain HTML. Two approaches work:

**Option 1: Keep HTML tags** (if your templates handle it)
```yaml
original_description: "<p>First paragraph.</p><p>Second paragraph.</p>"
```

**Option 2: Use HTML entities**
```yaml
original_description: "We chat about:&lt;/p&gt;&lt;p&gt;-Topic one"
```

### Multi-line Descriptions

For long descriptions with line breaks, you can use multi-line YAML:
```yaml
original_description: "First paragraph here.

Second paragraph here.

Third paragraph here."
```

Note: The opening `"` must be on the same line as the field name.

## Common Patterns from Existing Episodes

### Simple Description (most common)
```yaml
original_description: "Matt is joined by reporter Spencer Ackerman, author of the new book Reign of Terror."
```

### Description with Apostrophes (safe)
```yaml
original_description: "In his landmark biography of Stalin, Stephen Kotkin shows how totalitarian power worked."
```

### Description with Colons (safe inside quotes)
```yaml
original_title: "Tyler Cowen: Second-Order Economics, Talent, and Straussianism"
```

### Empty Fields
```yaml
original_subtitle: ""
original_description: ""
```

### Show Source with Special Characters
```yaml
show_source: "99% Invisible"
show_source: O'Reilly Solid Podcast (RIP) with Jon Bruner
```
Note: Apostrophes work without quotes, but `%` requires quotes.

## Pre-Flight Checklist

Before committing a new episode file:

1. [ ] Date is set to TODAY'S date, not the original publication date
2. [ ] All string fields that need quotes have them
3. [ ] No `[` or `]` characters in descriptions (remove them)
4. [ ] No curly quotes or curly apostrophes (use straight versions)
5. [ ] No internal double quotes in double-quoted strings
6. [ ] Em-dashes are replaced with en-dashes or `--`
7. [ ] File follows naming convention: `episode-XXX.md`
8. [ ] Run `hugo` locally to verify no parsing errors
9. [ ] Delete `public/` directory before committing

## Troubleshooting

**Hugo fails to parse frontmatter:**
- Check for unbalanced quotes
- Look for `[` or `]` characters
- Verify no internal `"` in double-quoted strings

**Strange characters in RSS feed:**
- Check for curly quotes/apostrophes
- Look for em-dashes or en-dashes
- Verify file is UTF-8 encoded

**Build succeeds but content looks wrong:**
- Check HTML entities are correct
- Verify multi-line strings maintain structure

**URL-encoded characters showing literally (like %5B):**
- Don't use URL encoding for display text
- Remove or replace problematic characters instead
