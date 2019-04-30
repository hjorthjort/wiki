`!` means evaluate to WHNF, much like `seq` does.
So `!n` is like `seq n n`
```hs
f x !y = x
unsafe = unsafePerformIO $ print "hi"
f 10 unsafe

```
prints:
```
"hi"
10
```
But `!` can be used in `data` declarations and pattern matches (requires `BangPatterns` extenstion):
```hs
data Foo = F !Int !(Maybe Int)
```

Whenever a `Foo` vaue gets evaluated to WHNF (like when pattern mathcing `Foo _ _`), so does its parameters.
