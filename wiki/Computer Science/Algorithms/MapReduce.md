Sort of like `map` and `reduce` (or `fold`) in many functional languages.
But not quite.

Rougly, a function `map_reduce` has the type

```hs
map_reduce mapper reducer :: -> [(k, v)] -> [(k', v')]

mapper  :: k ->  v  -> [(k', v')]
reducer :: k -> [v] -> [(k', v')]
```

Where, of course, the mapper and reducer need to work on compatible types.

Original paper: ["MapReduce: Simplified Data Processing on Large Clusters", by Jeffrey Dean and Sanjay Ghemawat](https://ai.google/research/pubs/pub62)

