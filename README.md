What we really should reconsider in React programming is whether we always need
to rely on reactive state. I am quite confident that using non-reactive state
in React components will fail code review in most of the software companies -
but why ? - because it's non-reactive...

But in fact it's the non-reactive state that we should always prefer over the
reactive counterpart - of course when applicable. The first reason for that is
that the non-reactive state could be just plain Javascript which so simple and
generic. Consider this excellent example:
```js
let inputValue = ''

const TextInput: React.FC = (props) => {
  return (
    <input 
      {...props}
      defaultValue={inputValue}
      onChange={(event) => inputValue = event.target.value} 
    />
  )
}
```
Here we rely just on the fact that an "uncontrolled" input element has an
internal state, so the external state does not need to induce a reload, only
keep the state reference. In fact here we discover a general rule: we could
always use a non-reactive state when the value changes between mounts and not
when the component is mounted.

Another reason is that non-reactive state is so unbiased and unopinionated,
because state is basic to Javascript and to any computer language in general.
In fact it is the reactivness that is so opinionated: consider shared-state
libraries like: flux, recoil, mobx, redux, react-query, etc. - they all differ
in core approaches to reactivness.  Here we are offered the possibility to just
use pure imperative code, without the eternal battles of which state library to
use.

Third reason is very straightforward - non-reactive state is faster. Do not
reload when we don't have to. It is a simple optimization strategy - a very
cheap one.

Now let us see things in another dimension, should the state be local or
global. This is actually a vertical thing to reactivness and will yield some
basic guideline on how to actually express state in code. The simple imperative
code is global by definition when written on the top of a js file. This is how
js modules work, the code at the top-level is just a factory function for this
module. If you export a state from a module, every import statement will yield
the same reference, not a copy of it. If you don't believe me go and check
yourself:
```js
// file global.js

let globalNumber = 1;

export { globalNumber }

// file a.js

import { globalNumer } from './global'

globalNumber++

// file b.js

import { globalNumer } from './global'

console.log(globalNumber) // '2', and not '1'
```

So now introduce the "state table" to have a basic guideline on how to implement the state.

|        | Non-reactive | Reactive                                 |
| ---    | ---          | ---                                      |
| Local  | `useRef`     | `useState`                               |
| Global | `new Map`    | use shared-state library or `useContext` |

The most convenient way to implement a global non-reactive state would be a
`Map` actually. Here we can have an arbitrary key to index different state
values, just as we would have in any shared-state library like *react-query*
for example.

> Tip: Use `JSON.stringify` to "hash" keys.

The take-away message is that by applying non-reactive state we can evade
picking a shared-state library for longer, or not using it all. Anyway even is
the choice is inevitable after all, by understanding the mechanics of state in
React - we can make this choice much more educated.
