---
title: Quick Fix - 網頁利用錨點連結導航時，導覽列遮住了標題怎麼辦
tags:
  - Quick Fix
  - CSS
keywords:
  - Quick Fix
  - CSS
  - Anchor Link
abbrlink: d74d57c8
date: 2021-04-19 21:00:00
---

Q：這是個常見的 CSS 問題，當利用錨點連結 `<a href="#heading"></a>` 跳到該標題時，如果是 `Fixed` 或 `Sticky` 的導覽列就會擋住標題，可以參考這個 [CodePen範例](https://codepen.io/DriftKingTW/pen/abpaYYb)

<!-- more -->

A：這問題的解法有相當多種，但這邊想推薦一個最簡單的純 CSS 解法：

```css
:root {
  scroll-padding-top: 3rem;
}
```

{% colorquote info %}
將上述 3em 的部分替換成自己的導覽列高度即可，可以在 [CodePen範例](https://codepen.io/DriftKingTW/pen/abpaYYb) 中玩玩看！
{% endcolorquote %}

> Quick Fix 是一些我自己平時遇到各種小問題的合輯，並提供自己的快速解法

# 參考

[3 More Life-Changing CSS Tips](https://www.youtube.com/watch?v=JSURzPQnkl0)

[5 More Must Know CSS Tricks That Almost Nobody Knows](https://www.youtube.com/watch?v=pKO1ktPQByk)

[MDN: scroll-padding-top](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-padding-top)