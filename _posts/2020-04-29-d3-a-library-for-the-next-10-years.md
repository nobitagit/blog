---
layout: post
title: D3: a JS library for the next 10 years?
---

D3 has been the go-to library for in browser data visualisation for many years by now.
While JavaScript libraries are notorious for being extremely transient and easily replaced by newer, shinier alternatives, for some reason this does not seem to apply to D3.

## Some history

The first published version I can trace by searching between npm and Github releases is [1.24.1](https://github.com/d3/d3/releases?after=v1.29.2) which is dated 2nd July 2011, although the first version ever seems to be from February that year, i.e. **over 9 years ago** at the time of writing. This means we're closing in to the first decade of d3.
In the JavaScript world this is probably the human equivalent of a century ago.

To give a bit of perspective, Backbone was created around that time, and so was AngularJS version 1.x (remember '$scope.apply()'?)
The JavaScript ecosystem has completely changed during this time, in terms of libraries, best practices and even language features.

Nevertheless, D3 is still here. And **it's more popular than ever**.

## The Present

![D3 on Npmtrends](https://dev-to-uploads.s3.amazonaws.com/i/o7unk9yroenasuwizbw9.png)

This chart is taken from [Npmtrends](https://www.npmtrends.com/d3-vs-chart.js-vs-highcharts) and shows the last 5 years of npm downloads of D3 compared to Chart.js and Highcharts.
D3 emerges as a clear winner, and this is not even considering the fact that many users are not downloading the full library (the entire d3 package), but only some of its modules, such as d3-array, or d3-scale.

## 6 reasons for the long-lasting success of D3

I can think of a few reasons why D3 is yet to be replaced by an alternative. In my view, they actually make it unlikely to be replaced any time soon.

### Stable

The biggest API change in D3 happened during the major upgrade from 3 to 4 as far as I know. From there on, the api has been pretty much the same.

### Thoroughly tested

D3 has an excellent test coverage and has been used in production for years now by thousands of developers. In fact D3 is almost...

### Bug free

Ok, this is an exaggeration. There's no such thing as a program with no bugs, and D3 is no exception.
Still, the number of issues is very limited, especially compared to competing charting libraries that have hundreds of open/unresolved issues.

This is a screenshot of the D3's main repo on Github, with a whopping 3 open issues.

![Issues on D3's main repo](https://dev-to-uploads.s3.amazonaws.com/i/vxwgzql7n81wabf3c0lt.png)

### Actively maintained
You can look at the latest version publish, or the [latest commit on Github](https://github.com/d3/d3/commit/7244e45e68af39c519f76667a03039d5b24dd453), or the planned features in the roadmaps. This makes it clear that the idea is to go on and keep D3 up to date and in line with modern JS.

### Modular build

One of the biggest criticisms towards D3 has historically been its staggering size. For years, including D3 on a page meant adding a great amount of kBs to your page. Since version 4 the library has been split into several standalone, dedicated modules. This means that you can include in your bundle only those part of the library that you actually leverage in your code.

The improvement in terms of size is remarkable. Let's visualise what this means. Here's the [composition of d3 version 3.5](https://bundlephobia.com/result?p=d3@3.5.17)

![composition of d3 version 3.5](https://dev-to-uploads.s3.amazonaws.com/i/hnfe28kvdf5p0x3qc9j3.png)

In this case adding D3 for a simple bar chart meant including totally unrelated stuff like functions to draw map projects (d3-geo), parsers and formatters for dates in a variety of locale-specific implementations, force layout helpers... In short, a lot of potentially useless code was added to the bundle.

Let's compare it to [the 5.x version](https://bundlephobia.com/result?p=d3@5.16.0)

![d3 5.x version composition](https://dev-to-uploads.s3.amazonaws.com/i/6w8zmhic9wtubzbljqh1.png)

This means that nowadays we can install only what's needed for the specific visualisation and leave the rest of D3 out of the bundle.

---

All these are valid points but wouldn't suffice to explain why D3 is still a relevant, desirable choice for anyone making visualisation. The missing one is...

### Philosophy

This is in my opinion the single most important reason of the list, and it's where all of the points above really come together in shaping a great library that will - in my opinion - stand the test of time.

> D3 is not a Data Visualization Library
>
> -- [Elijah Meeks](https://medium.com/@Elijah_Meeks/d3-is-not-a-data-visualization-library-67ba549e8520)

**D3 is not really a charting library**, it doesn't even have the concept of a chart. Rather, its goal is to provide the most common primitives to perform any type of mapping, mathematical operation or analysis on data. Like Lodash is the de-facto standard when it comes to data manipulation, D3 is the obvious choice when dealing with data analysis.

In short, no matter how JS will evolve, you'll still need to do the same computations to draw charts and to map raw data to x and y coordinates, or to easily map a range of values to a range of colours.

The basics of data visualisation and how to perform mathematical calculations are not going to change any time soon.
Since D3 operates at that low level, and in such a modular way, the reasons to find or even write an alternative library are very limited and not compelling enough.

For me, this fundamental approach is what sets D3 apart from any other solution out there, and the key reason for the long-lasting success of the library.

A lot of people seem not to be big fans of how D3 handles updating DOM (look at d3-selection) and it certainly can be quite intimidating. I am not going to discuss here about the D3 DOM api, or give any personal opinion, but again, the modularity of D3 means that you can always swap its selection methods with view libraries like React or Vue, and use D3 for calculation.

Some library authors have decided to do just that. Two examples of this approach are [Victory](https://github.com/FormidableLabs/victory/blob/b137e4aa3285c6646c83c507673842b545b26df4/packages/victory-core/package.json#L22-L26) and [VX](https://github.com/hshoff/vx/blob/master/packages/vx-hierarchy/package.json#L82)

## Conclusion

In conclusion, I think D3 is unlikely to be superseded by an alternative and, by the looks of it, it will remain the de-facto standard for the foreseeable future.
This does not mean it's perfect and that it comes with no disadvantages (one for all, the steep learning curve) but it's certainly still very much relevant, if not more than it ever was.

It also does not mean that every one that needs to draw a chart on a page need to learn it. But if you're serious about data visualisation and want to have full freedom when it comes to plotting data in a browser then **D3 is arguably going to be your best bet**.
