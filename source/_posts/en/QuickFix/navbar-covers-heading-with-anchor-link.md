---
title: Quick Fix - Navbar Covers Heading While Using Anchor Link
tags:
  - Quick Fix
  - CSS
keywords:
  - Quick Fix
  - CSS
  - Anchor Link
abbrlink: 2a73821d
date: 2021-04-19 21:00:00
---

Q: This is a common CSS issue. When using `<a href="#heading"></a>` to navigate around the article, the `Fixed` or `Sticky` will coverd the heading like this [CodePen](https://codepen.io/DriftKingTW/pen/abpaYYb)

<!-- more -->

A: There's hundred of ways to fix it, but this is probably the easiest way to do it (CSS only):

```css
:root {
  scroll-padding-top: 3rem;
}
```

{% colorquote info %}
You need to replace `3rem` with the height of your nav, play with it in  [CodePen](https://codepen.io/DriftKingTW/pen/abpaYYb)!
{% endcolorquote %}

> Quick Fix is a series of my solution for some random problem

# Reference

[3 More Life-Changing CSS Tips](https://www.youtube.com/watch?v=JSURzPQnkl0)

[5 More Must Know CSS Tricks That Almost Nobody Knows](https://www.youtube.com/watch?v=pKO1ktPQByk)

[MDN: scroll-padding-top](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-padding-top)