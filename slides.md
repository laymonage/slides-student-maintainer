---
# try also 'default' to start simple
theme: the-unnamed
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
# some information about your slides, markdown enabled
title: "From student to maintainer: how mentorship programs sustain Django and Wagtail"
info: |
  ## From student to maintainer: how mentorship programs sustain Django and Wagtail
  Talk given at PyCon UK 2025 in Manchester, UK.
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

I'm Sage Abdullah, you can find me online @laymonage on GitHub, Fosstodon, and other places.

First of all, I was in two minds about doing this talk. I was afraid that this was going to be Sage talking about Sage on the stage and I couldn't gauge whether people would want to engage – but, I'll try.

I'm from Indonesia.

These days I work at Torchbox in the UK to maintain and develop Wagtail CMS, an open source content-management system built on top of Django.

Back in 2019 I did Google Summer of Code (or GSOC) with Django as a student, and this year, I was a mentor.
-->

---
transition: slide-up
---

# Django's tagline

<v-switch>
  <template #0><img class="rounded-xl" src="/djangoproject.com.png"/></template>
  <template #1><img class="rounded-xl" src="/djangoproject.com.modified.png"/></template>
</v-switch>

<!--
I think it's safe to say that we all know Django's tagline:

"The web framework for perfectionists with deadlines."

I don't know who came up with it, but it's catchy. I looked at the web archive and it's been there since the first snapshot in July 2005. I hope Frank is going to tell us about this in his talk on Wednesday.

Anyway, but to me, Django is the web framework

!!!

that changed my life.

And let me tell you why.
-->

---
transition: slide-up
---

# How it started...

<v-switch>
  <template #0><img class="rounded-md h-xs mx-auto mb-4" src="/ppw-slides.png" /></template>
</v-switch>

A web design & programming course at university

<!--
So, I first learned Django during my second year of uni back in 2018 as part of the web programming course.

It used to be PHP, but the previous year they updated the curriculum to teach Python for the basics, so it makes sense to use Django for the web.

And yes, it's a screenshot of a slide within a slide.
-->

---
transition: slide-up
---

# A small project

<v-switch>
  <template #0><img class="rounded-md w-md mx-auto mb-4" src="/ppw-website.png" /></template>

  <template #1><img class="rounded-md w-3xl mx-auto mb-4" src="/jsonfield-postgres-0.png" /></template>

  <template #2>

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

Which started out simple, with just a model or two, some views and templates. And we added more features throughout the course. By the way, I spun up this six year old project which used Django 2.1 and it still worked.

Anyway, we were given new assignments every week. At one point we learned about making AJAX requests from the browser to Google Books API. You can search for books and add them to your favorites.

To store the "favorite books" data, I could technically just have a model with the book ID and use a ForeignKey to the user. But at the time, I thought having one model instance for each book you saved is a bit overkill, especially when the books data came from a third-party service – in this case Google.

So what did I do instead?

!!!

I used – or I guess abused – sessions. You can use it as a dictionary and even use lists as the values. Perfect! I'll just throw the list of IDs to the session. Done!

Well, almost. Using sessions would mean that the data will be lost when the user logs out. How can I keep the data then?

I looked it up, and saw that Django has a

!!!

JSONField that lets you store dictionaries and lists! But – it was only available for Postgres. We did use Postgres on Heroku for the deployed website, but we also used SQLite for local development. JSONField was not an option for me.

So, I was like. Okay, how hard is it to have my own JSONField? If I can transform the dictionary into a string, I can save it to a TextField. Then, I just need some way to parse the string back into a dictionary. And that's...

!!!

what I did! I just used Python's built-in JSON library to serialize and deserialize the data, then I can save it in a TextField.

That being said, I know that if I were to do this today, I would just use a dedicated model with ForeignKey to store the saved books – but hey, I was just a student back then.
-->

---
layout: center
---

# A few months later...

<!--
After all that, I finished my web development course by the end of 2018.

A few months later...
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

I already knew about GSoC at that point, and since I've learned a bit of Django, I was curious about Django's ideas for GSoC that year.

Notice how it says **in no particular order**. Well it just so happened that the first item on the list was:

**A cross-database JSONField!**

That was... _basically_ the **exact** thing I hacked together in my project.

**I was _hooked_.**
-->

---
transition: slide-up
---

# Getting the feel of it



<v-switch>
  <template #0>

<img class="rounded-md w-xl mx-auto mb-4" src="/easy-pickings.png" />

  </template>


  <template #1>

<img v-click class="rounded-md h-sm mx-auto mb-4" src="/first-pr.png" />

  </template>

  <template #2>

<img class="rounded-md w-4xl mx-auto mb-4" src="/first-three-prs.png" />

  </template>
</v-switch>

<!--
The thing with GSoC is that if you wanted to get in, you have to realize there are other students like yourself. So, you need to convince the mentors that you are the right person for the job. You have to stand out among other applicants. It's just like job hunting.

So, before I started working on my proposal, I concocted a plan. I wanted to get at least one pull request (PR) to Django merged – but hopefully more than just one.

I started looking into the contribution guide, and I

!!!

watched the ticket tracker, waiting for a new ticket marked as "easy pickings" – which is kind of like a "good first issue" label on GitHub. They come and go so quickly.

!!!

Eventually I was able to land my first PR to Django – after some back and forth. As you can see there are 21 comments on the PR, for something that's essentially a one-line change, with three lines of test, and some documentation.

I learned a lot from this PR and it was a great experience. You get the general feeling and the common practices on how to make changes to the codebase.

This is important, since you need to demonstrate that you are willing to learn. You are able to follow the instructions, and you are **patient**. Which may sound trivial, but it is actually a big deal in open source. Speaking from experience, there are people who rush to get things done and want their PR merged, but they don't follow the instructions from the maintainers. That is not a good outlook.

!!!

Anyway, then I got two more PRs merged, one of which was for a ticket that I wrote myself. All of these are basically just one-liners, but they cover different aspects: implementation, tests, and documentation.

Now that I have three PRs, I got started with my proposal.
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
I wrote the goals, the approach, and the timeline for the project.

Mind you, at the time, I was still taking my databases course. So, I just started learning SQL and the basic stuff like foreign keys and joins and whatnot.

I only had vague ideas of how this project was going to work – and looking back at the proposal, I think it showed.

It's not a perfect proposal by any means, but I put it together in between my midterm exams, so I did what I could.

!!!

I posted it to the django-developers mailing list, got a few responses.

I made a few tweaks based on the feedback.

Then... I _waited_.

And then...

!!!
-->

---
transition: slide-up
---

# A hit!


<v-switch>
  <template #0>

<img class="rounded-md h-sm mx-auto mb-4" src="/gsoc-acceptance.png" />

  </template>

  <template #1>

<img class="rounded-md w-4xl mx-auto mb-4" src="/gsoc-summary.png" />

  </template>

</v-switch>

<!--
I got: this!

My proposal was accepted. It was like 4:30 in the morning, and I got chills from reading this email.

It was happening!!!

!!!

So, over the course of the summer, I worked on the project. I was slightly intimidated when I saw that I had not one, not two, but four people mentoring me. It felt like the expectation was quite high.

But thankfully, I didn't have to start from scratch.
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

- We had it in Django-MySQL !!!
- We had one for Oracle, and !!!
- We also had django-jsonfallback that combined the built-in Postgres and django-mysql's JSONFields before falling back to a TextField on others.

!!!

and many, many other packages... some of which even predated the built-in Postgres JSONField.
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

<!--
On June 9th 2019, which happened to be my 20th birthday, I submitted this initial PR to Django.

You can see how many people reviewed the PR. On the right side there.

!!!

I have blurred the number of comments on the PR. Would anybody like to take a guess on how many comments we have here?

Fifty? A hundred? Two hundred?

!!!

There were 321 comments. This was just the initial PR that I kept on iterating.

So at one point, we decided to close it and open a new one to start fresh.

!!!

This is the final PR, opened months later in January 2020. GSoC already ended in August. It wasn't merged until May 8th 2020, so when COVID really kicked in.
-->

---
transition: slide-up
---

# Months later...

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/jsonfield-releasenotes.png" />

<!--
Then a few months later, in August of 2020, Django 3.1 was finally released – which included the new cross-database JSONField that I worked on.

So it took a little over a year from when I submitted the initial PR to the day it was released.

But hey, we made it!

Raise your hands if you've used `JSONField`. Thanks, glad you find it useful!
-->

---
transition: slide-up
---

# DjangoChat

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/djangochat.png" />

<!--
Then I was invited by Carlton and Will Vincent to the DjangoChat podcast. If you didn't know about DjangoChat already – give it a listen.

This was my first ever podcast. And still my only one so far. (hint hint?)

I was very nervous. I could've done better, but it's out there now, so if you do listen to it, please keep that in mind.
-->

---
transition: slide-up
---

# DjangoCon Europe 2020

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/djceu-2020.png" />

<!--
And then a few months later, I got the opportunity to give a talk about the JSONField implementation at DjangoCon Europe 2020. It was supposed to be in-person in Portugal, but COVID happened, so it was done virtually.

This was my first ever talk at a conference.
-->

---
transition: slide-up
---

# A job listing

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/torchbox-listing.png" />

<!--
About a year later, I was browsing Django's Discord server, and I saw a job listing. I went to the company's website to find out more, and I saw...

A job listing for a Wagtail Developer.

The job is to work on Wagtail the CMS. And doing open source as a full-time job was my dream.

I looked at the requirements, and I think I pretty much ticked all the boxes, so I sent my application.

Then I did a couple interviews and a coding task. I was interviewed by Tom Dyson, the CTO, and he knew about my JSONField stuff. A few days later, I got the job!

They wanted me to move to the UK as soon as possible, but it was such a surprise for me and my family. So I asked to work remotely for a few months while we sort out my visa and stuff.
-->

---
transition: slide-up
---

# First thing on the job

<img alt="" v-click class="rounded-md h-sm mx-auto mb-4" src="/wagtailspace.png" />

<!--
And do you know what one of the first things I did on the job was?

Well, Wagtail had their own version of JSONField that used the TextField approach. Around that time, it happened that Wagtail was about to bump its minimum Django version to 3.2, which includes the new JSONField.

!!!

So naturally, I was tasked to migrate all these existing text-based "JSONField"s into real JSONFields.

Then I gave a talk about it at Wagtail Space US, which is Wagtail's own conference. But I did it virtually because I was still in Indonesia.

And then a few months later...
-->

---
transition: slide-up
---

# Moved to 🇬🇧

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/bristol.jpg" />

<!--
I moved to the UK!

This is me on the Clifton Suspension Bridge in Bristol, during my first week in the UK.

Then, just a few weeks later, I went to DjangoCon Europe 2022 in Porto, in person this time. Can you guess the people I met there?
-->

---
transition: slide-up
---

# Don't* meet your heroes

<img alt="" class="rounded-md w-md mx-auto mb-4" src="/djceu-2021.jpg" />

*Unless they're from the Django community!

<!--
My GSoC mentors!

They are some of the absolute legends in the Django community and I was so happy to finally meet them.

You know what they say – don't meet your heroes, unless they're from the Django community. Cause they are awesome!
-->

---
transition: slide-up
layout: center
---

# The rest is history!

<!--
That was two years ago, and the rest – is history.

A lot of other things have happened since, but what I've shown you so far is enough to give you the idea.

But that's not to mention the effect of all this on the people around me as well. My family's financial situation pretty much had a complete turnaround. We were struggling to keep our feet up, and COVID made it even worse. But now it almost seems like it was just a bad dream. All thanks to Django.

It started with me learning Django, and the rest didn't happen overnight. It's a snowball that keeps on rolling, and I have been very lucky to have all these opportunities. It's a privilege.

And so, I'd like to thank the Django community for making it all possible.
-->

---
transition: slide-up
layout: center
---

# How do we keep this going?

<!--
I want Django to have such an effect on many many other people. For me to just be one example. One example out of many other people from all around the world.

So how do we keep this going?
-->

---
transition: none
---

# Google Summer of Code

<v-switch>

  <template #0>
    <img alt="" class="rounded-md h-sm mx-auto mb-4" src="/gsoc-2024.png" />
  </template>
  <template #1>
    <img alt="" class="rounded-md h-sm mx-auto mb-4" src="/gsoc-2024-pr.png" />
  </template>
  <template #2>
    <img alt="" class="rounded-md h-sm mx-auto mb-4" src="/gsoc-2025.png" />
  </template>

</v-switch>

<!--
This year, Django still participated in GSoC. We had four projects, compared to two when I did mine in 2019. Different projects with different mentors and different areas of Django to improve.

And now, you no longer have to be a university student to join.

Also, anybody in the community can take part in other ways. You can suggest project ideas. Or, if you feel like it, you can also be a mentor.

!!!

This year, I mentored a project that adds support for updating JSONFields on the database level. It's like an SQL – sorry, a sequel – to my project. It has come full circle.

Five years later, it's very interesting to see how much of the code has changed, and how much has stayed the same.
-->

---
transition: none
---

# Outreachy

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/outreachy.png" />

<!--
There is also Outreachy. It's a similar program to GSoC, but it's aimed towards supporting diversity.

Wagtail has participated in Outreachy a few times, and I can tell you that our interns are some of the coolest people I've ever worked with.

While Django has not participated in Outreachy so far, I think it's something we should look into in the future.
-->

---
transition: slide-up
---

# Djangonaut Space

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/djangonautspace.png" />

<!--
And we also have our own Djangonaut Space, a mentorship program for people who want to get into contributing to Django.

I think this is one of, if not the best initiative for Django ever. Maybe even in open source.

If you're interested, check out djangonaut.space. There's a panel discussion on Wednesday about Django mentorship, so we can hear more about it. There's also a talk about Djangonaut Space by Dawn and Rachell from last year's DjangoCon US and I highly recommend checking it out.
-->

---
transition: slide-up
---

# Signs of successful projects

- Idea
- Motivation
- People

<!--
Those were some of the programs we can take part in, to bring new contributors to our community.

And there are things we can do –as a community– to keep Django alive.
-->

---
transition: slide-up
---

# Making the most of it

- Prepare project ideas
- Well-documented contribution guide
- Good first issues / easy pickings
- Mentoring (mostly reviewing) capacity

---
transition: slide-up
layout: center
---

# Making it sustainable

<v-click>

There is no single answer

</v-click>

---
layout: center
---

# Thank you!

[slides.laymonage.com/student-maintainer](https://slides.laymonage.com/student-maintainer)

<!--
Thank you!
-->
