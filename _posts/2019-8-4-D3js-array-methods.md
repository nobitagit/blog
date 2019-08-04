---
layout: post
title: "Array methods in D3.js"
---

Arrays are the natural data structure for looping over data in JavaScript. While the native methods such as map, reduce, filter etc will provide a good base for pretty much any operation on arrays, many libraries and frameworks enrich the native set of functions with domain specific utilities.

[D3.js](https://d3js.org/), the de facto standard library for data visualisation in Js, is no exception. D3 actually provides [`d3-array`](https://github.com/d3/d3-array), an entire module of utility functions that covers many of the most common operations when dealing with data and visualisation.

Let’s see how we can leverage some of them, starting from the most commonly used ones.

## Installing and using the module the right way
First of all lets point out that it is strongly suggested installing and importing the `d3-array` module only, rather than the whole d3 module.

This means that rather than this:

```js
import d3 from 'd3';

d3.min([1,2,3]);
```

You will want to do this:

```js
import { min } from 'd3-array';

d3.min([1,2,3]);
```

This avoids downloading and bundling the whole d3 library. D3 has in fact moved to a fully modularised build since v3. This means we can install only the pieces we need, without incurring in a hefty penalty in terms of bundle size.

If you're curious to see which modules d3 exports, you can see them nicely listed in the [index.js](https://github.com/d3/d3/blob/master/index.js#L2).

---

To illustrate some of d3's array manipulation features let’s start with two dummy datasets, a sparse, unsorted array of random numbers (`data`) and an array of objects containing some songs along with my rating of each of them (`nestedData`).

```js
const data = [1, 3, 4.2, 22, 1.33, undefined, 21, 4, 3];
const nestedData = [{
rating: 5,
song: 'Song 2'
}, {
rating: 4,
name: 'Top Man'
}, {
rating: 4.5,
name: 'Clover Over Dover'
}, {
rating: 3,
name: 'Pressure On Julian'
}, {
rating: 4,
name: 'Yuko And Hiro'
}];
```

## `d3.min()`
Use it to find the lowest value inside an array.
Js provides a `Math.min()` operator but `d3.min()` will prove more flexible and forgiving, for example when our dataset is an array with holes (some positions have null or undefined value, just like our data array). In our case, here are the results:

```js
const dataMin = d3.min(data); // => 1
const nestedMin = d3.min(nestedData, item => item.rating); // => 3
```

Let’s compare the Js, no-lib version of the same code:

```js
const dataMin = Math.min.apply(null, data); // => NaN :(
const nestedMin = Math.min.apply(
null,
nestedData.map(item => item.rating)); // => 3
```

In the first case we are bitten by the sparse array, in the second we go through a certain degree of verbosity. The d3 versions are certainly safer, terser and more compact. Of course, we can cater for this particular case and fix the nasty NaN returned by the first method:

```js
const dataMin = Math.min.apply(
null,
data.filter(item => typeof item !== 'undefined')); // => 1
```

The implementation of `d3.min` can be found [here](https://github.com/d3/d3-array/blob/master/src/min.js). Read the next method for some common considerations about its usage in D3.

## `d3.max()`
Use it to find the highest value inside an array.
Quite unsurprisingly max is the opposite of the min method. The very same apply, so using it with our two arrays is just as easy as this:

```js
const dataMax = d3.max(data); // => 22
const nestedMax = d3.max(nestedData, item => item.rating); // => 5
```

The implementation of `d3.max` can be found [here](https://github.com/d3/d3-array/blob/master/src/max.js). Again, this method does not count `undefined` and `null` values, and it will safely skip over them. It will compute strings though, as we’ll see in a short while.

Just for fun I tried throwing a bit of everything to 'd3.max' and its diminutive counterpart 'd3.min'. Some (pointless?) tests include:

```js
d3.min(); // TypeError :(
d3.min(1); // undefined, make sure you pass an array!
d3.min(1, 2); // undefined, make sure you pass an array!
d3.min(null); // undefined
d3.min(['', 1, 2]); // => '' <= watch out for this one
d3.max(['', 1, 2]); // => 2
d3.min(['hey ho!', 1, 2]); // hey ho!
d3.max(['hey ho!', 1, 2]); // hey ho!
```

The biggest thing here — apart from the suggestion to make sensible datasets (i.e. do not copy the ones above) — is to be wary of empty values stored as empty strings as they are picked up and compared. Not that it’s that frequent, but anyway it’s good to know.
Comparing strings can actually be way more useful, though. We can for example find the darkest colour within a set.

```js
d3.min(['#15234C', '#5a5f6d', '#183b9e', '#3d9e18']); // "#15234C"
```

A practical example for this could be having a palette of colours and deciding to plot our data using the darkest colour as upper bound and the lightest as lowest bound. This will allow us to very easily create ranges of colours for our data visualisations.

This is a very powerful feature, and if you're interested in it, do check out my post on [Interpolating colours within a range in d3.js](https://nobitagit.github.io/blog/Interpolating-colors-range-d3js/).

## `d3.extent()`
Use it to find the lowest and the highest values in one function call. Returns an array.
Finding min and max values in an array is such a common operation in D3 that such functionality is grouped into one convenience method, `extent`.
Let's try it.

```js
d3.extent(data); // [1, 22]
d3.extent(nestedData, item => item.rating); // [3, 5]
```

This is exactly the same as the following code, but more concise:
```js
const dataExt = [d3.min(data), d3.max(data)]; // [1, 22]
```

With `extent` we are calling only one method rather than two. I thought that under the hood, `extent` was just a wrapper around `min` and `max`. It’s [not](https://github.com/d3/d3-array/blob/master/src/extent.js).
Arguably the most natural usage for extent is when calculating ranges and domain of our datasets, where we normally have to provide a minimum and a maximum number for computing a scale.

## `d3.sum()`
Use it to get a sum of all the elements of an array;

Again, as expected, it skips over null and undefined elements.

```js
d3.sum(data); // 59.53
d3.sum(nestedData, item => item.rating); // 20.5
```

This can be achieved quite easily with vanilla Js and reduce, but failing to check for null or undefined elements will spoil the party.

```js
data.reduce((acc, item) => acc + item, 0); // => NaN :(
nestedData.reduce((acc, item) => acc + item.rating, 0); // 20.5
```

## `d3.mean()` and `d3.median()`
Use them to find the mean and median values of an array.

We close today's overview with two very related methods, `mean` and `median`.
The meaning of mean and median can be easily mixed up, so I put these methods together in order to start with an explanation of their differences. Both concepts represent the average of a dataset, or better, the central tendency of a set of statistical scores.

The **mean** is the most widely understood and common expression of an average. It is calculated through the sum of all elements in the dataset divided by the number of them, or - translating this to Js - t*he sum of the items of the array, divided by its length*.

To give an example let’s create a new array for this purpose.

```js
const arr = [1,2,3,4,5,8,10];
let sum = arr.reduce((acc, i) => acc + i, 0);
sum / arr.length // 4.714285714285714
```

The **median** is instead that number that sits in the middle of our dataset. Our median will be the value where half of the items will be sitting before it, and the other half of the values will come after it.

There’s no easy mathematical formula for calculating the median. Hence, calculating its value for a dataset without any library is going to be a tougher challenge, more so because we have to account for different behaviours when our dataset length is even and when our dataset length is odd.

Given this sorted array:
```js
const data1 = [1,2,3,4,5];
// ----------------^
```
Our median will be 3, as that is the value that sits in the middle “splitting” the array in two halves.

In this case though:
```js
const data2 = [1,2,3,4,5,6];
// -----------------^ uhm... 3 or 4?
```
There’s **not** a single value splitting the array, so we can say that both 3 and 4 sit in the middle. To find the median in this case we have to sum these 2 values and divide by 2 (that is, we will apply the formula we used to find the mean). Our median in this case is 3.5.
Luckily D3 has this functionality built in, and we can leave the library do the math for us.

So, going back to our original datasets we can find mean and median very quickly.
```js
d3.mean(data); // 7.44125
d3.median(data); // 3.5
d3.mean(nestedData, i => i.rating); // 4.1
d3.median(nestedData, i => i.rating); // 4
```

The results above highlight a very interesting case. While the difference between mean and median of our set of songs is negligible (4.1 and 4), we can’t say the same when doing the same computations for our list of numbers. Why is that?

The reason is that the mean is easily influenced by spikes in the dataset. One single very low or very high value can easily skew our result by a significant amount. The median, instead, will not follow the same destiny, giving us an arguably more accurate idea of where the mid point is and what the central tendency actually looks like.

To illustrate this, let’s add one value to our previous array and test it out again with the help of d3:

```js
const data3 = [1,2,3,4,5,1500000];
d3.mean(data3); // 250002.5
d3.median(data3); // 3.5
```

Now it’s very clear how a subtle difference between the two concepts may actually translate to striking differences in real world scenarios.
This means that when we need to represent the average of a dataset we first of all have to understand “which average” we want our users to see, or which one is more meaningful when telling our story through a visualisation.

There’s no single answer to this question and that’s a very good reason to have well clear in mind what exactly are the purposes of these two apparently similar methods that D3 provides.

---

This is just a small part of methods that d3 provides to act on arrays and maps, and certainly the ones I use the most. Many more are exported though, as you can see from the [source code](https://github.com/d3/d3-array/blob/master/src/index.js), and it's good to know that there might be a function in there that is tailored to our needs.
