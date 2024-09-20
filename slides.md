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

So, I was like. Okay, how hard is it to have my own JSONField? If I can transform the dictionary into a string, I can save it to a TextField. Then, I just need some way to parse it back into a dictionary. And that's...

!!!

what I did! I just used Python's built-in JSON library to serialize and deserialize the data, then I can save the data in a TextField.

That being said, please don't try this at home!
-->

---
layout: center
---

# A few months later...

<!--
I finished my web development course by the end of 2018.

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

**A cross-DB JSONField!**

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

So, before I started working on my proposal, I concocted a plan. I wanted to get at least one PR to Django merged – but hopefully more than just one.

I started looking into the contribution guide, and I

!!!

watched the ticket tracker, waiting for a new ticket marked as "easy pickings" – which is kind of like a "good first issue" label on GitHub. They come and go so quickly.

!!!

Eventually I was able to land my first PR to Django – after some back and forth. As you can see there are 21 comments on the PR, for something that's essentially a one-line change, with three lines of test, and some documentation.

I learned a lot from this PR and it was a great experience. You get the general feeling and the common practices on how to make changes to the codebase.

Because you need to demonstrate that you are willing to learn. You are able to follow the instructions, and you are **patient**. Which may sound trivial, but it is actually a big deal in open source. Speaking from experience, there are people who rush to get things done and their PR merged, and not following instructions from the maintainers. That is not a good outlook.

!!!

Then I got two more PRs merged, one of which was for a ticket that I wrote myself. All of these are basically just one-liners, but they cover different aspects: implementation, tests, and documentation.

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


<v-switch>
  <template #0>

<img class="rounded-md h-sm mx-auto mb-4" src="/gsoc-acceptance.png" />

  </template>

  <template #1>

<img class="rounded-md w-4xl mx-auto mb-4" src="/gsoc-summary.png" />

  </template>

</v-switch>

<!--
I got this!

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

<!--
On June 9th 2019, which happened to be my 20th birthday, I submitted this initial PR to Django.

You can see how many people reviewed the PR. On the right side there.

!!!

I have blurred the number of comments on the PR. Would anybody like to take a guess on how many comments we have here?

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
-->

---
transition: slide-up
---

# DjangoChat

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/djangochat.png" />

<!--
Then I was invited by Carlton and Will Vincent to the DjangoChat podcast. If you didn't know already – give it a listen, it's great.
This was my first ever podcast. And still my only one so far – hint hint?

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

# Internship

<v-switch>

  <template #1>

<div class="relative">
  <img alt="" class="rounded-md absolute top-0 left-0 w-md mx-auto mb-4" src="/internship-0.png" />
  <img alt="" class="rounded-md absolute top-20 right-0 w-md mx-auto mb-4" src="/internship-1.png" />
</div>

  </template>

  <template #2>

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/internship-2.png" />

  </template>

</v-switch>

<!--
Later that year, I had to find an internship as part of my undergraduate degree. But unfortunately,

!!!

due to COVID, it was so hard to get one. A lot of the companies did not open any internship positions, and many even cancelled their programs.

However, I did get lucky.

!!!

I have some friends who worked at an agency – they use Django. And one of them noticed my JSONField project from the DjangoChat podcast.

I applied for an internship with them, and I got it. Thanks to Django!

My friend sent me this screenshot back in 2020. I'm so glad I've been keeping these "receipts" so I can use them for my talk.
-->

---
transition: slide-up
---

# Bachelor thesis

<div class="relative">
  <img alt="" class="rounded-md absolute top-16 left-46 w-xl mx-auto mb-4" src="/graduation.png" />
  <img alt="" class="rounded-md absolute top-0 left-32 w-[17.5rem] mx-auto mb-4" src="/bachelor-thesis.png" />
</div>

<!--
Then I wrote my bachelor thesis about my JSONField implementation – and I graduated in 2021.

And of course, even my graduation was virtual. COVID sucks.
-->

---
transition: slide-up
---

# Django Discord

<v-switch>

  <template #0>
    <img alt="" class="rounded-md w-xs mx-auto mb-4" src="/discord-0.png" />
  </template>

  <template #1>

  <img alt="" class="rounded-md w-xl mx-auto mb-4" src="/discord-1.png" />

  </template>

</v-switch>

<!--
A few months later, Carlton hit me up about a new Django Discord server.

It was not officially launched yet, so it was a bit hush-hush. But sure, I joined it.

I didn't really participate that much, but I looked for job listings from time to time, since I just graduated. And then one day...

!!!

