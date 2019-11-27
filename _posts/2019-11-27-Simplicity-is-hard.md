---
layout: post
title: "Lunch break talks #1: Simplicity is complicated"
---

I sometimes like to watch a talk during my lunch breaks, so I decided to share some of the best ones I bump into, along with my notes.
I try to keep the topics fairly language agnostic (after all it's a lunch break). 

In this case the talk is from [Rob Pike](https://en.wikipedia.org/wiki/Rob_Pike), one of the creators of **Go**. It still resonates with me, even if I am writing JavaScript pretty much all day. Simplicity is a goal at any level you're writing (not just when writing a language!) and it is a hard thing to achieve.

I really like that Rob Pike makes everyone think about how hard it is to architect the grammar of a language, and how hard it is to take decisions and make tradeoffs when designing a new one. Funny how just a few days ago I was wondering why Go was not providing a filter or map method for arrays or slices, and he actually answers it during this talk.

Such an inspiring video that I think every programmer should see, even if not interested in Go! 

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/rFejpH_tAHM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

My key key take aways:

## New features
- a lot of languages are adding new features (coff... coff.. JavaScript...)
- a lot of this new features are the same ones across languages
- these languages are starting to look very similar one another
- if programmers use very similar languages, they will start to all think the same way
- in reality, what you ideally want, is a language that is **very good at solving domain specific problems**

## Complexity
- more features equals more complexity 
- Go has added a very small set of features
- how to pick the right feature to add? By [picking only the right ones](https://youtu.be/rFejpH_tAHM?t=263)!


## Readability
- more features means more time spent thinking about **how** to write something
- ideally you want a language to allow for [one way - or at least not many ways - of writing a thing](https://youtu.be/rFejpH_tAHM?t=371)
- a smaller feature set makes it more maintainable in the long run

## Simplicity
- a lot of thinking has to be put to achieve simplicity
- GC is one example of it in Go. A very difficult feature to implement that is 100% hidden from the end user, or even from the spec
- the interface to the user might be simple, even the implementation in the language might be simple, but the design of a feature is very likely to be a long, difficult process
- a lot of complexity might lie behind very simple code. He makes a fantastic walkthrough of a simple server written in Go and what's happening behind the scenes [here](https://youtu.be/rFejpH_tAHM?t=1266)

Bottom line is, **simplicity is hard to get**, but - if you get it right - makes doing complicated things easy to achieve for your users.

