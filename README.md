# funcadelic.js

[![Build Status](https://travis-ci.org/cowboyd/funcadelic.js.svg?branch=master)](https://travis-ci.org/cowboyd/funcadelic.js)

The Fundamentals of Functional Programming are Fun!

`funcadelic` takes the simple idea that a single function can operate on many
different data structures (typeclass oriented programming) and brings
it to JavaScript. Sure, there are a lot of FP libraries out there, but
this one is geared towards unlocking the magical powers of functional
programming while always maintaining a tangible and accessible
experience for JavaScript developers. Because if you're a JavaScript
developer today, you're already using already using most of the
structures in funcadelic already!

Quick example:

``` javascript
import { map } from 'funcadelic';

function double(i) { return i * 2; }

map(double, [1,2]); //=> [2,4]
map(double, {one: 1, two: 2}) //=> {one: 2, two: 4}
```

Notice how we used a _single_ function `map` to operate on both an
`Array` and an `Object`? That's because they're both instances of
the `Functor` typeclass which has a single operation called
`map`. Look familiar? It should because `map` is something you
probably do with Arrays all the time. Funcadelic just takes
this concept and lets you make it available to _any_ data type for
which it makes sense, not just Array. And as it turns out there are
tons of them out there!

And just remember: Don't be afraid of weird names like
Semigroup, Functor, Monad, Monoid and the like! They're just arbitrary
names that describe really useful concepts. Kinda like this one.

>
> Taskulamppu
>

Looks pretty weird right? Exotic, even. It's hard to parse, so it must
be a hard concept.

Turns out it's the [Finnish word for
flashlight](http://en.bab.la/dictionary/finnish-english/taskulamppu);
A strange name at first (to non-finnish ears) for a very useful and
easily understood tool.

## Type Classes

These typeclasses are currently contained within funcadelic.

* [`Semigroup`](#semigroup)
  + [`append(a, b)`](#appenda-b)
* [`Functor`](#functor)
  + [`map(fn, functor)`](#mapfn-functor)
* [`Foldable`](#foldable)
  + [`foldl(fn, initial, foldable)`](#foldlfn-initial-foldable)
  + [`foldr(fn, initial, foldable)`](#foldrfn-initial-foldable)


### Semigroup

A semi group is fancy name isn't it? It's just a data structure that
can be "mashed" together with itself. Instead of `Semigroup`, you
could call it `MashableTogetherableWithItselfable`, but that's a lot
to say every time. You mash two items in a `Semigroup` together with
the `append` function.

#### append(a, b)

> Combine two members of a semigroup into a single value of the same
> semigroup.

Arrays are classic example of `Semigroup`. What do you get when you
mash two arrays together and what do you get? Well another array of
course! `Array + Array => Array`

``` javascript
import { append } from 'funcadelic';

append([1,2], [3,4]) //=> [1,2,3,4]

```

But arrays aren't the only things that can be mashed together. Objects
can too!

``` javascript
append({name: 'Charles'}, {occupation: 'Developer'}) //=> {name: 'Charles', occupation: 'Developer'}
```

When you smush two members of a semigroup together, you always get the
same type back. In that example above: `Object + Object => Object`


### Functor

I used to think that Functor was some strange amalgamation of the words
"Function" and "Constructor". I don't think that any more because it
isn't true, although I still have yet to find a satisfying origin for
that word.

Nowadays, it's enough for me to reflect upon the fact that if George
Clinton were running an empire, instead of calling the provincial
governors "Satraps", he would have probably called them "Functors".

![Bootsy](bootsy.jpg)

Once you give up hope on trying to understand the etymology of the
word "Functor", you can focus on the idea that it represents some
kind of "container" or "context" with its own intrinsic structure
separate from the things that it contains. Using the `map` function,
you can swap out the values in a container without changing the
structure of the container itself.

#### map(fn, functor)

> Create a new Functor by appplying the function `fn` to the content
> of `functor`. The structure of the output Functor exactly matches
> that of the input.

Arrays are once again a classic example of Functors. You can use `map`
to change the values in an array without changing the structure of the
array itself:

``` javascript
import { map } from 'funcadelic';

map(i => i * 2, [1,2]); //=> [2,4]
```

The key here is that structure of the Array doesn't change. The input
to `map` is an array of two elements, therefore the output of `map`
_must be an array of two elements_. The content of that two element
array has changed, but the structure has not.

And guess what? Objects, are also Functors! you can map over
them too. When you map over an object, you change the value associated
with each key, but _the keys present remain the same_.

``` javascript
map(i => i * 2, {one: 1, two: 2}); //=> {one: 2, two: 4}
```

Again, notice how `map` preserves the structure of the object, even
though the values change. Both the input and the output are objects of
two keys "one" and "two"

### Foldable

Functors are all about changing the values contained within a
structure while at the same time preserving the shape of the structure
itself. When we map an array of 10 items, you get an array of 10
items.

Foldable structures are ones where you can consider all of the values it
contains and use them to compute a single value. You may have heard of
this before if you're familiar with OO design patterns as "visiting".
You have a visitor that is passed each value in the collection, and
then updates an accumulated value it maintains. Once the visitor has
visited every value, the final accumulated value is your result.

Foldable is the same way, Starting with an initial value, you "visit"
each piece of data inside of a structure, and then incorporate it into
the final value. For many `Foldables` [there is a
difference](#right-vs-left) between folding from "left" and folding
from the "right", although much of the time you don't need to consider
this.

#### foldl(fn, initial, foldable)

> Compute a single value, starting with `initial` from all of the
> values contained within `foldable` by applying `fn` to each element
> it contains starting with the "left most" value. For a discussion of
> "left most" see [Right vs Left](#right-vs-left)

For each element in `foldable`, `fn` is called with an accumulator
value (memo) and the element. The result of this call becomes the new
value for the memo. After all elements have been been folded in this way,
the final value of the memo is the return value of the fold.

Let's turn to our workhorse friend the Array for a great example of
`Foldable` that's worth a thousand words.

``` javascript
import { foldl } from 'funcadelic';

foldl((sum, i) => sum + i, 0, [1,2,3,4]) //=> 10
foldl((sum, i) => sum - i, 0, [1,2,3,4]) //=> -10
```

If this looks familiar, it is. Folding is exactly what happens when
you use
[`Array.prototype.reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

#### foldr(fn, initial, foldable)

> Compute a single value, starting with `initial` from all of the
> values contained within `foldable` by applying `fn` to each element
> it contains starting with the "right most" value. For a discussion of
> "right most" see [Right vs Left](#right-vs-left)

For each element in `foldable`, `fn` is called with an accumulator
value (memo) and the element. The result of this call becomes the new
value for the memo. After all elements have been been folded in this way,
the final value of the memo is the return value of the fold.

Like our friend `foldl`, Array gives a great demonstration of `foldr`.

``` javascript
import { foldr } from 'funcadelic';

foldr((sum, i) => sum + i, 0, [1,2,3,4]) //=> 10
foldr((sum, i) => sum - i, 0, [1,2,3,4]) //=> -10
```

In this case, `foldr` is roughly equivalent to
[`Array.prototype.reduceRight`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight)

##### Right vs Left

What "right" and "left" means is a bit mushy and varies from Foldable
to Foldable. In an array it means starting from the first value and
ending with the last and vice-versa.

In a tree structure, it might be the difference between starting with
the leaf nodes instead of starting with the root and moving downward.

For the most part, you can ignore the difference in a language like
JavaScript with eager evaluation. It's a pretty advanced topic, and if
you find yourself coming up against problems and wondering which to
use and this documentation doesn't help. Shoot me a line and we can
talk it over about how to improve it. When in doubt, `foldl`.

## Development

```
$ yarn
$ yarn test
```
