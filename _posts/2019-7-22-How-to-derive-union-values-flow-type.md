---
layout: post
title: "How to to derive enum/union values in Flow without duplication"
---

A common pattern in Js when dealing with a pre-defined set of string values is to enclose them
in an object and use it as a map.

Say that we're dealing with an entity - let's say the request to join a group - that can be in 3 and only 3 possible states:

1. `approved`
2. `rejected`
3. `pending`

We might use these values straight into the code as they are:

```js
if (request.status === 'approved') {
  // ...
}

// elsewhere in the code
request.status === 'approved' ? showPage() : null
```

While this works it is error prone since we're missing the completion in our IDE (it's just a string),
plus it's really not future proof. If our backend will change the string for approved to be `confirmed` or even just `Approved` we will suddenly have to grep our codebase to change all instances of `approved` (hoping the very same string is not used elsewhere in totally unrelated contexts that might inadvertently be changed by our
search and replace).

The first remedy is generally to consolidate these string into an object and export it to use as a map.

```js
export const statuses = {
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
};
```

Now we can refactor our code:

```js
import { statuses } from '../../statuses.js';

//..
if (request.status === statuses.approved) {
  // ...
}
// ...

request.status === statuses.approved ? showPage() : null
```

We first of all get the nice to have autocompletion when typing out the code, plus we are sure that
had the api to change slightly and swap the string `approved` with `Approved` we'd need to change
one line versus as many as we have used that value.

If you're using a type checker in Js you will also have an even better guarantee when using these
statuses. The natural way to provide a type definition in this case would be an Enum.

To express this in Flow we'd use a union and the first try would perhaps be the following.

```js
/* @flow */

const statuses = {
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
};

type Status = 'approved' | 'rejected' | 'pending';
```

This absolutely works and there's nothing really wrong with it. We can check in the Flow repl:

```js
/* @flow */

const statuses = {
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
};
type Status = 'approved' | 'rejected' | 'pending';

const getStatus = (obj: { status: Status }): Status => obj.status;

// this will pass, all good
const ret0 = getStatus({ status: statuses.approved});

// this will throw an error, again, all good
const ret1 = getStatus({ status: 'nope' }); // ERROR: Cannot call `getStatus` with object literal bound to `obj` because string [1] is incompatible with enum [2] in property `status`.
```

You can try it [here](https://flow.org/try/#0PQKgBAAgZgNg9gdzCYAoVBjOA7AzgFzAIEN8BXXAU1zAF4wBvVMMYgBzYCc4A3SgEwBcYAOTsuvASIA0zMJ0oArShnwDhIhctVTZLNpWz8AltgDmGg0dNmRqAL4BudPgCeBsAGV8pCnVHi3Hz8ImAAPqJaKmoh4aJWJuYizpg4BGBmlPjevjT0ABRwAEaKwgxEPuS4wjlVYPYAlDWVfrQAfGDFigB0JFUpWHiECvgADP6Z2S24+eV9FMLzVLjdgZL8jc7AwGAAJACiAB4Gqvuc3JypQ-JZAIwTWbUUsxW5GthwBqGbYKjbYPsAEqAgDygOEAGFiNgPoQMMQYDAwAADSZPXDIsAIYz4AAWnRK0TAMBxlE4CLARTgZCMYHwcBRXUxRRUxAolAqnBsYAA2rcALpgYw0UxYAC2bFIxiKMA52LxYEMZDFvIATILTGAJAZOG4UUtkd1UEA).

While this works, we have to notice the repetition, as we're manually transcribing all the values
of the object to `Status` type. This might still be ok, since we're just using 3 values, but what if the values had to increase to 10? Or to 20?
Keeping the type def up to date would become tedious and error prone.

The solution is to use `$Values` and `typeof` to extract the values:

```js
/* @flow */

const statuses = {
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
};
type Status = $Values<typeof statuses>;
```

If we try this we'd see a strange behaviour though:

```js
// this will pass, all good
const ret0 = getStatus({ status: statuses.approved});

// this will pass!! That's not good!
const ret1 = getStatus({ status: 'nope' });
```

You can try it [here](https://flow.org/try/#0PQKgBAAgZgNg9gdzCYAoVBjOA7AzgFzAIEN8BXXAU1zAF4wBvVMMYgBzYCc4A3SgEwBcYAOTsuvASIA0zMJ0oArShnwDhIhctVTZLNpWz8AltgDmGg0dNmRqAL4BudPgCeBsAGV8pCnTAAJABqxDBk1AA8bgZwUEQ+5FS4AHzOmDgEYGaU+N6+NPQAFHAARorCDPH5wnmJYPYAlDUJfrTJYKWKAHQkiWnAwGD4ABbGNAjGMDBgbMS4uNKsU1lwcPzpeIQK+AAM-tm5LbiFlb0UwmdJXeLcfPyN-YMjY2ATy7PzAISfYAAqw6QRDRsHBCGZVvxPhtMtsAIz7HK1CgnKqJDQggwieoNRxgIA).

If we ask our IDE the return type of our `getStatus` function we will see that it's actually a `string` and not
one of the members of our `Status` union type.

![_config.yml]({{ site.baseurl }}/images/2019-7-22-flow-1.png)

The reason lies in the fact that when we create an object with properties in Flow it is treated as a *sealed object*. From the docs (emphasis mine):

> When you create an object with its properties, you create a sealed object type in Flow. These sealed objects will know all of the properties you declared them with and *the types of their values*.

Also:

> But when objects are sealed, Flow will not allow you to *add new properties* to them.

First of all let's clarify what a sealed object is. From [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/seal):

> The Object.seal() method seals an object, preventing new properties from being added to it and marking all existing properties as non-configurable. Values of present properties can still be changed as long as they are writable.

This means we will not be able to add new keys to this object (it will throw an error), but we will be able to change the existing values.
In this case Flow will only be able to infer the type of the values, but not their literal values, since they will still be available to be changed later on in the code.

The trick to make Flow really extract these literal values into an enum is to freeze the object like so:

```js
const statuses = Object.freeze({
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
});
```

What `Object.freeze` will do is to do everything that `Object.seal` does plus making sure no key/value pair can be modified.

Again [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze):

> The Object.freeze() method freezes an object. A frozen object can no longer be changed; freezing an object prevents new properties from being added to it, existing properties from being removed, prevents changing the enumerability, configurability, or writability of existing properties, and prevents the values of existing properties from being changed. In addition, freezing an object also prevents its prototype from being changed. freeze() returns the same object that was passed in.

Finally now Flow will be able to properly extract the values (and not just their primitive types). Let's test it:

```js
const statuses = Object.freeze({
  approved: 'approved',
  rejected: 'rejected',
  pending: 'pending'
});

type Status = $Values<typeof statuses>;

const getStatus = (obj: { status: Status }): Status => obj.status;

// this will pass, all good
const ret0 = getStatus({ status: statuses.approved});

// this will throw an error again, exactly as we want
const ret1 = getStatus({ status: 'nope' });  //  ^ Cannot call `getStatus` with object literal bound to `obj` because string [1] is incompatible with enum [2] in property `status`.
```

We are now sure that we can extend `statuses` to support more key/values without duplicating the values in our union type.

## References

- Flow docs on [object type inference](https://flow.org/en/docs/types/objects/#toc-object-type-inference)
- Another clear and short explanation of [Object.freeze vs Object.seal](https://stackoverflow.com/a/21402679/1446845)