I saw this message from Thibaud (whom I didn't really know at the time), a job listing for a junior level developer at Torchbox, an agency in the UK. They were doing remote hiring, pretty much worldwide.

So I looked at their website, and then I found...
-->

---
transition: slide-up
---

# A job listing

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/torchbox-listing.png" />

<!--
This!

A job listing for a Wagtail Developer.

The role is to work on Wagtail itself, an open-source content management system built on top of Django.

I looked at the requirements, and I think I pretty much ticked all the boxes, so I sent my application.

Then I did a couple interviews and a coding task. I was interviewed by Tom Dyson, the CTO, and he knew about my JSONField stuff. A few days later, I got the job!

They wanted me to move to the UK as soon as possible, but it was a surprise for me and my family. So I asked to work remotely for a few months while we sort out my visa and stuff.
-->

---
transition: slide-up
---

# First thing on the job

<img alt="" v-click class="rounded-md h-sm mx-auto mb-4" src="/wagtailspace.png" />

<!--
And do you know what one of the first things I did on the job was?

Well, Wagtail had their own version of JSONField that used the TextField approach. Around that time, it happened that Wagtail was about to bump its minimum Django version to 3.2, which includes the new JSONField.

So naturally, I was tasked to migrate all these existing text-based "JSONField"s into real JSONFields.

Then I did a talk about it at Wagtail Space US, which is Wagtail's own conference. But I did it virtually because I was still in Indonesia.

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

A lot of other things have happened since, but what I've shown you so far is enough to give you the idea. I haven't even mentioned the effect of all this on the people around me. My family's financial situation pretty much turned upside down. We were struggling to keep our feet up, and COVID made it even worse. But now it almost seems like a bad dream. All thanks to Django.

It started with me learning Django, and the rest didn't happen overnight. It's a snowball that keeps on rolling, and I have been very lucky to have all the opportunities. It's a privilege.

And so, I'd like to thank the biggest factor that made it all possible.
-->

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

<!--
Community.

None of this. None of this would've been possible without the Django community.

- The Django Fellows. Carlton, Mariusz, Tim, all the previous ones. And now we have Natalia and Sarah. They're absolute legends.
- django-developers. That's the name of the mailing list we used for people who contribute to the Django codebase. Discussing tickets, possible enhancements, reviewing PRs. Anyone can join.
- django-users. People who use Django. On their jobs, their personal projects, anything. Without its users, Django would not flourish.
- Django package maintainers. You write some code – it's useful, and you feel other people can benefit from it as well, so you publish it. Someone finds a way to improve it, you work together with them, and merge it in. In some cases, someone else improves your code and get it into Django core. Everyone gets it. Everyone wins.
- ...
- Django event organizers. I wouldn't be here without you folks. Thank you very much for running this event. It's no easy feat and I really appreciate it. These events keep the Django community alive in real life.
- Django event attendees. Everybody you see today, tomorrow, and the next few days plays a part in this. Without you, this event would be meaningless.
- And you! Whoever is watching this – right now in this room, or virtually from your own bedroom, or later on YouTube. If you're watching this, you play a part in keeping Django alive.
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

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/gsoc-2024.png" />

<!--
This year, Django still participated in GSoC. We had four projects this year, compared to two projects when I did mine in 2019. Different projects with different mentors and different areas of Django to improve on.

Google has removed the requirement for participants to be university students, so even more people can join.

And anybody in the community can take part. You can suggest project ideas. You can write a proposal and work on the project. If you feel like it, you can also be a mentor.

This year, I mentored a project that adds support for updating JSONFields on the database level. It's like an SQL – sorry, a sequel – to my project. Five years later, it's very interesting to see how much of the code has changed, and how much has stayed the same.
-->

---
transition: none
---

# Outreachy

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/outreachy.jpg" />

<!--
There is also Outreachy. It's a similar program to GSoC, but it's aimed towards supporting diversity. These days, I see a lot of toxic people on the internet discrediting the work of someone from an underrepresented background by calling them a "DEI hire" – which is just infuriating.

Wagtail has participated in Outreachy a few times, and I can tell you that our interns are some of the coolest people I've ever worked with. And it is really amazing what they can do with all their limitations. I think they can put a lot of privileged people to shame.

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

If you're interested, check out djangonaut.space, and there's also a talk about it [INSERT TALK HERE].
-->

---
transition: slide-up
layout: center
---

# Keeping Django alive

<!--
Those were some of the programs we can take part in, to bring new contributors to our community.

But apart from that, there are things that we can do to keep Django alive.
-->

---
transition: none
---

# Make PRs

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/django-contributors.png" />

<!--
One of the most obvious is to make PRs to Django. Submit bug fixes, enhancements, documentation improvements. Sure, it is not the easiest thing – but it is very, very rewarding. Seeing that purple merge icon on GitHub, and your name in the contributors list. It's one of the best feelings ever.
-->

---
transition: none
---

# Review PRs

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/django-review.png" />

<!--
Aside from making PRs, you can also review them!

Reviews can come from anyone – it doesn't have to be the Django Fellows. It doesn't have to be Natalia or Sarah.

You don't have to know the entire Django codebase to review PRs. With how big Django is, I don't think anyone does. But if you use Django enough, you'll be familiar with where things go.

And remember – these PRs will eventually become the things you use out of Django. Reviewing PRs from the perspective of a user really helps shape the APIs to be as friendly as they can be. So go out there and start digging.
-->

---
transition: none
---

# Create tickets

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/django-trac.png" />

<!--
And if you use Django enough, you'll encounter moments where you wish Django can do better. Maybe you find a bug, a potential feature, or a paragraph in the documentation you wish were there.

Write a ticket. Explain the situation and how you think it can be improved. Maybe others feel the same way, or maybe nobody had thought about it. Then maybe someone else works on it and get a PR merged into Django, giving you what you want – without you writing a single line of code.

Or maybe it gets rejected – but it's okay, we have disagreements from time to time. You can bring it up on the forums and maybe we change our minds. Here's a fun fact: the ticket for JSONField in Django was initially rejected. So, if your idea gets rejected, do not be discouraged.
-->

---
transition: none
---

# Publish packages

<img alt="" class="rounded-md w-lg mx-auto mb-4" src="/django-packages.png" />

<!--
Publish packages.

Django has batteries included, but the superchargers are sold separately. For a good reason.

The JSONField we now have in core wouldn't be possible without the existing packages we had in the ecosystem.

And while we talk about packages, I'd like to give a shoutout to Jeff Triplett and others who have been keeping djangopackages.org alive.

With how many packages there are out there, discoverability is a problem – and this website can help solve that.
-->

---
transition: none
---

# Test against Django's main

<img alt="" class="rounded-md w-3xl mx-auto mb-4" src="/django-cimain.png" />

<!--
Okay, this is an easy one.

If you haven't already, add an extra setup in your CI matrix to test your project against Django's `main` branch. Whether your project is a Django package, or an actual Django project. Test against Django's main.

This helps catch bugs and compatibility issues early. And this applies to both sides. You'll find out whether your project works with the bleeding edge version of Django and you can anticipate any API changes much quicker.

If all is well, you're going to have more confidence in upgrading to the final release when it comes out.

In some rare cases, you'll find that it's actually a regression in Django. Which is actually great, because if you report that and it gets fixed, you'll save other people from having the same trouble.

We do this with Wagtail, and there have been a few times where we noticed some regressions and we reported and/or submitted the fix. Everyone wins.
-->

---
transition: none
---

# Donate

<img alt="" class="rounded-md w-lg mx-auto mb-4" src="/django-donate.png" />

<!--
If you've got some money but not the time to do any of the previous stuff, here's an easy one.

Maintaining Django costs money. The DSF pays out the Django Fellows, and that money comes from donations. So, make a donation.
-->

---
transition: none
---

# Donate

<img alt="" class="rounded-md w-4xl mx-auto mb-4" src="/django-sponsors.png" />

<!--
You can also do it via GitHub Sponsors, if that's more convenient.
-->

---
transition: none
---

# Participate in events

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/django-events.png" />

<!--
And as I said before, participate in events, just like this!

Thank you everybody for being here. It's part of what keeps Django alive.

And if you want to take it a step further...
-->

---
transition: none
---

# Participate in events

<img alt="" class="rounded-md h-sm mx-auto mb-4" src="/djceu-coc.jpg" />

<!--
You can help organize the events!

Last year I volunteered to be in the Code of Conduct team for DjangoCon Europe. Events like this won't be possible without the hard work by the organizers – so again, thank you for this wonderful event.

There's plenty of opportunities to volunteer, so if you're interested, just ask the organizers.

And when you return home from DjangoCon this week...
-->

---
transition: none
---

# Join meetups

<img alt="" class="rounded-md w-4xl mx-auto mb-4" src="/django-meetup.png" />

<!--
Consider joining your local Django meetups.

In the UK, there's the Django London meetup group that gathers every month. It's a great group of people with a warm atmosphere, I've been to quite a few times and I can highly recommend it if you're in the UK.

And since we're in Durham...
-->

---
transition: slide-up
---

# Join meetups

<img alt="" class="rounded-md w-2xl mx-auto mb-4" src="/django-social-raleighdurham.png" />

<!--
There's also the DjangoSocial group here in Durham. It's a group where you can do social activities with other people from the Django community.

It's part of the larger DjangoSocial network. If you're interested you can go to django.social or talk to Jon Gould and the folks from Foxley Talent.

Okay, and finally...
-->

---
transition: slide-up
---

# Use it!

<img alt="" class="rounded-md w-4xl mx-auto mb-4" src="/django-use.png" />

<!--
Just use it. Use Django.

This is a screenshot of Django's usage statistics on GitHub.

I don't know what happened here when I took it, but this was not edited. I think there must've been a bug on GitHub because that's a _lot_ of cats.

Anyway. One of the reasons why my uni teaches Django is because it's widely used in the industry. I If nobody uses Django – I wouldn't be here today.

And, that's it from me.
-->

---
layout: center
---

# Thank you!

[slides.laymonage.com/django-life](https://slides.laymonage.com/django-life)

<!--
Thank you!
-->
