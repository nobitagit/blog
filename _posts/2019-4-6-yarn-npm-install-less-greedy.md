---
layout: post
title: A simple trick to make yarn and npm installs less memory intensive 
---

One of the funniest and most overused memes of the JavaScript side of the Internet world is surely the one below.

![_config.yml]({{ site.baseurl }}/images/2019-4-6-node_modules)

If you've ever ventured inside a node_module directory of a medium sized project the (unknown?) author of this successful image clearly has a point. 

For instance if you had to start a fresh project with Create React App and, just after running 'npx create-react-app my-app' you were to count the number of files you'd see something like this:

```sh
$ find ./node_modules -type f | wc -l
  27032
```

Just like that, you gave over 27k new items for our friend Spotlight to index.

Now, I rarely use Spotlight other than to quickly open an app (and when I do this [happens]()), but that doesn't prevent it to crawl the content of my HD. 
As any other file in the file system the content of node_modules get indexed by Spotlight by default.
I never really thought about this util I read the following tweet.

https://twitter.com/roelvangils/status/1113074439976075264

This along with the fact that `rm -rf ./node_modules && yarn` is one of the most common commands of a developer that happens to work with JavaScript means that Spotlight is potentially re-indexing this content multiple times a day.

With this newly acquired knowledge we can use a couple of tricks to aovid this and hopefully avoid some wasted, memory intensive work for our laptop.

You can then add a postinstall hook to your package.json scripts so that will be invoked in the background whenever you install the dependencies of your project.

{
   //...
   "scripts": {
       "clean": "rm -rf ./build",
       "start": "node index.js",
        "postinstall": "touch ./.metadata_never_index"
    }
}

If instead you'd rather have it available on just about any node project without touching the package.json you can add this to your `~/.bash_profile` or `./zsh_profile`.

function npmi {
    mkdir node_modules 2>/dev/null
    touch ./node_modules/.metadata_never_index
    if [ -f yarn.lock ]; then
        yarn install $@
    else
        npm install $@
    fi
}


Finally it may be useful to just do the same for all existing projects on our file system.

find . -type d -name "node_modules" -exec touch "{}/.metadata_never_index" \;

This was suggested on Yarn's Github in a comment to [this issue](https://github.com/yarnpkg/yarn/issues/6453#issuecomment-476048618)

[code example]

Credits
* original tweet
* [Chris Bull](https://github.com/chrisbull) for the super handy one liner to exclude all projects from Spotlight
* [Emmanuel Kluge](https://twitter.com/Herschel_R/status/1113095592559984641) that came up with a bash function from which the one above is derived and aliased it to just `i` (savage).
* the unknown author of the node_modules image above



