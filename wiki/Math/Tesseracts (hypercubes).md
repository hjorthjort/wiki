Construction
============

To make a tesseract of any dimension:

1. Start with a point and 1 dimension. This is a 1-tesseract. Set `n := 1`.
2. Add a new dimension, move 1 unit in that dimension, and copy the existing tesseract there.
3. Join up corresponding points (i.e., every corner with the new copy of that corner).
4. You now have a `n+1`-tesseract. Set `n := n + 1`
