---
layout: post
title: "Ruby - How to store and load ENV variables in dev "
image: "stones"
---

It is very common to use an env variable in ours projects, specialy when we are using Docker or Heroku. In this case several configurations will be done using ENV vars.

Also we want to use that without so much work in our development enviroment.

We could create some script in shell setting all this variables to us and use this script to start our application

```
export FACEBOOK_APP_ID="<FACEBOOK_APP_ID>"
export FACEBOOK_APP_SECRET="<FACEBOOK_APP_SECRET>"
export GOOGLE_APP_ID="<GOOGLE_APP_ID>"
export GOOGLE_APP_SECRET="<GOOGLE_APP_SECRET>"

bundle exec rails server
```

Or you can use the [dotenv](https://github.com/bkeepers/dotenv), a very simple and cool gem that does this work for you.

To use it follow the instructions in [https://github.com/bkeepers/dotenv](https://github.com/bkeepers/dotenv), create you .env with all env variables needed in development and just execute your project using ``bundle``.

The .env file would be very simple

```ruby
FACEBOOK_APP_ID="<FACEBOOK_APP_ID>"
FACEBOOK_APP_SECRET="<FACEBOOK_APP_SECRET>"
GOOGLE_APP_ID="<GOOGLE_APP_ID>"
GOOGLE_APP_SECRET="<GOOGLE_APP_SECRET>"
```

Just dont forget to add ``.env`` in your ``.gitignore`` to avoid commit credentials or other critical informations in your git repository. 

```
echo ".env" >> .gitignore
```

You should only add a ``.env.example`` without any information as an example of what should be configured.

## References
  - Dotenv: [dotenv](https://github.com/bkeepers/dotenv).
