Streams and higher order functions
==================================

The usual functions are named:
- `map`
- `flatMap`
- `reduce` (must be associative for the semantics to hold in parallel execution)
- `filter`
- `parallelPrefix` (parrallel scan)

Creating streams
----------------

Simple:
- `Stream.of(2,3,4,5,6)`.
- `IntStream.range(0, 10_000)`
- `random.ints(5_000)`
- `IntStream.iterate(0 /*First element */, x -> x + 1)`
- `Arrays.parallelPrefix(a, (x, y) -> /* operate on x and y */)`

For the built-in types, use special functional interfaces, e.g., don't use `Function<Integer, Bool>` but instead use `IntPredicate`; don't use `Function<Double, Long>`, use `DoubleToIntFunction`, etc.
This avoids the (huge) overhead of boxing/unboxing.
Some functions are not defined for the special built-in function types, `flatMap` being an example.
We can use the method `boxed` to make a stream o primitive values a stream of objects: `IntStream.range(0,10).boxed().flatMap(x -> Stream.of(x, x*x, x*x*x))`.

Parallelism
-----------

Simple: just call `parallel()` on you stream and let the compiler and runtime system work it's magic.
Just make damn sure you don't have side effects.

In depth: look at the [`Spliterator<T>`](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html) interface.
To implement your own, implement the abstract functions 
- `int characteristics()` (use one of the enums, e.g. `ORDERED`)
- `long estimateSize()` (how much remaining work in the iterator, basically)
- `boolean tryAdvance(Consumer<? super T> action)` (tries to work on remaining elements; returns true if there is at least one element left to work on)
- `Spliterator<T> trySplit()` tries to create a new spliterator with elements that get removed from the current spliterator.


A parallel task can then create a bunch of spliterators and compute them in parallel, sort of like creating a lot of Haskell sparks, using `parListChunk` and `parBuffer`.
The parallelizer uses work-stealing queues and a thread pool, so the spliterator need not be perfect, just helpful.
