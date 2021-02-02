---
hide: true
toc: true
comments: true
layout: post
title: Using Bootstrap to Style a Django App
description:  Using the crispy tag and Bootstrap 5 for nicer styling of Django apps.
image: images/logos/bootstrap.png
categories: [python, django, bootstrap]
---

[Django](https://www.djangoproject.com/) allows building simple (and complex) web apps quickly, using [Django Templates](https://docs.djangoproject.com/en/3.1/ref/templates/) for rendering.
By default, forms, buttons, and other elements are not styled and look quite ugly:

Using [Bootstrap](https://getbootstrap.com/) and [`django-crispy-forms`](https://django-crispy-forms.readthedocs.io/en/latest/index.html),
the rendered templates can easily be improved to look much nicer 
- without having to adjust styling manually. For example:


As an example, I extend my [Django "Hello World" App](https://github.com/stefanbschneider/django-hello-world)
(described in a [previous post](https://stefanbschneider.github.io/blog/django-heroku))
by adding a simple form and rendering it with Bootstrap 5 and `crispy`.

TODO: add a simple "name" field, which is used to then print "Name XY says Hello World! + counter"
Don't save name to DB

# Installing Bootstrap and `django-crispy-forms`

## Adding Bootstrap to the Django App

## Installing `django-crispy-forms`

# Building a `crispy` Form

# What Next?
