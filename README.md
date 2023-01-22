# notes

This repo stores my notes, PBL records, ideas, etc.

Each file or folder contains a note.

## Contents

<!-- TODO -->

## Markdown Note Template and Conventions

```markdown
title: < TITLE NAME >
[summary: # Only one line]
[author:     # Default value is "小 K" or "Little K" if ommited]
[datetime:   YYYY-MM-DD HH:MM:SS  # Create time
            [YYYY-MM-DD HH:MM:SS]  # Modified time if this field exists]
[tags:       tagname1
             [tagname2]
             [tagnamen]  # Multiple lines if needed; 1 line for each tag; No blank sign inside tagname]
[privacy:    # 1 for private; 0 for public; Default value is 0 if ommited]
[collection: # The collection name this note belongs to; In default collection if ommited]

[TOC]

# < TITLE NAME >

< NOTE BODY >
```

- Omitting contents between `[` and `]` is allowed.
- Each field name and value are separated by blank signs (including single/multiple spaces/tabs).
- The field name is case insensitive.
- The default encoding is UTF-8.
- Headers in `< NOTE BODY >` start with the second-level header, as the first-level header is for the note title.

## License

[![CC BY 4.0][cc-by-shield]][cc-by]

This work is licensed under a
[Creative Commons Attribution 4.0 International License][cc-by].

[![CC BY 4.0][cc-by-image]][cc-by]

[cc-by]: http://creativecommons.org/licenses/by/4.0/
[cc-by-image]: https://i.creativecommons.org/l/by/4.0/88x31.png
[cc-by-shield]: https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg