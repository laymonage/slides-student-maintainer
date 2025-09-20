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
colorSchema: light
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

# From student to maintainer

<h2 class="pl-0!">
  How mentorship programs sustain
  <img class="w-22 inline-block" alt="Django logo" src="/django-logo.svg"/>
  and
  <img class="w-32 inline-block mb-2" alt="Wagtail logo" src="/wagtail-logo.svg"/>
</h2>

<div class="flex flex-col items-center mt-8">

  <img class="w-32" alt="QR code to presentation slides" src="/qr.png" />
  <a href="https://sage.id/pyconuk25">sage.id/pyconuk25</a>

</div>

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


<div class="flex flex-col gap-8 justify-between w-64 absolute top-42 right-12">
  <img alt="Wagtail logo" src="/wagtail-logo.svg" />
  <img alt="Torchbox logo" src="/tbx-logo-black.svg" />
</div>

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
Thank you everybody for coming, and thanks to all the organizers for such a wonderful event. It's not my first time speaking at a conference, but it is my first time at PyCon UK and my first PyCon as well, so I'm very excited to be here.

I'm Sage Abdullah, you can find me online @laymonage on GitHub, Mastodon, Bluesky, and other places.

I'm from Indonesia.

These days I work at Torchbox in the UK to maintain and develop Wagtail CMS, an open source content-management system built on top of Django. So, thank you to Torchbox for paying me to be here. If you want to know more about us or Wagtail, please feel free to talk to me later. I will also be around for sprints tomorrow morning.

Back in 2019 I did Google Summer of Code (or GSOC) with Django, as a student, and last year, I was a mentor.
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
If you go to the djangoproject.com website, you'll see Django's tagline:

"The web framework for perfectionists with deadlines."

I don't know who came up with it, but it's catchy. I looked at the web archive and it's been there since the first snapshot in July 2005.

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

They used to teach PHP, but in 2017 they updated the curriculum to teach Python for the basics, so it makes sense to use Django for the web.

And yes, I just put a screenshot of a slide within my slides.
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

At one point we learned about making AJAX requests from the browser to Google Books API. The assignment was to add a feature that lets you search for books and show the results.

There were bonus points if you could save your favourite books. I didn't want to bother creating the models and whatnot just to get the bonus points, especially since the data came from a third-party service.

I was hoping I could just save the list of books somewhere, like saving a Python object.

I found out that Django has a

!!!

JSONField that lets you store dictionaries and lists! But – it was only available for Postgres. We did use Postgres on Heroku for the deployed website, but we also used SQLite for local development. JSONField was not an option for me.

So, I was like. Okay, how hard is it to have my own JSONField? If I can transform the dictionary into a string, I can save it to a TextField. Then, I just need some way to parse the string back into a dictionary. And that's...

!!!

what I did! I just used Python's built-in JSON library to serialize and deserialize the data, then I can save it in a TextField.

That being said, I know that if I were to do this today, I would just use a dedicated model with ForeignKeys to store the saved books – but hey, I was just a student looking to score... some bonus scores.
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

Notice how it says at the bottom, **in no particular order**. Well it just so happened that the first item on the list was:

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

I learned a lot just from this one PR and it was a great experience. You get the general feeling and the common practices on how to make changes to the codebase.

This is important, since you need to demonstrate that you are willing to learn. You are able to follow the instructions, and you are **patient**. Which may sound trivial, but it is actually a big deal in open source. Speaking from experience, there are people who rush to get things done and want their PR merged, but they don't follow the instructions from the maintainers.

Recently, I've even been seeing some pull requests that are clearly completely AI-generated without demonstrating that a human understood what the issue is. That is not a good look.

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

I posted it to the django-developers mailing list, (yes, we still used a mailing list back then), got a few responses.

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

So, over the course of the summer, I worked on the project. So, usually GSoC projects have like one or two mentors. I was slightly intimidated when I saw that I had not one, not two, but four people mentoring me. It felt like the expectation was quite high.

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

<div class="flex items-center justify-center mt-32">

  ## And other packages...

</div>

