---
layout: post
title: "Upping your Lodash game: going functional from chaining to piping"
---

[Lodash](https://lodash.com) is pretty much the de-facto standard when we're looking for a utility library for data manipulation in JavaScript. So much that it is the [most depended on package](https://gist.github.com/anvaka/8e8fa57c7ee1350e3491) on npm.

Lodash allows developers to write expressive code by covering the most common needs when handling data.
For instance it makes writing and reading operations like these seem very natural and straightforward:

```
_.intersection(['a', 'b', 'c'], ['a', 'c', 'e']); // ['a', 'c']
```

It's easy to fall in love with the expressiveness and convenience of such a tool. As soon as you get out of the most simple use cases though you will arguably miss a great feature of native array methods such as `Array.map`, `Array.slice` and the likes. This "missing" feature is chaining.

We'll see in example in a minute. A quick note before going any further.

## Disclaimer: which Lodash?
As we will see Lodash comes in different distributions.
In this post we will start by making use of [Lodash-es](https://github.com/lodash/lodash/tree/es) which is Lodash exported as ES modules. This allows us to write stuff like:

```js
import { take, orderBy } from 'lodash-es';
```

rather than the slightly more verbose

```js
import take from 'lodash/take';
import orderBy from 'lodash/orderby';
```

And have our bundler (Webpack in this case) only include what's needed by `take` rather than blindly bundling the whole Lodash.

The goal in both cases here is to stay away from importing the whole library, which will happen when writing stuff like:

```js
// try to avoid doing this
import _ from 'lodash';

_.take([1,2,3], 2); // [1, 2]
```
In this post we will see both how to use different flavours of Lodash to achieve what we want and what this means for our bundles as well.

Finally, you can find a repo with the below code split into commits if you're interested in trying it yourself [link].
I will reference the commits for each stage in the comments at the top of each section. For example this will check out the first commit.

```js
// -> git checkout 56e092ab1bd01b2492286d5e0f6922b0405b482b
```

With the introduction out of the way it's time to see some code!

## Introduction: our challenge of the day
In order to demostrate the power of Lodash we will set ourselves a realistic goal.
Given a list of players we want to find the names of the top three players by number of goals.
If two players have the same number of goals, then the one who managed to get to that tally with a lower number of shots will be the winner.

Here's a list of Premier League strikers, along with their stats.
```
const players = [
  { player: 'Aleksandar Mitrovic', goals: 10, shots: 118 },
  { player: 'Mohamed Salah', goals: 19, shots: 116 },
  { player: 'Harry Kane', goals: 17, shots: 110},
  { player: 'Sergio Agüero', goals: 19, shots: 99},
  { player: 'Raùl Jiménez', goals: 12, shots: 96},
  { player: 'Paul Pogba', goals: 13, shots: 93 },
  { player: 'Eden Hazard', goals: 16, shots: 86},
  { player: 'Pierre-Emerick Aubameyang', goals: 18, shots: 81 },
  { player: 'Gylfi Sigurdsson', goals: 12, shots: 78},
  { player: 'Sadio Manè', goals: 18, shots: 77},
];
```

We will use this data througout the post and reference it as `players` from here on.

## Step 1: Let's keep it simple by using intermediate variables

To get the ball rolling we'll start from the easiest approach. We will use a handful of Lodash methods to solve the challenge and will use variables to store intermediate values.

```js
// git checkout aa1935007c2fa7e3663df7401dfca9f62715f3e7
import { take, orderBy, map } from 'lodash-es';

// Sort players by goals scored and shots taken.
// If 2 players have the same number of goals, the one player
// with less shots on targets is ranked higher.
const sorted = orderBy(players, ['goals', 'shots'], ['desc', 'asc']);
const top3 = take(sorted, 3);
const result = map(top3, 'player');
console.log(result); // ["Sergio Agüero", "Mohamed Salah", "Sadio Manè"]
```
Nice, that's the result we were after. Problem solved.

Let's quickly analyse the bundle now.
```sh
> npm run build
```

We can also run the prod code from within the build dir:

```sh
> python3 -m http.server 3333
```

And finally opening our browser at `localhost:3333` and open the console to see the result.
Now that we have built our code we can check *how* it was built and its size.

```sh
du -h build/js/*.js
 20K    build/js/1.chunk.js
4.0K    build/js/app.js
```

We can see that Webpack has automatically split our code in two files, one containing our code (app.js), the other containing the lib (1.chunk.js).
As a side note `app.js` is actually a bit smaller than 4k [as reported by `du`](https://unix.stackexchange.com/questions/34331/why-does-the-du-command-count-in-4kb-steps/34338#34338), but we can keep that 4k as a reference value.

## Step 2: From variables to chaining, or how to break production in 3 simple steps

Ok, so now our first priority is to get rid of those redundant intermediate variables (`const sorted` and `const top3` in the case above).
Chaining is perhaps the first idea that comes to our mind, and a good one, so that we can do something like this.

```js
const result = orderBy(players, ['goals', 'shots'], ['desc', 'asc'])
   .take(3)
   .map('player');
```

Although chaining is actually provided by Lodash, if you try this you will see it's not working.
There are various ways to achieve it with Lodash and one of them is to use the combination of `chain` and 'value' methods. Here's an example.

```js
import { chain } from "lodash-es";

const result = chain(players)
  .orderBy(["goals", "shots"], ["desc", "asc"])
  .take(3)
  .map("player")
  .value();
```

Let's run the app in development (`npm run start`) and we will see that everything works as expected.

![_config.yml]({{ site.baseurl }}/images/2019-7-2-lodash-2.png)

Let's see how our prod bundle would look like in this case.

```sh
npm run build
```
And cd into our distribution directory.

```sh
cd build/js
ls -l
-rw-r--r--  1 user1  staff   2788 Apr 21 21:41 app.js
-rw-r--r--  1 user1  staff  22082 Apr 21 21:41 app.js.map
```
Strangely enough Webpack produced only one bundle and did not split app and chunk as before.
Plus, the bundle looks suspiciously tiny.

```sh
-> du -h app.js
4.0K    app.js
```

We can try running the app in prod and see what happens.
```
cd ..
python3 -m http.server 3333
```

We open http://0.0.0.0:3333/ and we can see we somehow managed to break our app.

```
TypeError: (intermediate value)(...).orderBy is not a function
```

![_config.yml]({{ site.baseurl }}/images/2019-7-2-lodash-1.png)

The reason here is that Webpack is unaware that Lodash in this case needs other methods than the one we're explicitly importing (ie `chain`). The bundler then happily tree-shakes all the methods that appear to be unused, crucially leaving us with no _.map, no _.orderBy, no _.take on the prod bundle. This will end up throwing a runtime error on production. Not exactly great.
To fix this we can import the whole lodash and destructure only what we need later on.
We'll do just that and see the outcome.

## Step 3: Fix the broken chain by importing all the things
As said, let's import the whole Lodash by importing the default export and assigning it to the `_` char. We then extract chain via destructuring, so the rest of the code stays the same.
```js
// git checkout eb0a80fc81e51d325e12042b59f57e33e13c4cab
import _ from "lodash-es";

const { chain } = _;

const result = chain(players)
  .orderBy(["goals", "shots"], ["desc", "asc"])
  .take(3)
  .map("player")
  .value();
```

A quick check at our dev env build will show that everything is still working, so we can rebuild the production bundle and test in the browser. Everything works.

![_config.yml]({{ site.baseurl }}/images/2019-7-2-lodash-3.png)

Let's check the bundles once more.

```sh
-> du -sh build/js/*.js
 88K    build/js/1.chunk.js
4.0K    build/js/app.js
```

We now are back to having 2 files, our "main" (app.js) and our chunk. Unfortunately this last file is way bigger than before.


This comes down to the fact that we told Webpack to include the full library and our bundler did just that. As it turns out, chaining and tree-shaking in Lodash-es [can't be achieved together](https://github.com/lodash/lodash/issues/3298), unless you're willing to do [some not-so-pretty gymnastics](https://github.com/lodash/lodash/issues/3298#issuecomment-341685354).

The question now is, is there a way we can we have the expressiveness of chaining (or a similar pattern) without incurring in a penalty cost or having to maintain a clunky custom version of it? That's exactly what we'll try to achieve in our next steps.

## Step 4: From chaining to piping

The first thing we'll do is shift from one pattern, [chaining](https://medium.com/backticks-tildes/understanding-method-chaining-in-javascript-647a9004bd4f), to a similar but fundamentally different one, i.e. [piping](https://vanslaars.io/post/create-pipe-function/).

Chances are you have already seen piping in action. In any case, the idea behind `pipe` is very simple.
Pipe will accept 2 arguments: a sequence of functions and a value as starting input.
Every function inside `pipe` will then receive as input the output of the previous one.

This is exactly what we need, and in essence, not *that* distant to what we have when chaining.
As it turns out, Lodash provides a `flow` function that is the equivalent of pipe. Let's see it at work in practice.

```js
// git checkout 146c84a17f2c44c81317794740e8d8c46aae0938
import { flow, orderBy, take, map, partial } from "lodash-es";

const result = flow(
  _players => orderBy(_players, ["goals", "shots"], ["desc", "asc"]),
  _players => take(_players, 3),
  _players => map(_players, "player")
)(players);
```

This is great. We have now removed the need of intermediate constants and we turned our data transformation into a pipeline. The `flow` function takes care of kicking off everything with the value of `players` and then passing the result of each step (each line) to the next transformation.

A quick check at our bundle and we can see that we've trimmed down the size of our bundle again.

```sh
-> du -sh build/js/*.js
 32K    build/js/1.chunk.js
4.0K    build/js/app.js
```

I find the code readable as it is but there's still quite a bit of redundancy here. Those `_players` argument repeated twice on each line create noise and it would be really nice if we could remove them.

As things stand we have have 2 solutions. We can be cheeky and just rename that variable to something very short, since it's quite clear by now what that value represents:

```js
const result = flow(
  v => orderBy(v, ["goals", "shots"], ["desc", "asc"]),
  v => take(v, 3),
  v => map(v, "player")
)(players);
```

But wouldn't it be even better if we could remove that arrow function altogether?
In essence what I am aiming for is this.

```js
const result = flow(
  orderBy(["goals", "shots"], ["desc", "asc"]),
  take(3),
  map("player")
)(players);
```

Which is the closest we could get to the original chained version:

```js
const result = chain(players)
  .orderBy(["goals", "shots"], ["desc", "asc"])
  .take(3)
  .map("player")
  .value();
```

Unfortunately that doesn't quite work.
To do make it happen we need to somehow turn the Lodash methods we are using into functions that support partial application. We can attempt to do that, and actually Lodash helps us again by providing a convenience method to turn every function into one that executes only when the last argument is passed. `_.partial` is what we are looking for.

## Step 5: Partially there

```js
import { flow, orderBy, take, map, partial } from "lodash-es";

const __ = partial.placeholder;

const result = flow(
  partial(orderBy, __, ["goals", "shots"], ["desc", "asc"]),
  partial(take, __, 3),
  partial(map, __, "player")
)(players);
```

There's quite a bit of explanation to do here.
First thing, we pass the function we want to turn into one that supports partially applied arguments.

```js
partial(orderBy, ...),
```

Then we list all the arguments we want to pass to this function, in order.
Crucially the first argument we need to pass to it is our `_players` argument. We can now instruct Lodash that we will pass this value at a later stage by using a placeholder. Lodash provides this functionality so that we can mark the slots where the arguments will be passed once they become available.

```js
const __ = partial.placeholder;
// ...
partial(orderBy, __, ... )
```

We then can just list all the remaining arguments, as we now them already:

```js
 partial(orderBy, __, ["goals", "shots"], ["desc", "asc"])
```

Here's once again the full version of it:

```js
const __ = partial.placeholder;
// Sort players by goals scored and shots taken.
// If 2 players have the same number of goals, the one player
// with less shots on targets is ranked higher.
const result = flow(
  partial(orderBy, __, ["goals", "shots"], ["desc", "asc"]),
  partial(take, __, 3),
  partial(map, __, "player")
)(players);
```

**NOTE**: this is perhaps an oversimplification of `_.partial` and really geared towards explaining our particular problem at hand. Notably there's an improved version we could achieve in this case by using `_.partialRight`, but I decided to skip it for this post. There's an example in this answer that I posted on [StackOverflow](https://stackoverflow.com/a/42368281/1446845) if you are interested.

Our bundle still looks ok

```js
-> du -sh build/js/*.js
 32K    build/js/1.chunk.js
4.0K    build/js/app.js
```

But the implementation itself does not seem too much of an improvement to what we had when using our arrow functions.
We can certainly do better. Ideally we would want Lodash to take care of partial application without us having to be so explicit in **how** to do that, nor do it for every method.
To do so we need a different version of Lodash, Lodash/fp.

## STEP 6: Meet Lodash/fp

Lodash provides a version that supports partial application out of the box for every method. Along with other features such as rearranging the arguments so that the data is passed as the last argument of each method rather than being the first, Lodash/fp will allow us to get where we want.


Let's first install the "regular" version of Lodash exported as Node.js modules. This actually contains the functional version version of the library, which is [missing in Lodash-es](https://github.com/lodash/lodash/issues/3247)


```sh
npm i --save lodash
```

We then change the import to reflect that:

```js
// import { flow, orderBy, take, map, partial } from "lodash-es";
import { flow, orderBy, take, map, partial } from "lodash/fp";
```



And we can finally change our transformation to leverage partial application provided out of the box:

```js
git checkout 9ecd0acd4b40d20ce1de7bfea83b62a60b6868f6
import { flow, orderBy, take, map, partial } from "lodash/fp";

const result = flow(
  orderBy(["goals", "shots"], ["desc", "asc"]),
  take(3),
  map("player")
)(players);
// > > Array(3) [ "Sergio Agüero", "Mohamed Salah", "Sadio Manè" ]
```
We run our code, and - again - we have the result we want.
We then check the size of our bundle and....

```js
-> du -sh build/js/*.js
 84K    build/js/1.chunk.js
4.0K    build/js/app.js
```

It's clearly gone back to include the whole library code!
The reason is the way we import Lodash methods. Unfortunately since we are not using `Lodash-es` anymore Webpack can't tree shake named imports.

## Step 7: Switching imports

The solution is to change them to be default imports.

```js
import flow from "lodash/fp/flow";
import orderBy from "lodash/fp/orderBy";
import take from "lodash/fp/take";
import map from "lodash/fp/map";

const result = flow(
  orderBy(["goals", "shots"], ["desc", "asc"]),
  take(3),
  map("player")
)(players);
// > > Array(3) [ "Sergio Agüero", "Mohamed Salah", "Sadio Manè" ]
```

```sh
-> du -sh build/js/*.js
 52K    build/js/1.chunk.js
4.0K    build/js/app.js
```

As you can see we have trimmed down our bundle again. Although it's not as small as it was before at 32K, we're really importing only what we need.

## Conclusion: Moving to lodash/fp. Is it worth it?

So, should you move to using pipes rather than chaining and convert your imports to use `lodash/fp`? As everything in programming (or in life!) the answer is only one: it depends.
Let's compare our original, chained version:

```js
const result = chain(players)
  .orderBy(["goals", "shots"], ["desc", "asc"])
  .take(3)
  .map("player")
  .value();
```js

To the final one using Lodash/fp:

```js
const result = flow(
  orderBy(["goals", "shots"], ["desc", "asc"]),
  take(3),
  map("player")
)(players);
```

As you can see the difference in syntax is minimal.

As we have seen we can trim down our bundle size by not importing the entirety of Lodash, but if we are to use the `fp` version of Lodash we will perhaps have a slightly bigger bundle size (although smaller than the full package import) and we will lose the very handy feature to use named imports (import { flow, orderBy, take, map, partial } from "lodash-es") while still supporting tree-shaking.

There's one big advantage though where the functional version wins hands down.
Apart from more subjective stylistic preferences adopting pipes instead of chaining will allow us to intersperse the usage of lodash methods with *our own functions*.
For example we could extract the first two steps of the pipe and assign them to a const:

```js
const top3 = p =>
  flow(
    orderBy(["goals", "shots"], ["desc", "asc"]),
    take(3)
  )(p);


const top3names = flow(
  top3,
  map("player")
)(players); // logs 3 players' names


const top3totalGoals = flow(
  top3,
  sumBy('goals)
)(players); // 56
```

This way we can find a meaningful name for and reuse the `top3` function elsewhere. In this case `top3` contains only lodash methods, but of course we're not limited to them. As long as the new function receives data in and returns data out we'll be good to go.

My personal opinion is that moving to pipes + lodash/fp is worth most of the times and is defintely worth trying if you're a heavy Lodash user.

## Useful links

- [Lodash FP guide](https://github.com/lodash/lodash/wiki/FP-Guide)
- [3 Lodash functions you should be using in your JavaScript](http://www.scottmessinger.com/2015/05/19/functional-programming-with-lodash/)
- [Why chaining is a mistake](https://medium.com/making-internets/why-using-chain-is-a-mistake-9bc1f80d51ba#.dcv5qk552)
- [Stack Overflow's "how do you chain functions using Lodash](https://stackoverflow.com/questions/35590543/how-do-you-chain-functions-using-lodash/42368281#42368281)
