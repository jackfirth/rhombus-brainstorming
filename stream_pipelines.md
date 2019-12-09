- Feature Name: Stream Pipelines
- Start Date: 2019-12-08
- RFC PR: [racket/racket2-rfcs#0000](https://github.com/racket/racket2-rfcs/pull/0000)

# Summary
[summary]: #summary

This RFC proposes adding *stream pipelines* composed of *transducers* and *reducers* to Rhombus.

Transducers are first-class objects that describe a one-pass, incremental, and synchronous transformation of an ordered sequence of values.

Reducers are similar to transducers, but instead of transforming the sequence into a new sequence, a reducer aggregates a sequence into a single result value.

# Motivation
[motivation]: #motivation

TODO

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

A *stream pipeline* is an operation on a sequence that combines zero or more *transducers*, representing *intermediate operations* on the sequence, with a single *reducer* representing a *terminal operation*.

Stream pipelines are constructed using the `transduce` method on sequences.

It accepts zero or more transducers and returns a partially-constructed pipeline, which has a single `into` method that accepts the reducer used as the pipeline's terminal operation and runs the pipeline.

```rhombus
widgets.transduce(
    // we only want red widgets
    filtering(w -> w.color == RED),

    // we want the heaviest widgets
    sorting(w -> w.weight, descending=true),

    // but only the 10 heaviest
    taking(10),

    // get the prices of the heaviest widgets
    mapping(w -> w.price))

  // finally, add all the prices together
  .into(intoSum)
```

The functions `filtering`, `sorting`, `taking`, and `mapping` all return transducers.

The `intoSum` constant is a reducer that adds a sequence of numbers together.

## Transducers

A *transducer* is an object that describes a way of transforming a sequence.

Such sequence transformations include mapping, filtering, batching, sorting, grouping-by-key, and many others.

Unlike plain functions, the transformations described by transducers have three key properties:

- They only make *one pass* over the sequence. This ensures that a transducer can be applied to a sequence that is too large to hold in memory.

- They describe an *incremental* transformation of the sequence. That is, rather than consuming the whole input sequence and then emitting the whole output sequence, a transducer emits outputs as soon as it can compute them based on the inputs it has consumed so far.

- They run *synchronously*. Transducers do not run in parallel, and they cannot consume and emit elements concurrently. When a sequence is transformed by a chain of multiple transducers, those transducers take turns executing. Later transducers are prioritized over earlier transducers. A transducer runs until it either outputs a value for a later transducer or requests an input from an earlier transducer, at which point the other transducer runs.

## Reducers

TODO

## Sequences

```
// transduce a sequence eagerly, returning a new sequence
xs.transduce(mapping(f)).immediately()

// transduce a sequence lazily without caching
xs.transduce(mapping(f)).onEachIteration()

// transduce a sequence lazily with caching
xs.transduce(mapping(f)).onFirstIteration()
```

## Composition

```
// compose two transducers together into a single transducer
mapping(f).then(filtering(p))

// stick a transducer in front of a reducer, returning a new reducer
mapping(f).thenInto(intoSum)
```

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

TODO

# Drawbacks
[drawbacks]: #drawbacks

TODO

# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives

TODO

# Prior art
[prior-art]: #prior-art

TODO

# Unresolved questions
[unresolved-questions]: #unresolved-questions

TODO

# Future possibilities
[future-possibilities]: #future-possibilities

TODO
