---
title: Regex
top: false
cover: false
toc: true
date: 2022-04-12 14:57:48
password:
summary:
description: Learn Regex
categories:
  - Programming
tags:
  - Regex
---

# Regular Expressions in Vim

## Lookahead and Lookbehind

Directly some examples.

See `:help /\@=`, `:help /\@!`, `:help /\@<=`, and `:help /\@<!` for detail.

### Positive lookahead with `\@=` and negative lookahead with `\@!`

```tex
quick fox quick dog quick fox
quick dog quick fox
dog fox
```

Find `quick` if followed by `dog` with `/quick\( dog\)\@=`.

Find `quick` if not followed by `dog` with `/quick\( dog\)\@!`.

### Positive Lookbehind with `\@<=` and negative Lookbehind with `\@<!`

Find `fox` preceded by `quick` with `\(quick \)\@<=fox`.

Find `fox` not preceded by `quick` with `\(quick \)\@<!fox`.
