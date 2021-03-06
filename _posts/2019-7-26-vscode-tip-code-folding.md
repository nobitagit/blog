---
layout: post
title: "Vs Code one tip a week: code folding"
---
Code folding can make browsing long files easier. When we're trying to focus on the big picture without being distracted by smaller details code folding can be an invaluable aid.

---
This is an effort to learn how to get the best out of Vs Code keyboard shortcuts without feeling overwhelmed.

Learning how to use our favourite IDE without a mouse can make a big difference.
Since an extension like the amazing [Key Promoter for Jetbrains](https://plugins.jetbrains.com/plugin/4455-key-promoter) seems to be [far from being a reality for now at least](https://github.com/Microsoft/vscode/issues/26729), I started collecting my favourite keyboard tricks in bite sized chunks.

The idea is simple: find one tip you like, practice it the whole week until you've really mastered it and made it part of your workflow and then move to the next one.

---

<img src="https://media.githubusercontent.com/media/nobitagit/blog/gh-pages/images/2019-7-26-vscode-tip-code-folding.gif" />

To fold a block

```
⌘ + OPTION + [
```

To unfold it:

```
⌘ + OPTION + ]
```


## Users of Vs Code with Vim extensions
The above command will work for all users of VsCode. Those using a vim emulation extension like VsCodeVim or amVim will have an extra pair of shortcuts to choose from.

To fold a block, switch to normal mode and:

```
z + c
```
Where "c" stands for "close".

To unfold:

```
z + o
```

Where "o" stands for "open".

## Further shortcuts
Note that you can also fold and unfold an entire file, but I find that less useful and never really felt the need to memorise that shortcut combination.

