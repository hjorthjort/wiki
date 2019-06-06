# Key Bindings

Toggle different views by pressing any of the following keys:

**View Commands**:

-   `t` - display the (somewhat) pretty K **t**erm.
-   `c` - display current **c**onstraints.
-   `k` - display `<k>` cell.
-   `b` - display **b**ehavior tree.
-   `s` - diaplay **s**ource code.
-   `e` - display **e**vm specific module.
-   `m` - display **m**emory cell.
-   `d` - display **d**ebug cells (see toggling debug cells below).
-   `r` - display applied K **r**ule.
-   `z` - display **z**3 feedback from attempted rule application.
-   `Up/Dn` - scroll view **up** and **down**.

**Navigation Commands**:

-   `n`       - step to **n**ext opcode
-   `p`       - step to **p**revious opcode
-   `Shift+n` - step to **n**ext k term
-   `Shift+p` - step to **p**revious k term
-   `Ctrl+n`  - step to **n**ext branch point
-   `Ctrl+p`  - step to **p**revious branch point
-   `NUMBER`    - choose branch in behavior tree with that number

**Toggling Debug Cells**:

The following commands are prefixed with `:` (and are typed at the bottom of the interface).
It's possible to toggle the debug cells view for specific cells, which prints out the JSON representation of the given cells.
Remember, you must turn on the **d**ebug cells view to see these (above).

-   `:show ethereum.evm.callState.gas` - show the contents of the `<gas>` cell in the **d**ebug cells view.
-   `:hide ethereum.evm.callStack.pc`  - hide the contents of the `<pc>` cell in the **d**ebug cells view.
-   `:omit   gas pc` - omit the contents of the `<gas>` and `<pc>` cells in the **t**erm view.
-   `:unomit pc programBytes`  - unomit the contents of the `<pc>` and `<programBytes>` cells in the **t**erm view.

# Pretty printing

KLab uses the generated K AST, which can look unwieldy die to its structuring and naming practices.
It is possible to make a language construct display in a certain way by adding a rule for it in one of the KLab files.
Currently that file is `lib/kast.js` (2019-06-06).
Currently, the place for adding prettier rules (for example, making `_+Int_` display as `+`) is in the lists `infix`[^2] and `tokenMap`[^1] in that file.

[^1]: <https://github.com/dapphub/klab/blob/2be2c339dd0df009dc4976799b2ab8aede230e75/lib/kast.js#L249>

[^2]: <https://github.com/dapphub/klab/blob/2be2c339dd0df009dc4976799b2ab8aede230e75/lib/kast.js#L262>
