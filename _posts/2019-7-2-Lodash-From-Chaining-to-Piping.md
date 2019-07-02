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

[lodash-2]

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

[lodash-1]

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

[lodash-3]

Let's check the bundles once more.

```sh
-> du -sh build/js/*.js
 88K    build/js/1.chunk.js
4.0K    build/js/app.js
```

We now are back to having 2 files, our "main" (app.js) and our chunk. Unfortunately this last file is way bigger than before.


This comes down to the fact that we told Webpack to include the full library and our bundler did just that. As it turns out, chaining and tree-shaking in Lodash-es [can't be achieved together](https://github.com/lodash/lodash/issues/3298), unless you're willing to do some gymnastics https://github.com/lodash/lodash/issues/3298#issuecomment-341685354
Is there a way we can we have the expressiveness of chaining (or a similar pattern) without incurring in a penalty cost or having to maintain a clunky custom version of it? This dilemma can be solved, and we will see a possible solution in two acts.
