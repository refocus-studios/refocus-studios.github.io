---
layout: post
title:  "Phase II - RS Collaboration Development"
date:   2014-01-01 20:54:44
categories: 
---

### ToDo

- Add Bootstrap
- Push to Heroku

#### Bootstrap

[Physically added Bootstrap files to the project.](http://getbootstrap.com/)


[Added alerts that work with Bootstrap](https://gist.github.com/ariperez83/8208993)

{% gist 8208993 %}
 
* Added a Navbar

#### Heroku

* Set the following settings in the '/config/environments/production' file so that Bootstrap will work on Heroku.

```ruby
config.cache_classes = true
config.serve_static_assets = true
config.assets.compile = true
config.assets.digest = true
```

* Set the Heroku Google OmniAuth2 variables

```bash
rake figaro:heroku
```
* Manually add users using the Heroku Rails Console

```
heroku run rails c
```

```
User.create( email: "ari@refocus-studios.com", password: Devise.friendly_token[0,20])
```

