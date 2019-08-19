Spreading props on a React Component is a very common pattern that I picked up very easily and loved from day 1.
With time though I learnt to understand the implications of using the spread operator in different contexts and came to the conclusion that most of the time spreading props in React is best avoided. Let's see why.

## I stopped spreading props... but not quite

> Use spread props sparingly

First of all I want to start with a clarification. The title of this post is misleading on purpose (read: clickbaity). Props spreading has its fair share of good use cases and is an expressive and concise pattern.

A good scenario to demonstrate how props spreading can really shine is [HOCs](https://reactjs.org/docs/higher-order-components.html). Let's look at this example.

```js
const withDouble = Comp => props => (
// We could wrap any type of Component here so we just pass all props down as they are
<Comp {...props} value={props.value * 2} />
);

const SomeInput = ({ onChange, value, disabled }) => (
<input value={value} disabled={disabled} onChange={e => onChange(e.target.value)}/>
);

const Doubled = withDouble(SomeInput);

const MyCal = () => {
const [val, setVal] = React.useState(0);

return (
<>
<SomeInput value={val} onChange={setVal} />
<Doubled value={val} disabled />
</>
);
}
```

'MyCal' is a bare-bones, extremely limited calculator that only doubles the input we type on a field.
In this case we use 'withDouble' as a generic wrapper that can wrap and enhance any component.
As such it must remain unaware of the props that it will forward down the tree.

Being able to spread props like so '<Comp {...props} />' is really powerful because we are free to enhance any component we might have. In the example above we can see that passing 'disabled' later on will actually work eactly for that reason.

```jsx
<Doubled value={val} disabled /> // this prop will be forwarded to `SomeInput` that will render a read only field
```

All great so far.

## Beautiful is better than ugly.
Let's look at how 'SomeInput' is implemented though.

```js
const SomeInput = ({ onChange, value, disabled }) => (
<input value={value} disabled={disabled} onChange={e => onChange(e.target.value)}/>
);
```

Now that we're in love with our new skill of spreading props, we might be tempted to do this:

```js
const SomeInput = ({ onChange, ...props }) => (
<input {...props} onChange={e => onChange(e.target.value)}/>
);
```

If we try that the component will work just the same. The problem we just introduced may not be apparent for now, but we have lost control over which prop our underlying 'input' will receive. Try this and you will see that 'randomProp' will be happily forwarded to '<input />' itself.

```jsx
<SomeInput onChange={alert} randomProp={2} />
```

Had we kept our original implementation the stray property would just have been ignored.

```jsx
const SomeInput = ({ onChange, value, disabled }) => (
<input value={value} disabled={disabled} onChange={e => onChange(e.target.value)}/>
);

<SomeInput
onChange={alert} // known props, it will be passed down
randomProp={2} // unknown one, ignored
/>
```

While this might seem simplistic problems like these become more and more common as the size and complexity of the codebase grow. Applying layers and layers of Components that just pass down props without any sort of check will make it very hard to follow where data flows and which attributes are applied where.

## In the face of ambiguity, refuse the temptation to guess.
Looking at the latest example makes the case for using a type checker, to help flag non-existing props.
Actually introducing type definitions on an existing codebase with spreads all over the place is not the most pleasing experience either.
If you're into TypeScript or Flow try writing a type def this:

```jsx
const MyComp = ({
type,
value,
...rest
}: Props) => (
const className = type === 'round' ? 'Btn--round' : 'Btn';

return (
<Actionable className={className} {..rest}>{value}</Actionable>
)
)

type Props = {
type?: 'round',
value: React.Node
// and??
}
```

Guessing 'value' and 'type' is quite straightforward. What about the 'rest' though? How should the shape look like?
We either become sloppy and allow 'any', which makes me question why we're even trying to type this thing, or we have to open the 'Actionable' implementation, check how props are handled there and hope that there's not another spread there (which is highly possible) otherwise we'll have open yet another file.

Once done that I would also check all instances of 'MyComp' to make sure random or outdated props are not passed by mistake. If this sounds tedious it's because it is.

Let's compare it to this other implementation.

```jsx
const MyComp = ({
type,
value,
colour,
size,
onClick,
onHover
}: Props) => (
const className = type === 'round' ? 'Btn--round' : 'Btn';

return (
<Actionable
onHover={onHover}
onClick={onClick}
className={className}
colour={colour}
size={size}>
{value}
</Actionable>
)
)
```

While we can't be 100% sure about every single type in this list of props we can do a lot without looking any further.

```tsx
type Props = {
type?: 'round',
value: React.Node,
colour: string,
size: string | number,
onClick: () => void,
onHover: () => void
};
```

While this is not perfect, it's miles better than what we have above. I would even say this next lousy effort is better than the first! At least we're listing out all the acceptable props!

```tsx
type Props = {
type?: any,
value: any,
colour: any,
size: any,
onClick: any,
onHover: any
};
```

This concept might seem specific to typings, but I think very well represents the difference in terms of cognitive effort required when reading code written in one way or the other.

## Explicit is better than implicit.
In conclusion, while spreading props is a powerful pattern we must be aware of its drawbacks and conscious that this technique has its place and merits, but is certainly not a silver bullet.

Having props listed out clearly helps us communicate intent and capabilities of our functions. Spreading them can be used to serve a purpose but should never come at the cost of readability or safety.

## References
With time I saw that the pitfalls of spreading props are actually documented in more than one official piece of documentation.

> Spread attributes can be useful but they also make it easy to pass unnecessary props to components that don’t care about them or to pass invalid HTML attributes to the DOM. We recommend using this syntax sparingly.
[React docs](https://reactjs.org/docs/jsx-in-depth.html#spread-attributes)

- The first quote at the beginning of the article is from [Airbnb's JavaScript Style Guide](http://airbnb.io/javascript/react/).

- There's more than one reference in this article that comes from the [Zen of Python](https://www.python.org/dev/peps/pep-0020/).
