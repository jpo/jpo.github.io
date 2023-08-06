---
title: "Vim Tip: How to Use Vertical Guides"
description: "Vim Tip: How to Use Vertical Guides"
permalink: "{{ page | permalink }}"
date: '2012-06-29'
tags: [vim]
---

Vertical guides are a feature of most modern text editors that are useful when aligning text. Programmers typically use these to ensure that their code is indented properly or doesn’t exceed a certain line length (typically 80 or 100 characters).

In Vim, this feature is called ```colorcolumn```. When set, ```colorcolumn``` will change the color of one or more columns in the current window. Using this feature is easy. Simply set the ```colorcolumn``` variable to the value of the column you want to highlight in the vim command line. Below is an example of setting the ```colorcolumn``` to 80.

```vim
:set colorcolumn=80
```

If you want to highlight multiple columns, you can give ```colorcolumn``` a comma-seperated list of values.

```vim
:set colorcolumn=80,100
```

To turn ```colorcolumn``` off, you can simply set the value back to zero.

```vim
:set colorcolumn=0
```

Finally, if you want to change the color, you can use the ```highlight``` command.

```vim
:highlight ColorColumn guibg=red
```

## Further Reading

The Vim help documentation is an excellent resource if you know how to find what you’re looking for. You can find more information about ```colorcolumn``` by entering ```:help colorcolumn``` on the Vim command line.
