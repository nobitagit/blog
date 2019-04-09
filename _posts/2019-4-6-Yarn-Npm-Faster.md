---
layout: post
title: A simple trick to make npm and yarn installs on Mac a bit less painful
---

One of the funniest and most overused memes of the JavaScript side of the Internet world is surely the one below.

![_config.yml]({{ site.baseurl }}/images/2019-4-6-node_modules.png)

If you've ever ventured inside a node_module directory of a medium sized project the (unknown?) author of this successful image clearly has a point.

For instance if you had to start a fresh project with Create React App and, just after running 'npx create-react-app my-app' you were to count the number of files you'd see something like this:

```sh
$ find ./node_modules -type f | wc -l
  27032
```

Just like that, you gave over 27k new items to our friend Spotlight to index.

Now, I rarely use Spotlight other than to quickly open an app, and when I do this happens

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Mac spotlight search be like:<br><br>Me: Types a couple letters<br>Mac: Here&#39;s the exact app you want<br>Me: Awesome, let me just press enter<br>Mac: OR MAYBE YOU WANT THIS COMPLETELY UNRELATED THING<br><br>*opens completely unrelated thing*</p>&mdash; Bruno Lemos (@brunolemos) <a href="https://twitter.com/brunolemos/status/1110316576157384704?ref_src=twsrc%5Etfw">March 25, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

But I digress.
What's imprtant is that the fact that I so rarely use it doesn't prevent Spotlight to crawl the content of my HD.
As any other entry in the file system the content of node_modules gets indexed by Spotlight by default.
I never really thought about this util I read the following tweet.

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Does your Mac becomes slow/unresponsive (fans kicking in etc.) when you `npm install` a huge project with a million tiny dependencies? I learned that adding an empty `.metadata_never_index` in /node_modules *beforehand* will prevent Spotlight from indexing all that crap.</p>&mdash; Roel Van Gils (@roelvangils) <a href="https://twitter.com/roelvangils/status/1113074439976075264?ref_src=twsrc%5Etfw">April 2, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

This along with the fact that we may often run a command like `rm -rf ./node_modules && yarn` means that Spotlight is potentially re-indexing this content multiple times a day.

With this newly acquired knowledge we can use a couple of tricks to avoid this issue and hopefully spare some wasted, memory intensive work to our machines.

For example, you can add a postinstall block to your package.json scripts so that it will be invoked in the background whenever you install the dependencies of your project.

```json
{
  //...
  "scripts": {
    "clean": "rm -rf ./build",
    "start": "node index.js",
    "postinstall": "touch ./.metadata_never_index" // <--
  }
}
```

This is nice and easy but you will need to do the same for all your projects, plus it will run for everyone else using that package.json, even if not on a Mac.
To have a similar functionality available everywhere on your file system and without touching the package.json you can add this to your `~/.bash_profile` or `./zsh_profile`.

```sh
# open your profile with `vim ~/.bash_profile`
# and paste the function below
function npmi {
    mkdir node_modules 2>/dev/null
    touch ./node_modules/.metadata_never_index
    if [ -f yarn.lock ]; then
        yarn install $@
    else
        npm install $@
    fi
}
```

Running `npmi` on the root of any node project will add the `.metadata_never_index` file automatically, just before installing the dependencies.
Finally it may be useful to just do the same for all existing projects on our file system.

```sh
find . -type d -name "node_modules" -exec touch "{}/.metadata_never_index" \;
```

This was suggested on Yarn's Github in a comment to [this issue](https://github.com/yarnpkg/yarn/issues/6453#issuecomment-476048618) and it seems it appeared first as far back as 2016 in a [StackExchange answer](https://apple.stackexchange.com/a/258791/44487).

Hopefully this simple trick will save us a bit of time (and battery power!) in the future.

