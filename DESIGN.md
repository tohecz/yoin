
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

# Idea for UI

    % file issue.tex
    \usepackage[onlyflags={aaa,bbb}]{yoin}
    
    ...
    
    \addarticle{JohnDoe}[tag=pdflatex]
    \addarticle{JoshSoe}[tag=lualatex]
    
    ...
    
    \begin{yoinshell}[flag=aaa]
      \RunForEach[onlytag=pdflatex]{pdflatex \BaseName}
      \RunForEach[onlytag=lualatex]{lualatex \BaseName}
      \AutoRunForEach[onlytag=pdflatex, engine=pdflatex]
      \AutoRunForEach[onlytag=lualatex, engine=lualatex]
      \Run{pdflatex --jobname=\JobName-1 "\def\noexpand\yoinnoshell{}\noexpand\input{\JobName}}"}
      \AutoRun[suffix={-1}, engine=pdflatex, prependmacros={\def\noexpand\yoinshell{}}]
    \end{yoinshell}
    
    \begin{yoinshell}[flag=bbb]
      \Run{gs %
        -sOutputFile="\JobName-bw.pdf" %
        -sDEVICE=pdfwrite %
        -sColorConversionStrategy=Gray %
        -dProcessColorModel=/DeviceGray %
        -dAutoRotatePages=/None %
        -dCompatibilityLevel=1.4 %
        "\JobName.pdf\Space" < /dev/null}
    \end{yoinshell}
    
    ...
    
    \begin{document}
    
    \section{Foreword, colophon, whatever}
    
    ...
    
    \section{Table of Contents}
    
    \begin{itemize}
    \yoinForEach{\Property{title} by \Property{authors} on page \Property{firstpage}{
    \end{itemize}
    
    \joinSetPageNumber[]
    
    \joinPrintArticles[cleardoublepage,openany]
    
    \end{document}
