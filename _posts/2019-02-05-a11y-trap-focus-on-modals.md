---
layout: post
title: "Accessibility - Trapping focus on dialog"
image: "capybaha"
---

One little tip when making your website accessible is taking care of the focus order,
for people who can't use mouse and any other pointing device is pretty frustrating when
they trigger a dialog when the focus got lost in the page, or inside dialog they press Tab
in the last focusable element and the focus goes to the page.

To solve this we can implement a simple trick, a trap focus function.

```javascript
var trapFocus = function (containerSelector) {
  var focusableElements = document.querySelector(containerSelector).querySelectorAll('button, a, input')

  var first = focusableElements[0];
  var second = focusableElements[1];
  var last = focusableElements[focusableElements.length - 1];

  first.onkeydown = function (e) {
    if (e.keyCode == 9 && e.shiftKey) {
      e.preventDefault();
      last.focus();
    }
  };

  last.onkeydown = function (e) {
    if (e.keyCode == 9 && !e.shiftKey) {
      e.preventDefault();
      first.focus();
    }
  };

  // As the first element generally is the close button we focus on the second instead.
  second.focus();
}
```

Then simply call `trapFocus('selector')` after openning the dialog and voila.
