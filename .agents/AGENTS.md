# Math Explorer - Project Rules and Learned Patterns

## Project Overview
- Hugo static site using the PaperMod theme
- Target audience: Class 6+ math students (India)
- Reference project for patterns: https://github.com/codefrydev/Updates

---

## KaTeX Math Rendering - Correct Setup

### Architecture
KaTeX is loaded manually via extend_head.html (CSS) and extend_footer.html (JS),
because PaperMod does NOT have built-in KaTeX support. The math = true in hugo.toml
is a custom user param - it does nothing by itself in PaperMod.

### Correct Loading Pattern (extend_footer.html)
Load both KaTeX scripts SYNCHRONOUSLY (no defer) at end of body, then call
renderMathInElement directly in inline script after. NEVER use defer + onload
together - this creates a race condition where onload fires before the deferred
katex.min.js has executed.

### Goldmark Passthrough (hugo.toml)
Must be configured so Hugo Markdown parser does not consume dollar signs:

  [markup.goldmark.extensions.passthrough]
  enable = true
  [markup.goldmark.extensions.passthrough.delimiters]
  inline = [['\$', '\$'], ['\\(', '\\)']]
  block = [['\$\$', '\$\$'], ['\\[', '\\]']]

### Currency Dollar Signs in Markdown
Never use bare dollar signs like dollar-40 in post content - KaTeX will try to parse
them as math. Always wrap currency dollar signs in span tags:
  The book costs <span>$</span>40.
This works because KaTeX auto-render only processes text nodes, not element nodes.

### Do NOT use
- defer on katex.min.js combined with onload on auto-render.min.js (race condition)
- Bare dollar signs in plain prose text
- Backslash-dollar in plain Markdown text (backslash renders literally)
- dollar-text-backslash-dollar-amount-dollar pattern - too fragile

---

## Content Writing Rules

### Math Expressions
- Inline math: dollar-expression-dollar
- Block math: double-dollar-expression-double-dollar on its own line
- Currency amounts: <span>$</span>40 in prose, \$ inside math mode
- Dollar sign in block math: use \$ (e.g., Discount = \)

---

## Reference Project Patterns (codefrydev/Updates)
- Uses config.yaml (YAML) vs our hugo.toml (TOML) - both valid
- Same PaperMod theme, same math = true global param
- No extend_footer.html for KaTeX in that repo
- Uses Mermaid diagrams via extend_head.html
- Has Microsoft Clarity analytics injected in extend_head.html
