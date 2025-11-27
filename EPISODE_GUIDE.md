# Episode File Guide

This document describes how to create and format episode files for the safe-to-eat Hugo podcast project.

## File Naming

- Files go in `content/posts/`
- Format: `episode-XXX.md` (zero-padded to 3 digits)
- Examples: `episode-001.md`, `episode-045.md`

## Frontmatter Structure

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

## Field Reference

| Field | Required | Quotes | Notes |
|-------|----------|--------|-------|
| `title` | Yes | No | Format: `Episode XXX` |
| `date` | Yes | No | RFC 2822 format: `Day, DD Mon YYYY HH:MM:SS +0000` |
| `eptype` | Yes | No | Always `full` |
| `episode_number` | Yes | No | Integer, no leading zeros |
| `alm_description` | No | No | Your personal notes (can be empty) |
| `show_source` | Yes | Optional | Source podcast name |
| `original_title` | Yes | Yes | Episode title from source |
| `original_subtitle` | No | Yes | Can be empty `""` |
| `original_description` | Yes | Yes | Episode description |
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

### SAFE Characters (no encoding needed)

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

### PROBLEMATIC Characters - URL Encode or Remove

| Character | Problem | Solution |
|-----------|---------|----------|
| `[` | YAML parsing issues | URL encode: `%5B` or remove |
| `]` | YAML parsing issues | URL encode: `%5D` or remove |
| `"` (inside string) | Breaks YAML string | Remove or use HTML entity `&quot;` |
| `—` (em-dash) | Non-ASCII encoding | URL encode: `%E2%80%94` or use `--` |
| `–` (en-dash) | Non-ASCII encoding | URL encode: `%E2%80%93` or use `-` |
| `'` (curly apostrophe) | Non-ASCII encoding | Replace with `'` (straight) |
| `'` (curly apostrophe) | Non-ASCII encoding | Replace with `'` (straight) |
| `"` `"` (curly quotes) | Non-ASCII encoding | Remove or replace with nothing |

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

1. [ ] All string fields that need quotes have them
2. [ ] No unescaped `[` or `]` characters in descriptions
3. [ ] No curly quotes or curly apostrophes (use straight versions)
4. [ ] No internal double quotes in double-quoted strings
5. [ ] Em-dashes are URL-encoded or replaced
6. [ ] File follows naming convention: `episode-XXX.md`
7. [ ] Run `hugo build` to verify no parsing errors

## Quick Reference: URL Encoding

| Character | URL Encoded |
|-----------|-------------|
| `[` | `%5B` |
| `]` | `%5D` |
| `—` (em-dash) | `%E2%80%94` |
| `–` (en-dash) | `%E2%80%93` |
| space | `%20` |
| `&` | `%26` |

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
