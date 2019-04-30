Folds
=====

**Mnemonic***
- Left fold puts the initial value at the **left*** of the list.
  That means it's good for cases when you want to build a result from the start of the list to the end, with an initial element first.
  The accumulator goes to the **left** in the function, because that's the order it comes in the list.
  Example, adding variables to an environment: `foldl (\env x -> x `extend` env) emtpyEnv xs`
  For a non-associative operator, the parentheses go to the **left**.
- Right folds puts the initial value at the **right** of the list.
  That means it's good when you want the accumulator to play the role of the `nil` list at the end of the cons list.
  Foldr replaces the cons with the operator, and the nil with the initial element.
  So it's good for dealing with lazy lists where you can take from the top.
  For a non-associative operator, the parentheses go to the **left**.

  ```hs
  foldl op zero (a1:a2:a3:nil)` =
    ((zero `op` a1) `op` a2) `op` a3
  ```
  ```hs
  foldr op zero (a1:a2:a3:nil)` =
    a1 `op` (a2 `op` (a3 `op` zero))
  ```
