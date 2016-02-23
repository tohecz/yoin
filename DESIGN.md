
# Design decisions of the project

Before any implementation is done, some things have to be fixed from the design point of view.

# Objectives

- Allow journal issues and similar things (such as conference proceedings) to be compiled by a single-run multi-process compilation.
- Allow articles to be processed multiple times with different options for different purposes.
- Provided as a package, not a class.
- Written "fully" in expl3, using as little external packages as possible, namely:<br>
  - `pdfinclude`, `expl3`, `xparse` (let's see what else will be needed).
- High level of modularity, **still, reasonably working "out of the box".** (Let's not forget about this one!)
- Allow for (automatic?) consecutive page numbering between issues in the same volume of a journal.
- Use dtx for documentation.
- Allow parallel processes for the articles (is it possible? I do that now in linux, but that's using BASH).
- Allow various compilation chains for various articles (KV interface for `\addarticle`).
- Allow different configurations to be defined and switched between easily.
