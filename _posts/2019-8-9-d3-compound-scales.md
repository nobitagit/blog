---
layout: post
title: "Compound scales in D3.js"
---

Scales are an extremely flexible feature of D3.js.
Even though in many cases our need is just to have a function that maps one value in the `domain` to one value in the `range`, sometimes it can be handy to have a mapping from one to many values.

Suppose for example that we need to map one input to a set of values such as color and radius to build a simple scatterplot where the dots have radius and color depending on the input value from the dataset.

In these case being able to have a compound scale is what we need, and thankfully D3 provides this feature out of the box. Just pass two (or more) objects to the range function and d3 will do the rest.

Here’s a simple example:

```js
const myScale = scaleLinear()
  .domain([0, 100])
  .range([
    {color: '#4c0080', radius:'15px'},
    {color: '#43a1ad', radius:'150px'}
  ]);
```

This code will return a function that will convert our values (in this case from 0 to 100) to an object with all the values appropriately interpolated.

For example:

```js
myScale(41); // => {color: 'rgb(72, 66, 146)', radius: '70.35px'}
```

A simple yet super-handy feature, especially considering that we don’t need to specify the interpolator because the library will figure it out for us. On top of that we are not tied to single level objects, but deep objects will work just the same.


