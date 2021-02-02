---
hide: true
toc: true
comments: true
layout: post
title: Using Bootstrap to Style a Django App
description:  Using the crispy tag and Bootstrap 4 for nicer styling of Django apps.
image: images/logos/bootstrap.png
categories: [python, django, bootstrap]
---

[Django](https://www.djangoproject.com/) allows building simple (and complex) web apps quickly, using [Django Templates](https://docs.djangoproject.com/en/3.1/ref/templates/) for rendering.
By default, forms, buttons, and other elements are not styled and look quite ugly:

TODO

Using [Bootstrap](https://getbootstrap.com/) and [`django-crispy-forms`](https://django-crispy-forms.readthedocs.io/en/latest/index.html),
the rendered templates can easily be improved to look much nicer 
- without having to adjust styling manually. For example:

TODO

As an example, I extend my [Django "Hello World" App](https://github.com/stefanbschneider/django-hello-world)
(described in a [previous post](https://stefanbschneider.github.io/blog/django-heroku))
by adding a simple form and rendering it with Bootstrap 4 and `django-crispy-forms`.
All it does is asking for the user's name and then displaying `<username> says "Hello World!"` and a counter of how often the butten has been clicked.
Still, the small example illustrates how to use Bootstrap and `django-crispy-forms`.
Especially with many or large forms, `django-crispy-forms` becomes useful to reduce repetitive boilerplate.


# Installing Bootstrap and `django-crispy-forms`

## Adding Bootstrap to the Django App

## Installing `django-crispy-forms`

```
pip install django-crispy-forms
```

Add `crispy_forms` inside `settings.py`:

```python
INSTALLED_APPS = (
    ...
    'crispy_forms',
)
```

Enable the Bootstrap 4 template pack, adding the following line to `settings.py`:
```python
CRISPY_TEMPLATE_PACK = 'bootstrap4'
```

TODO: continue here

# Building a `crispy` Form

# What Next?