<div class="allthelinks">

  [django-mysql](https://github.com/adamchainz/django-mysql) |
  [oracle-json-field](https://github.com/Exscientia/oracle-json-field) |
  [django-jsonfallback](https://github.com/raphaelm/django-jsonfallback) |
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

!!!

- We had it in Django-MySQL
- We had one for Oracle, and
- and many, many other packages... some of which even predated the built-in Postgres JSONField and unfortunately unmaintained.
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

<img alt="PR #11452 to django/django, Fixed #12990 -- Added JSONField model field." class="rounded-md h-sm mx-auto mb-4" src="/gsoc-pr.1.png" />

  </template>

  <template #1>

<img alt="A closer look of PR #11452 to django/django, showing that the PR was closed and a new PR #12392 was opened. The comments count is blurred." class="rounded-md w-3xl mx-auto mb-4" src="/gsoc-pr.2.png" />

  </template>

  <template #2>

<img alt="Conversation: 321 (comments)" class="rounded-md w-2xl mx-auto mb-4" src="/gsoc-pr.3.png" />

  </template>

  <template #3>

<img alt="PR #12392 merged to django/django on May 8 2020 with 84 comments" class="rounded-md w-3xl mx-auto mb-4" src="/gsoc-pr.4.png" />

  </template>

</v-switch>

<!--
On June 9th 2019, which happened to be my 20th birthday, I submitted this initial PR to Django. (So that's what, more than six years ago now... where did all the time go? Anyway...)

You can see how many people reviewed the PR. On the right side there.

!!!

I have blurred the number of comments on the PR. Would anybody like to take a guess on how many comments we have here?

Fifty? A hundred? Two hundred?

!!!

There were 321 comments. This was just the initial PR that I kept on iterating.

So at one point, we decided to close it and open a new one to start fresh.

!!!

This is the final PR, opened months later in January 2020. GSoC already ended in August. It was not merged until May 8th 2020, so when COVID really kicked in.
-->

---
transition: slide-up
---

# Months later...

<img alt="What's new in Django 3.1: JSONField for all supported database backends" class="rounded-md h-sm mx-auto mb-4" src="/jsonfield-releasenotes.png" />

<!--
Then a few months later, in August of 2020, Django 3.1 was finally released – which included the new cross-database JSONField that I worked on.

So it took a little over a year from when I submitted the initial PR to the day it was released.

But hey, we made it!

Raise your hands if you use Django and you use `JSONField`.

Why on earth would you use it? Why don't you use actual models with real tables? Or, why not just use a no-SQL database? I'm joking, thank you, glad you find it useful!
-->

---
transition: slide-up
---

# DjangoChat

<img alt="DjangoChat Podcast: Google Summer of Code – Sage Abdullah" class="rounded-md h-sm mx-auto mb-4" src="/djangochat.png" />

<!--
Then I was invited by Carlton and Will Vincent to the DjangoChat podcast. If you didn't know about DjangoChat already – give it a listen.

This was my first ever podcast. And still my only one so far.

I was very nervous. I could've done better, but ehh, it's out there now, so if you do listen to it, please keep that in mind.
-->

---
transition: slide-up
---

# DjangoCon Europe 2020

<img alt="DjangoCon Europe 2020 talk: Implementing a Cross-DB JSONField" class="rounded-md h-sm mx-auto mb-4" src="/djceu-2020.png" />

<!--
And then a few months later, I got the opportunity to give a talk about the JSONField implementation at DjangoCon Europe 2020. It was supposed to be in-person in Portugal, but COVID happened, so it was done virtually.

This was my first ever talk at a conference.
-->

---
transition: slide-up
---

# A job listing

<v-switch>

  <template #0>

  <img alt="A job listing from Thibaud for a Junior Developer at Torchbox UK, remote allowed" class="rounded-md w-xl mx-auto mb-4" src="/discord-1.png" />

  </template>

  <template #1>

  <img alt="A job listing on Torchbox's website for a Wagtail Developer" class="rounded-md h-sm mx-auto mb-4" src="/torchbox-listing.png" />

  </template>

</v-switch>

<!--
A little over a year later, I graduated from university and was working at a startup in Indonesia.

One day, I was browsing Django's Discord server, and I saw a job listing for a Junior Developer by someone named Thibaud whom I didn't know at the time. But the job was remote-friendly, so I went to the company's website to find out more, and I saw...

!!!

A different job listing for a Wagtail Developer.

The job is to work on Wagtail the CMS. And doing open source as a full-time job was my dream.

I looked at the requirements, and I think I pretty much ticked all the boxes, so I sent my application.

Then I did a couple interviews and a coding task. I was interviewed by Tom Dyson, the CTO at the time, and he knew about my JSONField stuff. A few days later, I got the job!

They wanted me to move to the UK as soon as possible, but it was such a surprise for me and my family. So I asked to work remotely for a few months while we sort out my visa and stuff.
-->

---
transition: slide-up
---

# First thing on the job

<img alt="Wagtail Space US 2022 talk: Bringing JSONField into Wagtail core" v-click class="rounded-md h-sm mx-auto mb-4" src="/wagtailspace.png" />

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

<img alt="Sage jumping on the Clifton Suspension Bridge in Bristol" class="rounded-md h-sm mx-auto mb-4" src="/bristol.jpg" />

<!--
I moved to the UK!

This is me on the Clifton Suspension Bridge in Bristol, during my first week in the UK.

Then, just a few weeks later, I went to DjangoCon Europe 2022 in Porto, in person this time. Can you guess the people I met there?
-->

---
transition: slide-up
---

# Don't* meet your heroes

<img alt="Carlton Gibson, Sage Abdullah, Adam Johnson, Mariusz Felisiak" class="rounded-md w-md mx-auto mb-4" src="/djceu-2021.jpg" />

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
That was three years ago now, and the rest – is history.

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
I want open source to have such an effect on many many other people. For me to just be one example. One example out of many other people from all around the world.

So how do we keep this going?
-->

---
transition: none
---

# Google Summer of Code

<v-switch>

  <template #0>
    <img alt="Django's GSoC 2024 projects" class="rounded-md h-sm mx-auto mb-4" src="/gsoc-2024.png" />
  </template>
  <template #1>
    <img alt="PR #18489 to django/django, Added support for using key and path transforms in update() for JSONFields" class="rounded-md h-sm mx-auto mb-4" src="/gsoc-2024-pr.png" />
  </template>
  <template #2>
    <img alt="Wagtail's GSoC 2025 projects" class="rounded-md h-sm mx-auto mb-4" src="/gsoc-2025.png" />
  </template>

</v-switch>

<!--
This year, Django still participated in GSoC. Last year in particular (point), we had four projects, compared to two when I did mine in 2019. Different projects with different mentors and different areas of Django to improve.

And now, you no longer have to be a university student to join.

Also, anybody in the community can take part in other ways. You can suggest project ideas. Or, if you feel like it, you can also be a mentor.

!!!

Last year, I mentored a project that added support for updating JSONFields on the database level. It's like an SQL – sorry, a sequel – to my project. It has come full circle.

Five years later, it's very interesting to see how much of the code has changed, and how much has stayed the same.

!!!

This year, I mentored two projects out of four that we did for Wagtail. One project was to add Content Security Policy compatibility to Wagtail, and the other for UI improvements to the media library.
-->

---
transition: none
---

# Outreachy

<v-switch>

<template #0>

  <img alt="Blog post on Wagtail.org: 'Our very first Outreachy interns'" class="rounded-md h-sm mx-auto mb-4" src="/outreachy.png" />

</template>

<template #1>

  <img alt="Blog post on Wagtail.org: 'Introducing Wagtail's new accessibility checker" class="rounded-md h-sm mx-auto mb-4" src="/wagtail-accessibility-checker.png" />

</template>

</v-switch>

<!--
There is also Outreachy. It's a similar program to GSoC, but it's aimed towards supporting diversity.

Wagtail has participated in Outreachy a few times. One of the interns that I mentored and we're really proud of, Albina, made a built-in accessibility checker into Wagtail, which helps content editors in creating more accessible content. It's really amazing to see a new contributor come in and make such a big impact.

Django itself has not participated in Outreachy so far, but I think it's something that more open source projects should consider.
-->

---
transition: none
---

# Djangonaut Space

<img alt="Djangonaut Space: Where contributors launch" class="rounded-md h-sm mx-auto mb-4" src="/djangonautspace.png" />

<!--
And Django also has its own Djangonaut Space, a mentorship program for people who want to get into contributing to Django.

I think this is one of, if not the best initiative for Django ever. Maybe even in open source.

If you're interested, check out djangonaut.space. There's also a talk about Djangonaut Space by Dawn and Rachell from DjangoCon US 2023 and I highly recommend checking it out.
-->

---
transition: slide-up
---

# Python Core Mentorship

<img alt="A screenshot of an email from Guido to Mariatta about starting a mentorship for Python core development" class="rounded-md h-sm mx-auto mb-4" src="/python-documentary.png" />

<!--
I'm not sure if Python has a structured mentorship program, but I found that there's a mailing list for Python Core Mentorship. If you watched the new Python documentary, you would see that even Guido himself was willing to mentor new contributors like he did with Mariatta.
-->

---
transition: slide-up
---

# Signs of successful projects

<div class="flex justify-between w-full">

<div v-click>

As a mentee, you need to have:

- Clear understanding of the project
- Proactivity
- Openness to feedback
- Patience
- Persistence

</div>

<div v-click>

As a mentor, you need to provide:

- Regular, supportive communication
- Timely reviews
- Constructive feedback

</div>

</div>

<style>
  p {
    font-size: 1.25rem;
  }

  li {
    font-size: 1.25rem;
    line-spacing: 1.25;
  }
  li:not(ul ul li) {
    margin-bottom: 0.25rem;
  }
</style>

<!--
So, having been a mentee myself, and also a mentor, here are what I think makes a successful project in a mentorship program.

!!!

As a mentee, I cannot stress enough how important it is to have good reading comprehension skills. As someone who is new to the project, you'll do a lot of reading to understand what the project is aiming for, and how to get there in the context of the codebase. This includes reading the contribution guide, the documentation, the relevant tickets, the discussion in those tickets, and of course, the code itself.

You also need to be proactive. Don't wait for the mentor to tell you what to do next. If you have questions, ask them. If you are stuck, ask for help. If you have ideas, share them. Mentors are there to guide you, not to hold your hand the whole time.

You also need to be open to feedback. Mentors will review your code and give you suggestions on how to improve it. Don't take it personally. It's not about you, or at least usually, it's about the code. Learn from the feedback and apply it.

You also need to be patient. Instead of sprints, in open source it's more of a marathon. Things take time. Reviews take time. Don't rush things, but don't slack off. And don't turn your Slack off (at least when you're working). And, enjoy the journey.

And finally, you need to be persistent. There will be challenges and setbacks. You will make mistakes. You will face rejections. But don't give up. Keep trying. Keep learning.

!!!

As a mentor, it's mainly about being present and supportive. Make sure you communicate regularly with your mentee so you can make sure they are on the right track and not blocked. Be timely with your reviews so they can keep the momentum going. And don't be afraid to give constructive feedback. Otherwise, they won't learn. But remember to be kind and respectful.

So, overall, it's pretty obvious. But it can be quite a challenge when you're volunteering with limited time.
-->

---
transition: slide-up
---

# Making the most of it

- Well-prepared project ideas
  - Not limited to "core" projects
- Clear contribution guide
- Good first issues / easy pickings
- Mentoring (mostly reviewing) capacity

<style>
  li {
    font-size: 1.5rem;
    line-spacing: 2;
  }
  li:not(ul ul li) {
    margin-bottom: 0.5rem;
  }
</style>

<!--
So, how do we make the most of these mentorship programs?

First, prepare the project ideas. If you're an open source maintainer, think about what are some good projects that can be done in a few months. Good projects are well-defined, not too big and not too small.

Also, this might be obvious, but make sure the idea itself has been accepted by the community. You don't want to waste everybody's time working on something that nobody wants, or if there's a strong pushback.

Also, don't limit yourself to "core" projects. For bigger projects like Django and Python, if you don't have good ideas for the core codebase, think about the ecosystem. The supporting packages, libraries, tools, websites, and so on. Wagtail started participating in GSoC under the Django umbrella, but now we have our own organization.

Make sure you have a clear contribution guide. This is crucial for new contributors to get started. It should cover the basics of how to set up the development environment, how to write the code (as you might have specific styleguides), and how to run tests.

Take the time to triage and label good first issues or easy pickings. This will help new contributors find something to work on. Make sure the issues are well-defined and have clear acceptance criteria.

Finally, make sure you have enough mentoring capacity. Mentoring takes time and effort. Make sure you have enough people who are willing and able to mentor new contributors. Reach out to the community and see if there are people who are interested in mentoring.

-->

---
transition: slide-up
layout: center
---

# Making it sustainable

<v-switch>

<template #1>

There is no single answer

</template>

<template #2>

<div class="mt-8 flex flex-col items-end">

> …I came for the language, but I stayed for the community.

–Brett Cannon, Python Core Dev

</div>

</template>

</v-switch>

<!--
With big projects like Python and Django, mentorship is pretty much a necessity to bring new contributors in.

But how do we make it sustainable?

!!!

Unfortunately, there is no single answer to this question. Different projects have different needs and challenges. Also, you cannot really expect people to stick around, giving their time and energy away essentially for free.

So the best thing you can do is to be welcoming to new contributors, and keeping the community engaged and motivated.

There's this quote from Brett Cannon, one of the Python core developers.

!!!

I came for the language, but I stayed for the community.

I think that rings true for many people in open source.

One of the ways to do bring more people and get them to stay, from my experience, is to make sure your community see the value of mentorship. Celebrate the successes, learn from the mistakes, and share what you've learned and achieved with the wider community.
-->

---
layout: center
---

# Thank you!

[sage.id/pyconuk25](https://sage.id/pyconuk25)

<!--
That's it from me, thank you, I hope you found it useful.
-->
