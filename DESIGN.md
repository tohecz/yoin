
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
    \usepackage{yoin}
    \yoinSetup{
      defineflags = {aaa,bbb}, % All flags and tags have to be defined, to avoid unexpected typos
      definetags = {pdflatex,lualatex},
      onlyflags = {aaa} % We process only `joinshells` flagged `aaa`, not the other ones
    }
    
    ...
    
    \yoinAdd{JohnDoe}[tag=pdflatex]
    \yoinAdd{JoshSoe}[tag=lualatex]
    
    ...
    
    \begin{yoinshell}[flag=aaa]
      \RunForEach[onlytag=pdflatex]{pdflatex \BaseName} % compile articles that have to be compiled using pdflatex
      \RunForEach[onlytag=lualatex]{lualatex \BaseName} % the same for lualatex
      \AutoRunForEach[onlytag=pdflatex, engine=pdflatex] % the same as above using the "auto" feature
      \AutoRunForEach[onlytag=lualatex, engine=lualatex]
      \Run{pdflatex --jobname=\JobName-1 "\PassOptionsToPackage{subprocess}{yoin}\noexpand\input{\JobName}}"}
        % run this document, with jobname set to "issue-1", with "subprocess" passed to the package so that yoinshell blocks are ignored
      \AutoRun[suffix={-1}, engine=pdflatex]
        % the same as above using the "auto" feature (in autorun, "subprocess" will be passed automatically)
        % it's also possible to add [prependmacros={\PassOptionsToClass{bla}{blabla}}] or whatever
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
    \yoinForEach{\item
      \Property{title}
      \IfPropertySet{authors}{by \Property{authors}}{}
      on page \Property{firstpage}}
    % macro \Property and its friends shall be expandable
    \end{itemize}
    
    \joinPrintArticles[cleardoublepage,openany,setpagenumber]
    
    \end{document}
