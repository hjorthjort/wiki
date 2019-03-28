This is my Everything-I-Know Wiki, my external brain, my repository of scattered but important knowledge.

The presentation format is unknown as of yet. Ideally, I would like to have a simple structure that will work forever, and not rely on a format like GitBooks or similar.

Organization
============

Content will be organized in directories, each directory representing a
category, subcategory, subsubcategory, etc. A directory may contain zero or
more files, each file being a topic. Each file will be a regular markdown file.
Soft links allows putting the same directory or file in different places in the
wiki. Hard links are a better way to point to the actual files.

Rendering
=========

**Initial plan**

A single page with all the Markdown files rendered in HTML (ideally with LaTeX
rendering, too, if there's a good converstion tool to HTML). In a left pane, a
tree of directories (openable?) with anchor links to each post.

Pros:
- Ctrl+F becomes a global search
- Easy to implement
Cons:
- Ugly


The main motivation for using Markdown is that it is portable and reads well in
source format. I want to be able have a wiki that is usable regardless of
whether it gets rendered or not. That said, the envisioned target format is a
web-page that looks a lot like a GitBook or wiki-page.

Open questions
==============

What's a good (robust) way to handle links? I expect to move things down into
subfolders, maybe rename folders, and so on. I want to do that without breaking links.
MVP: Broken link detector: program that scans the wiki for broken soft links.

More advanced solution: create some sort of structure for directory
intersections: Don't let one of the directories own it's subdirectory: when
another part of the wiki links to some subdirectory, the subdirectory is placed
in the intersection of the two linking places, rather than in one of them.

How to deal with math symbols? LaTeX would be nice ...
