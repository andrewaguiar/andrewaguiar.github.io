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

So simply call `trapFocus('selector')` after openning the dialog and voila.

Complete code:

```html
<style>
.dialog {
  position: relative;
  display: flex;
  flex-flow: column;
  padding: 3em 2em 2em 2em;
  background-color: #efefef;
  border: 1px solid #333;
}
.dialog .close {
  width: 32px;
  height: 32px;
  background-color: transparent;
  border: none;
  padding: 0;
  font-size: 2em;
  position: absolute;
  top: 30px;
  right: 30px;
}
.dialog input {
  padding: 10px;
  font-size: 18px;
}
.dialog .submit {
  margin-top: 1em;
  padding: 12px;
  font-size: 20px;
  background-color: #ccc;
  border: 1px solid #333;
}
</style>

<dialog class="dialog">
  <button class="close">&times;</button>

  <label>Name</label>
  <input />

  <label>Email</label>
  <input />

  <label>Age</label>
  <input />

  <button type="submit" class="submit">Save</button>
</dialog>

<button>Open</button>
```
