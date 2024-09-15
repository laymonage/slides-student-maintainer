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

I
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

  <template #2>

```py
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


---
layout: center
---

# One year later...

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

---
transition: slide-up
---

# A hit!

<img class="rounded-md h-sm mx-auto mb-4" src="/gsoc-acceptance.png" />

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

  </template>

</v-switch>


---
layout: center
---

# Should be "easy"...

<v-click>

...right?

</v-click>

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
