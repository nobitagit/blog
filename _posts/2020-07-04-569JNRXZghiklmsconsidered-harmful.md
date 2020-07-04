---
layout: post
title: $ considered harmful
---

One of the most satisfying feelings in life is waking up at 5am, going to the kitchen to drink a glass of water while casually scrolling through Twitter on your phone and finding someone (on the Twitter feed, not in the kitchen) that is right on the internet*:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Does anyone else find the use of $ or whatever at the start of commands like this frustrating?<br><br>Yes, it delineates commands, but it means you can&#39;t easily copy and paste. <a href="https://t.co/UJKUEk4JrE">pic.twitter.com/UJKUEk4JrE</a></p>&mdash; Cybergibbons (@cybergibbons) <a href="https://twitter.com/cybergibbons/status/1277199020268703744?ref_src=twsrc%5Etfw">June 28, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

_* Where right means: someone else is saying something you always thought of, but never quite found time/space/willpower/occasion to express to a wider audience or even to yourself._

![Morning duties](https://dev-to-uploads.s3.amazonaws.com/i/9hbwbe4ej5shyzw7nand.jpg)

## Disclaimer

Let's begin with a disclaimer: this is a clear case of first world problem that really has no great meaning in the grand scheme of things, and the arguments against it are still valid. I just found it amusing to see I wasn't really the only one being mildly frustrated by it.

I also think that the discussion that ensued is interesting and worth reading.

## Problem

When setting up a project you might need some setup and most of the times this involves running a handful of shell command.  An example:

```
$ curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python
$ pyenv install 3.7.6
$ pyenv local 3.7.6
```

Ok, now try copy pasting those 3 lines in one go... Chances are you will see as output something like this:

```
zsh: command not found: $
```

Isn't this irritating?

## What does `$` even mean

There are a few reasons why `$` is prepended to every line of a shell command example:

- it indicates that indeed, what you are looking at is a command that is meant to be run in a shell
- It indicates that you need to run this command as a user, and not as root

## Why you should **not** remove `$`

Some valid arguments in favour of keeping the $:

- You should never trust commands on the internet and blindly copy paste them

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">This is sometimes also done to actively discourage copy&amp;paste of multiple (or single) commands from the web because it&#39;s NEVER a good idea to do so. Browsers don&#39;t filter out invisible content when you do. Almost as bad as a wget or curl piped to a shell.</p>&mdash; Henrik Danielsson (@two_d) <a href="https://twitter.com/two_d/status/1277464720728035331?ref_src=twsrc%5Etfw">June 29, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

- You should communicate when to run a command a root (using `#`) and when not (`$`)

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Seems to be an unwritten law, $ for normal user, # for admin.</p>&mdash; Konrad Iturbe (@konrad_it) <a href="https://twitter.com/konrad_it/status/1277201037321998338?ref_src=twsrc%5Etfw">June 28, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## The case for removing `$`

- makes it harder to copy, especially multiline commands
- not everyone might be aware of # meaning "run as root"

In the  thread another user makes an great point

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">If the code block is to demonstrate the 𝑜𝑢𝑡𝑝𝑢𝑡 of a command, then I include the $ (one line only) to differentiate the command from the output.<br><br>Otherwise, leave them out so the block can be copy/pasted.</p>&mdash; Joe Julian (@JoeCyberGuru) <a href="https://twitter.com/JoeCyberGuru/status/1277990481884901376?ref_src=twsrc%5Etfw">June 30, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## How to remove `$`

If you have read so far and sort of agree that we might be better off not including the `$` in shell samples, here's a few ways to make it happen.

### The easy way

Just don't type it! This applies to Github and pretty much all places where you have no control over how code samples are rendered. A post on Dev.to is one example.

So this
```sh
$ pyenv install 3.7.6
```
becomes
```sh
pyenv install 3.7.6
```
if a command is meant to be run as root prepend it with sudo

```sh
sudo apt-get install ubuntu-desktop
```

### The stylish way

If you have control over the rendering and you still like you readers to see the `$`, consider using CSS to remove it from the text that ends up in the clipboard when copying. In the thread, Twitter user [@beccadottex](  https://twitter.com/beccadottex) shares a [Codepen](https://codepen.io/rebeccajae/pen/PoZJwav) of how this can be done.

[@theconfigurator](https://twitter.com/theconfigurator) shares another one here

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">It&#39;s pretty easy to do, and makes the UX so much better.<br><br>HTML:<br>&lt;prompt /&gt;<br><br>CSS:<br>prompt::before {<br> content: &quot;$&quot;;<br>}</p>&mdash; configurator 🏳️‍🌈 (@theconfigurator) <a href="https://twitter.com/theconfigurator/status/1277279975654281216?ref_src=twsrc%5Etfw">June 28, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Should you really stop using $?
As any other opinion based topic, there seems to be far from an agreement on the best practice around this, as the long thread on Twitter shows.

As for myself, I always found it redundant and pointless but funnily enough [I found myself using it out of habit](https://dev.to/aurelio/up-your-lodash-game-by-going-functional-from-chaining-to-piping-3li8). From now on, I will purposefully keep the `$` out of my docs.

/rant

---

- _comic at the beginning is a ripoff of Xkcd's [Duty Calls](https://xkcd.com/386/)_
- _cover image is by [Ryan Quintal](https://unsplash.com/@ryanquintal)_.
