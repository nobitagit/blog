---
layout: post
title: How I remember everything I learn
---

Have you ever been asked this question: 

> If you could have just one superpower, which one would you choose?

It's one of those questions that can either leave you frozen without any actual clue what to say or you might catch yourself with so many ideas that you're simply overwhelmed by the abundance of options.

---

_Unrelated: the most clever answer to this question I've read is [this one](https://qr.ae/pNboqr) by the way._

![Screenshot from a Quora answer](https://dev-to-uploads.s3.amazonaws.com/i/czpskp44c6rp78opbgl7.png)

_(not a bad idea, right?)_

---

There's no right or wrong of course, but if you asked me, my answer would be something like:

> I would be able to remember **everything** I learn

I don't know about you, but **knowledge retention** is easily one the big challenges of my life. I read blogs every day, I watch countless videos, I start (and sometimes finish) a few technical books per year and remembering everything I learnt is outright **unrealistic**.
There's just so much one can retain of what they read or hear.

## What I tried

If you're like me, you've tried them all:

- blogging about it
- highlighting the most important sentences of the books you read
- taking physical notes
- taking notes online (eg Evernote)
- writing summaries of the books and videos you read
- flash cards
- semi-organised (or semi-random), heavy bookmarking with tools like Pocket
- you name it

All these methods have their merits. All of them ultimately **fall short** for me.
Some worked better than others but I couldn't for the life of me stick to one.

## An impossible balance

I felt I was somehow looking for an **impossible balance**. I wanted a solution that:

- was structured...
- ...but still loose enough so it didn't kill my creativity and the free flow of thoughts that happens when learning something new
- searchable
- low friction, as in, easy to type in, giving me a pleasant, natural experience
- non-existent learning curve (no "new revolutionary tools"). Sorry, I'm busy leaning something else
- easily retrievable, available anywhere
- recoverable if a disaster happens (think about forgetting your notebook on a train, or having it chewed by your dog)
- trackable, I wanted to be able to see my progress through the months, and possibly get some stats, if not for vanity

## Turning point

I was resigned to never find a solution for it.
Until one day, I randomly bumped into [this](https://lobste.rs/s/ord0rg/does_anyone_else_keep_their_own_knowledge)

> I’ve been extending and improving my [personal wiki](https://github.com/nikitavoloboev/knowledge) for 1 year now and it has been one of the best things I’ve done. I found writing blog posts was too high friction and very often didn’t finish things because there is so much you can talk about in any given article. But a wiki is just a living document containing your notes and thoughts on things. I also use it as my public bookmark manager as I collect interesting to me links under each topic.
>
> For my wiki, I render everything to the web first with GitBook. And I have a macro I run that automatically commits any changes I’ve made with Sublime Text on the mac and Ulysses on the phone so everything is super easy to edit and publish.
>
> Does anyone else keep their own wiki here? Or you think a blog is enough for you?
>
> -- [Nikita Volobev](https://github.com/nikitavoloboev) on [Lobsters](https://lobste.rs/)

And there it was. The sign that the "impossible balance" **was actually possible**.

From that moment, around 2 years ago, I also created [my own personal wiki](https://github.com/nobitagit/knowledge) and it's been my go-to tool for remembering everything I learn.

It's nothing more than a repo on Github, divided in folders with a bunch of READMEs in each of them.
Folders are the main categories, such as databases, Kubernetes, JavaScript, security, regex etc. Each README inside them subdivide the folder into specific topics. Security would have READMEs for JWT, Frontend, Cryptography etc.

## Evolution

I initially started with only programming topics but found myself using the wiki to store notes about literally anything I learn in any domain such as music, writing, managing, speaking and writing German.

The structure is not super strict, but still provide some order and thanks to Github searching is absolutely a breeze.

Since everything is committed, I wrote a small Go program to calculate some stats by accessing the git history and generate an HTML page. A Github action runs the script and publishes it to a URL. The page looks - let's say - minimal (it has literally 3 lines of CSS), but it gives me an overview of what I have been learning in the past 90 days. Apparently I've been very much into career development and management as of late.

## Giving it a try

I strongly recommend giving this method a try if you're also struggling with knowledge retention.
The investment to start it so low, you literally only need to create a repo and that's it, and you can even fork mine and simply delete most of the folders if you like.

Nikita and I are not the only ones using this method. You can find plenty others for inspiration by going through [this long list of wikis](https://github.com/RichardLitt/meta-knowledge).
