---
layout: post
title: "Interpolating colours within a range in D3.js"
---

Colours can be a powerful visual hint in a data visualisation. 
A good rule of thumb though is to use them sparingly, only when they add something meaningful to the chart and [not just because they are pretty](https://youtu.be/AiD6etOB6qI?t=1m3s).

A good alternative to using many different colours is to use just a range of different shades of one or two colours across our entire dataset. This will highlight visually the differences in “weight” of each datapoint without distracting the user with an infinite rainbow of colours.
D3.js makes achieving this goal incredibly easy, allowing us to pass a colour codes to our `ranges`. We just need to pass the two colour codes we want to use as extremes and we will be good to go.

```js
import { scaleLinear } from 'd3-scale';

const ourScale = scaleLinear()
  .domain([0, 100])
  .range([
     '#63a6d6', // <= lower bound of our color scale
     '#124488'  // <= upper bound of our color scale
  ]);
```

D3.js will in turn interpolate the value we pass to our scale function to give us back the appropriate color code. Easy!
Here below a working JsFiddle to check how it works live.

<script async src="//jsfiddle.net/gleezer/v8k047yh/embed/js,html,css,result/dark/"></script>
