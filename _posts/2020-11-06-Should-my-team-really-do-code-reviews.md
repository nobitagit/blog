---
layout: post
title: Should my small team really, really, really do code reviews?
---

While nearly everyone on paper agrees that code reviews are "a good thing" <sup>tm</sup> and everyone should do them, the reality often tells a different story. Big teams and companies all pretty much have a code review process in place. On the other hand, small startups often skip this step, especially at the early stages of their life.

If you're short of time feel free to skip at the last section and get a quick list of to-dos.
(And by the way, the answer to the question in the title is **yes**)

## Why you shouldn't do code reviews

Or rather, why people justify their team skipping reviews.

- Code reviews slow us down
- We're all senior developers, we trust each other
- We will start code reviews as soon as it makes sense
- We're only 2 developers, why should we?

If you recognise yourself in one or more of the above statements, I hope this post will convince you to reconsider.

## Let's talk about code quality

Code reviews are one of the best way to favour accountability and "good quality code", and - if done right - are better than any linting tool, code style guide or automated process.
But code quality comes at a cost, and the money used to pay for it is one of the most valuable currencies for a startup: **time**.
It is commonly accepted that doing things right is slower than doing them worse.

Martin Fowler talks about this in his article ["Is High Quality Software Worth the Cost?"](https://martinfowler.com/articles/is-quality-worth-cost.html). He mentions something interesting for us:

> Developers find poor quality code significantly slows them down **within a few weeks** [_emphasis mine_]. So there's not much runway where the trade-off between internal quality and cost applies.

[image]

_this chart is taken from Fowler's article ["Is High Quality Software Worth the Cost?"](https://martinfowler.com/articles/is-quality-worth-cost.html)_

If we agree on Mr Fowler's take we can see that "good code" pays off very quickly, even at very early stages.
While the first lines of a new codebase are often seen as something temporary ("it's only a prototype after all"), the reality is that those lines will influence the speed of development very early on. In a few weeks this advantage will very likely be noticeable.

## What to review

When it comes to code reviews, sometimes the question arises about **what exactly to review**. From what we've seen so far it's clear that my suggestion is that every new piece of functionality or any meaningful change should go through code review from day 1.
But what about **very small changes**? What about a quick readme update?

I keep it simple, **do code review absolutely everything**. Every single PR should be peer reviewed. And yes, this "unfortunately" means no pushing to straight to master, no "this is a quick one" PRs that go in unreviewed.

Sounds harsh? Maybe. But it's very hard to draw a line of what exactly is a meaningful change and what is a trivial one. Is a 200 lines indentation tweak worth more or less than a 1 line change in the models of your application? Even a readme update can contain misleading information or be based off wrong assumptions. And if we allow very small changes to go through by skipping review, what's the threshold that divides what's to be reviewed from what's not? How do you explain this to the next dev joining?

**If it's a quick change, it will also be very quick to review**.

## But but... this slows me down!

I hear you. Yes. You're pushing a one-liner and I'm telling you that you need to wait 2 hours, or even a day for someone else to review it.
I agree, this is **unacceptable**. And that's why having **prompt reviews** should be a top concern of your team processes.

We're in a small startup and speed is everything. Well, it turns out speed is everything at massive scales such as Google, too.

> At Google, we optimize for the speed at which a team of developers can produce a product together, as opposed to optimizing for the speed at which an individual developer can write code
>
> -- From [Google Engineering Practices Documentation](https://google.github.io/eng-practices/review/reviewer/speed.html)

Here are the guidelines from Google:

- If you are not in the middle of a focused task, you should do a code review shortly after it comes in.
- One business day is the maximum time it should take to respond to a code review request (i.e. first thing the next morning).
- Following these guidelines means that a typical PR should get multiple rounds of review (if needed) within a single day.

Your startup is fast, your reviewers should be too.
If you're looking for a nifty tool to automatically track how fast your team is I suggest [Velocity](https://codeclimate.com/velocity/act-optimize/).

## How thorough should your reviews be?

Reviews don't mean quickly scrolling through dozens lines of code and pressing "approve", perhaps adding a thumbs up to add a human touch to it.
It's very important to **actually read and understand the code**. If we are always very permissive we will get sloppy and just let anything go in. If you - on the contrary -  make a habit of reading critically and making an effort to understand what's going on, then it will get normal to ask questions and clarifications on many PRs.

This will make commenting a part of the daily routine of the company. When it'll be time to be more critical regarding an approach taken in code it will be less likely to cause a stir or be a source of litigation.

Reading the code also means that **knowledge spreads faster within the team**. Code reviews are not just about code quality, they are also about knowing what's happening around the codebase while you're not looking.

**Read the code, and understand it**. If you don't, **ask questions**.

## Who should be assigned to reviews?

In super small teams this is a non issue, you don't have much choice.
When the team grows, the question of who should review might arise.
In my view:

- Many assignees are better than fewer
- Aim for at least 2 reviewers, if the team's size allows. Look at how you can make this a policy with Github's [protected branches](https://docs.github.com/en/free-pro-team@latest/github/administering-a-repository/configuring-protected-branches)
- Also **have junior devs do review**.
- I repeat, **have junior devs do reviews**!

If you want to make assigning people faster, you can use Github's [codeowners](https://docs.github.com/en/free-pro-team@latest/github/creating-cloning-and-archiving-repositories/about-code-owners) that will automatically assign for you. Open a PR and you're done.

## One extra tip: comment your own PRs

Adding comments to a PR should not be limited to other people's PRs. You can add comments to your own ones as well.
I'm not the only one doing this and I find it really helpful when someone does it.

You can anticipate questions, explain why you've taken a certain approach, tell the reader that you already have a follow-up PR in mind.
This, again, will make reviews faster.

## In short

I hope by now I convinced you that code reviews are an investment that have short and long time benefits that even an early stage startup should consider.
Here's a quick list of action items for the busy ones and a recap for everyone who read so far.

- Do code reviews from day 1
- Review every single change, no matter how trivial
- Require "Pull request reviews before merging". See how on [Github](https://docs.github.com/en/free-pro-team@latest/github/administering-a-repository/configuring-protected-branches)
- Use [codeowners](https://docs.github.com/en/free-pro-team@latest/github/creating-cloning-and-archiving-repositories/about-code-owners) so you don't have to waste time picking reviewers.
- Strive for fast code review turn arounds. Maximum waiting time should be one day, but preferably it should be quantified in minutes. Read [how Google does it](https://google.github.io/eng-practices/review/reviewer/speed.html).
- You can track your team's speed and impact of code review by using tools like [Velocity](https://codeclimate.com/velocity/act-optimize/)
- Be thorough when reviewing, understand what you're reading. Avoid giving thumbs up to absolutely everything, without reading.
