---
layout: post
title: "Ruby - Starting localhost server to serve files in local folder"
image: "clock"
---

Sometimes we want to start a server locally to serve a local folder with some assets (html, css, js) using,
using ruby it is easy, just run a:

```
ruby -run -ehttpd . -p8000
```

and it is done, you can access your local files using a `http://localhost:8000`.

If you want to make it accessible through internet you can use the [ngrok.com](https://ngrok.com) service.
So just make the combination of

```
ruby -run -ehttpd . -p8000 & ngrok http 8000
```

This way you will serve current folder in localhost:8000 and ngrok will make a tunnel through internet.
