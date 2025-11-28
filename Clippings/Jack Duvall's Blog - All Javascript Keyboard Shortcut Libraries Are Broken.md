---
title: "Jack Duvall's Blog - All Javascript Keyboard Shortcut Libraries Are Broken"
source: "https://blog.duvallj.pw/posts/2025-01-10-all-javascript-keyboard-shortcut-libraries-are-broken.html"
author:
published:
created: 2025-11-27
description: "Either subtly, or not-so-subtly. There is no way to fix the more subtle variant, and the only solution is to Give Up (on supporting a large subset of keyboard shortcuts)."
tags:
  - "clippings"
---
## What The Good Libraries Have To Do

When searching NPM for “shortcut” libraries sorted by most recently updated, I came across [keyboard-i18n](https://www.npmjs.com/package/keyboard-i18n), which has an interesting solution to this problem: it uses the experimental Keyboard API (only supported on Chrome) to read the [KeyboardLayoutMap](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardLayoutMap), which maps `code -> key` with no modifiers. From this, you can build a reverse mapping `key -> code`, which lets you specify your keyboard shortcuts using `key`, but read them using `code`, getting you the best of both worlds! `@sofie-automation/sorensen` also uses this approach afaict.

This is not quite the end of it, however; there are certain keys we might want to bind, like `Ctrl+[`, that won’t be present on all layouts. `keyboard-i18n` has some useful maps for certain keys like this that will work on international layouts without them, by mapping to non-roman characters in a similar location.

Unfortunately, this comes with a downside: the Keyboard API is only supported on Chrome. Which maybe isn’t a problem if the rest of your app only supports Chrome! But is still a problem if you want to support Safari<sup><a href="https://blog.duvallj.pw/posts/#user-content-fn-1" id="user-content-fnref-1" data-footnote-ref="" aria-describedby="footnote-label">1</a></sup>, or that other browser everyone forgets about (Firefox).
## What We Cross-Browser Plebians Have To Do

So, if we want to support all major browsers, and all major keyboard layouts, our keyboard shortcut definitions must:

- Use `key`
- Only use roman alphabet characters (A-Z) and arabic numbers (0-9)
- Use `.toLowerCase()` or `.toUpperCase()` to normalize case when checking what character was pressed
- Only allow `Shift` as a modifier with alphabet characters
- Never allow `Alt` as a modifier

As far as I can tell, there is no general-purpose shortcut library that gets this right. And maybe it’s the state of web search, but I can’t find anyone talking about this either. [This `lexical` PR](https://github.com/facebook/lexical/pull/6110) is about the closest I could get. I’ll probably just copy them, enforcing these rules within my own proprietary framework.

Everything is broken, and everything is fine 🔥