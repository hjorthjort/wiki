What happens when you run out of virtual memory?
================================================

https://utcc.utoronto.ca/~cks/space/blog/unix/NoSwapConsequence

RAM is basically full of anonymous pages (that can't be moved anywhere), and file-backed pages (which are just disk files cached to memory).
The anonymous pages contain other stuff, like allocated memory buffers, variables, etc., of running processes -- all the dynamic memory of the programs, pretty much.
The canonical example is all the memory of a web browser: shit-tonnes of dynamic memory but most of it kinda not needed right now because the tab is just open in the background.
File-backed pages often contain the code you're running and lots of the persistent data.

If you run out of virtual memory, any Unix system will start moving what it can out of RAM and onto disk.
That means file-backed pages get moved out.
That means your system will get incredibly slow, flitting back and forth moving things it really could need to disk all the time, while anonymous pages that you don't really need right now but that can't be moved stay in memory.

Swap
====

https://wiki.archlinux.org/index.php/Swap

You can check if/how much swap you have with

```
free -h
```
