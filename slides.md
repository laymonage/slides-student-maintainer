---
# try also 'default' to start simple
theme: the-unnamed
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
# some information about your slides, markdown enabled
title: "Django: the web framework that changed my life"
info: |
  ## Django: the web framework that changed my life
  Talk given at DjangoCon Europe 2024 in Vigo, Spain.
# apply any unocss classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# https://sli.dev/guide/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations#slide-transitions
transition: slide-up
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true
---

# Halo!

<!--
Halo!

That's not halo, it's _halo_. That's how you say "hello" in Indonesian.
-->

---
transition: fade-out
---

# ~~About me~~ Outline

- Sage Abdullah / `@laymonage`
- Computer Science, Universitas Indonesia
- Wagtail Developer at Torchbox
- Google Summer of Code with Django
  - 2019: student
  - 2024: mentor

<style>
  li {
    font-size: 1.75rem;
    line-spacing: 2;
  }
  li:not(ul ul li) {
    margin-bottom: 0.5rem;
  }
</style>

<!--
Thank you everybody for coming, and thanks to all the organizers for such a wonderful event. I'm so grateful to be here again.

I'm Sage Abdullah, you can find me online @laymonage on GitHub, Fosstodon, _even_ Steam.

First of all, I was in two minds about doing this talk, because usually I do more technical kind of talks. I was afraid that this was going to be Sage talking about himself on the stage and I couldn't gauge whether people would want to engage – but, I'll try.

This is a slide about me, but I think it works just as well as an outline for this talk.

I'm from Indonesia.

Now I work at Torchbox in the UK to maintain and develop Wagtail CMS, an open source content-management system built on top of Django.

Back in 2019 I did Google Summer of Code (or GSOC) with Django as student, and this year, I was a mentor.
-->

---
transition: slide-up
---

# The tagline

<v-switch>
  <template #0><img class="rounded-xl" src="/djangoproject.com.png"/></template>
  <template #1><img class="rounded-xl" src="/djangoproject.com.modified.png"/></template>
</v-switch>

<!--
I think it's safe to say that we all know Django's tagline:

"The web framework for perfectionists with deadlines."

Which, as far as I know, looking at the web archive, it's been there since the start in July 2005.

But to me, Django is the web framework that changed my life.

And let me tell you why.
-->

---
transition: slide-up
---

# How it started...

<v-switch>
  <template #0><img class="rounded-md h-xs mx-auto mb-4" src="/ppw-scele.png" /></template>
  <template #2><img class="rounded-md h-xs mx-auto mb-4" src="/ppw-slides.png" /></template>
</v-switch>

<v-click at="1"> A web design & programming course at university </v-click>

<!--
So I first learned Django during my second year of uni back in 2018 as part of a web design and programming course.

!!!

This is a screenshot of the online learning platform where the resources were posted.

!!!

The course used Django to teach server-side web programming. They previously used PHP but they updated the curriculum to use Django the previous year, along with the switch to Python for the basic programming course.
-->

---
transition: slide-up
---

# A small project

<v-switch>
  <template #0><img class="rounded-md w-md mx-auto mb-4" src="/ppw-website.png" /></template>

  <template #1>

```py
def my_books(request):
    return JsonResponse({"books": request.session['books']})

def favorite(request):
    book_id = request.POST.get('book_id', '')
    if not book_id:
        return JsonResponse({'success': False})
    if book_id not in request.session['books']:
        request.session['books'].append(book_id)
        request.session.modified = True
    return JsonResponse({'success': True})

def unfavorite(request):
    book_id = request.POST.get('book_id', '')
    if not book_id:
        return JsonResponse({'success': False})
    if book_id in request.session['books']:
        request.session['books'].remove(book_id)
        request.session.modified = True
    return JsonResponse({'success': True})
```

  </template>

  <template #2><img class="rounded-md w-3xl mx-auto mb-4" src="/jsonfield-postgres-0.png" /></template>

  <template #3>

```py
import json

class BooksList(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    books = models.TextField(default='{"books": []}')

...

@receiver(user_logged_in)
def load_books_set(sender, user, request, **kwargs):
    request.session['books'] = json.loads(user.bookslist.books)['books']


@receiver(user_logged_out)
def save_books_set(sender, user, request, **kwargs):
    user.bookslist.books = json.dumps(
        {"books": [book for book in request.session['books']]}
    )
    user.save()

```

  </template>
</v-switch>

<!--
As part of the course we were tasked to build our own website.

Which started out simple, with just a model or two, some views and templates, and we added more features throughout the course. By the way, I spun up this six year old project which used Django 2.1 and it still worked.

Anyway, we were given new assignments every week. At one point we learned about making AJAX requests from the browser to Google Books API. You can search for books and add them to your favorites.

To store the "favorite books" data, I could technically just have a model with the book ID and use a ForeignKey to the user. But at the time, I thought having one model instance for each book you saved is a bit overkill, especially when the books data came from a third-party service – in this case Google.

So what did I do?

!!!

