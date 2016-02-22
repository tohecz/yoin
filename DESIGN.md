
# Design decisions of the project

Before any implementation is done, some things have to be fixed from the design point of view.

# Objectives

- Allow journal issues and similar things (such as conference proceedings) to be compiled by a single-run multi-process compilation.
- Provided as a package, not a class.
- Written "fully" in expl3, using as little external packages as possible, namely:<br>
  - `pdfinclude`, `expl3`, `xparse`.
- High level of modularity, still, reasonably working "out of the box".
- Allows for (automatic?) consecutive page numbering between issues in the same volume of a journal.