I used – or I guess abused – sessions. You can use it as a dictionary and even use lists as the values. Perfect! I'll just throw the list of IDs to the session. Done!

Well, almost. Using sessions would mean that the data will be lost when the user logs out. How can I keep the data then?

!!!

I looked it up, and saw that Django has a JSONField for this purpose, but it was only available for Postgres. We did use Postgres on Heroku for the deployed website, but we also used SQLite for local development. JSONField was not an option for me.

So, I was like. Okay, how hard is it to have my own JSONField? If I can transform the dictionary into a string, I can save it to a TextField. Then, I just need some way to parse it back into a dictionary. And that's... what I did!

That being said, please don't try this at home!
-->

---
layout: center
---

# One year later...

<!--
And then one year later...
-->

---
transition: slide-up
---

<v-switch>
  <template #0>

# An opportunity

<img class="rounded-md w-sm mx-auto mb-4" src="/gsoc-website.png" />

  </template>


  <template #1>

# An opportunity

<img class="rounded-md w-sm mx-auto mb-4" src="/gsoc-django.1.png" />

  </template>

  <template #2>

# A coincidence?

<img class="rounded-md w-sm mx-auto mb-4" src="/gsoc-django.2.png" />

  </template>
</v-switch>

<!--
I saw an opportunity.

This is the Google Summer of Code website back in 2019.

If you're not familiar with Google Summer of Code (GSoC), it's an annual global program held by Google, where university students can work with an open-source organization on a 3-month project in the summer. And, they get paid by Google!

Organizations like Django can list a few project ideas and apply to the program, and Google will give a number of project slots. Then, students can submit a proposal for the project they want to work on, to be reviewed and accepted by someone from the organization.

!!!

Django has participated in GSoC every year since 2016. And for each year, we put a list of project ideas on the wiki.

I finished my web development course by the end of 2018. In the next term, so early 2019, I took a database course.

I already knew about GSoC at that point, and since I've learned a bit of Django, I was curious about Django's ideas for GSoC.

Notice how it says **in no particular order**. Well it just so happened that the first item on the list was:

**A cross-DB JSONField!**

That was... _basically_ the **exact** thing I hacked together in my project.

**I was _hooked_.**
-->

---
transition: none
---

# A shot


<v-switch>
  <template #0>

<img class="rounded-md h-sm mx-auto mb-4" src="/gsoc-proposal.png" />

  </template>

  <template #1>

<img class="rounded-md h-sm mx-auto mb-4" src="/gsoc-mailist.png" />

  </template>

</v-switch>

<!--
I put together my proposal. I wrote the goals, the approach, and the timeline for the project.

Mind you, at the time, I was still taking my databases course. So, I just started learning SQL and the basic stuff like foreign keys and joins and whatnot.

I only had vague ideas of how this project was going to work – and looking back at the proposal, I think it showed.

It's not a perfect proposal by any means, but I put it together in between my midterm exams, so I did what I could.

!!!

I posted it to the django-developers mailing list, got a few responses – see if you can spot some familiar names there.

I made a few tweaks based on the feedback.

Then... I _waited_.

And then...

!!!
-->

---
transition: slide-up
---

# A hit!

<img class="rounded-md h-sm mx-auto mb-4" src="/gsoc-acceptance.png" />

<!--
I got this!

My proposal was accepted. It was like 4:30 in the morning, and I got chills from reading this email.

It was happening!!!

!!!

So, over the course of the summer, I worked on the project. I didn't start from scratch, mind you.
-->

---
transition: slide-up
---

# Shoulders of giants


<v-switch>
  <template #0>

<img alt="Built-in JSONField in Django, for PostgreSQL only" class="rounded-md h-xs mx-auto mb-4" src="/jsonfield-postgres.png" />

Built-in `JSONField` in `django.contrib.postgres`

  </template>

  <template #1>

<img alt="JSONField in the django-mysql package, for MySQL only" class="rounded-md h-xs mx-auto mb-4" src="/jsonfield-mysql.png" />

`JSONField` in the `django-mysql` package by Adam Johnson

  </template>

  <template #2>

<img alt="The oracle-json-field package, for Oracle only" class="rounded-md h-xs mx-auto mb-4" src="/jsonfield-oracle.png" />

`JSONField` for Oracle Database by Vackar Afzal @ Exscientia

  </template>

  <template #3>

<img alt="The django-jsonfallback package, uses contrib.postgres JSONField for PostgreSQL, django-mysql for MySQL, and falls back to TextField-based implementation for others" class="rounded-md h-xs mx-auto mb-4" src="/jsonfield-fallback.png" />

`django-jsonfallback` for "all" databases by Raphael Michel

  </template>

  <template #4>

<div class="flex items-center justify-center mt-32">

  ## And many others...

</div>

<div class="allthelinks">

  [jsonfield](https://github.com/rpkilby/jsonfield) |
  [django-annoying](https://github.com/skorokithakis/django-annoying#jsonfield) |
  [django-json-field](https://github.com/derek-schaefer/django-json-field) |
  [django-jsonfield](https://github.com/adamchainz/django-jsonfield) |
  [django-jsonfield-compat](https://github.com/kbussell/django-jsonfield-compat) |
  [dj-jsonfield](https://github.com/ratson/dj-jsonfield) |
  [vlk-django-jsonfield](https://github.com/vialink/vlk-django-jsonfield) |
  [linaro-django-jsonfield](https://pypi.org/project/linaro-django-jsonfield) |
  [jsonfield2](https://github.com/rpkilby/jsonfield2) |
  [django-jsonfield2](https://github.com/DarioGT/django-jsonfield2) |
  [django-softmachine](https://github.com/certae/django-softmachine) |
  [django-simple-jsonfield](https://github.com/devkral/django-simple-jsonfield) |
  [easy_jsonfield](https://github.com/claydodo/easy_jsonfield) |
  [django-jsonbfield](https://pypi.org/project/django-jsonbfield)

</div>

  </template>

  <style>
    .allthelinks p {
      justify-content:center;
      display: flex;
      flex-wrap: wrap;
      gap: 0.75rem;
    }
  </style>

</v-switch>

<!--
I was standing on the shoulders of giants.

Obviously, we already had the Postgres JSONField at the time, which worked wonders. But we also had external packages that implemented JSONField for other database backends too.

- We had it in Django-MySQL, and
- We had an oracle-json-field package for Oracle, and
- We also have django-jsonfallback that would use the built-in Postgres and django-mysql's JSONFields before falling back to a TextField on others.

and many, many other packages... some of which predated the built-in Postgres JSONField.
-->

---
layout: center
---

# Should be "easy"...

<v-click>

...right?

</v-click>

<!--
So... it should be "easy"...

!!!

Right?
-->

---
transition: slide-up
---

# The work

<v-switch>
  <template #0>

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/gsoc-pr.1.png" />

  </template>

  <template #1>

<img alt="" class="rounded-md w-3xl mx-auto mb-4" src="/gsoc-pr.2.png" />

  </template>

  <template #2>

<img alt="" class="rounded-md w-2xl mx-auto mb-4" src="/gsoc-pr.3.png" />

  </template>

  <template #3>

<img alt="" class="rounded-md w-3xl mx-auto mb-4" src="/gsoc-pr.4.png" />

  </template>

</v-switch>

---
transition: slide-up
---

# Months later...

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/jsonfield-releasenotes.png" />

---
transition: slide-up
---

# DjangoCon Europe 2020

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/djceu-2020.png" />

---
transition: slide-up
---

# Internship

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/internship.png" />

---
transition: slide-up
---

# Bachelor thesis

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/graduation.png" />

---
transition: slide-up
---

# A job listing

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/torchbox-listing.png" />

---
transition: slide-up
---

# First thing on the job

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/wagtailspace.png" />

---
transition: slide-up
---

# Moved to 🇬🇧

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/bristol.jpg" />

---
transition: slide-up
---

# Don't* meet your heroes

<img alt="" class="rounded-md w-md mx-auto mb-4" src="/djceu-2021.jpg" />

*Unless they're from the Django community!

---
transition: slide-up
layout: center
---

# The rest is history!

---
transition: slide-up
---

# Community

None of this would've been possible without the awesome Django community.

<v-clicks>

- Django Fellows
- django-developers
- django-users
- Django package maintainers
- Django Software Foundation members
- Django event organizers
- Django event attendees
- You!

</v-clicks>

---
transition: slide-up
layout: center
---

# How do we keep this going?


---
transition: none
---

# Google Summer of Code

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/gsoc-2024.png" />


---
transition: none
---

# Outreachy

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/outreachy.jpg" />

---
transition: slide-up
---

# Djangonaut Space

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/djangonautspace.png" />

---
transition: slide-up
layout: center
---

# Keeping Django alive

---
transition: none
---

# Make PRs

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/django-contributors.png" />

---
transition: none
---

# Review PRs

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/django-review.png" />

---
transition: none
---

# Create tickets

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/django-trac.png" />

---
transition: none
---

# Test against Django's main

<img alt="" class="rounded-md w-3xl mx-auto mb-4" src="/django-cimain.png" />

---
transition: none
---

# Publish packages

<img alt="" class="rounded-md w-lg mx-auto mb-4" src="/django-packages.png" />

---
transition: none
---

# Donate

<img alt="" class="rounded-md w-lg mx-auto mb-4" src="/django-donate.png" />

---
transition: none
---

# Donate

<img alt="" class="rounded-md w-4xl mx-auto mb-4" src="/django-sponsors.png" />

---
transition: none
---

# Participate in events

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/django-events.png" />

---
transition: none
---

# Participate in events

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/djceu-coc.jpg" />

---
transition: none
---

# Join meetups

<img alt="" class="rounded-md w-4xl mx-auto mb-4" src="/django-meetup.png" />

---
transition: slide-up
---

# Use it!

<img alt="" class="rounded-md w-4xl mx-auto mb-4" src="/django-use.png" />



---
layout: center
---

# Thank you!

[slides.laymonage.com/django-life](https://slides.laymonage.com/django-life)
