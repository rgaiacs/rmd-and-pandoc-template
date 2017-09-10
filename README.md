This is a write version of my presentation at the R user group at the University of Manchester.

## Motivation

Meet Alice and Bob.

Alice and Bob are researchers. Sometimes they have **awesome** ideas.

And sometimes they collaborate in an shared idea.

When they collaborate,
they create something concreate faster than when they work alone.

Because collaborate with each other has been very productive,
in the last year they have committed to support [open access](https://en.wikipedia.org/wiki/Open_access), open data and [open source software](https://opensource.org/).

Among the open source software that Alice and Bob use,
their favourites are [R](https://www.r-project.org/) and [RStudio](http://rstudio.com/).

Alice and Bob love to write their research narrative in [R Markdown](http://rmarkdown.rstudio.com/). R Markdown helps very much to have the data visualisation in the correct place and always correct.

Unfortunately,
they waste time because none of the journals accept R Markdown documents as input format
which translate in Alice and Bob tailoring their narrative to the journal style.

At some point,
Alice and Bob were so frustrated with the time wasted tailoing the same narrative more than once
just because the publisher couldn't handle R Markdown that they decided to, as good researchers,
investigate a solution for the problem.
They wore their x-ray goggles to look how R Markdown works under the hood
and they discovered that part of R Markdown magic is powered by [Pandoc](http://pandoc.org/).

They also discovered that Pandoc allow users to provide [custom templates](http://pandoc.org/MANUAL.html#templates).
Now, Alice and Bob have a hypothesis: **can they use Pandoc's custom template to save time?**

## Hypothesis Investigation

Pandoc's documentation says

> A custom template can be specified using the `--template` option.
> You can also override the system default templates for a given output format `FORMAT`
> by putting a file `templates/default.FORMAT` in the user data directory.

It also says

> `--data-dir=DIRECTORY` Specify the user data directory to search for pandoc data files.
> If this option is not specified, the default user data directory will be used. This is, in Unix:
>
> ~~~
> $HOME/.pandoc
> ~~~
>
> (...)
>
> and in Windows Vista or later:
>
> ~~~
> C:\Users\USERNAME\AppData\Roaming\pandoc
> ~~~

Pandoc sets some variables by default when it reads the input document
and those variables can be used in the template by surrounding the variable name with the dollar sign, `$`.

With that information and al their R knowledge,
Alice and Bob wrote on their lab notebook

1. Clone the Git repository with the last paper.

   Run `git clone git@gitlab.com:rgaiacs/rmd-and-pandoc-template.git` at the command line.
2. Create the directory `_pandoc/templates` inside the directory `rmd-and-pandoc-template`.

   Run `cd rmd-and-pandoc-template`, `mkdir _pandoc` and `mkdir _pandoc/templates` at the command line.
3. Get one [LaTeX templates](https://www.overleaf.com/gallery/tagged/academic-journal) from [Overleaf](https://www.overleaf.com/)
   and store it inside `_pandoc/templates`.

   Visit [Overleaf's APA6 template](https://www.overleaf.com/latex/templates/your-apa6-style-manuscript/kngbbqpypjcq#.WbQ2Mzt0N4k),
   copy it and save as `_pandoc/templates/apa6.tex`.
4. Change `_pandoc/templates/apa6.tex` to make use of Pandoc's variables `title`, `author` and `body`. Also include some necessary LaTeX packages.
5. Add Pandoc arguments.

   ~~~
   output:
    pdf_document:
      pandoc_args: [
        "--data-dir", "_pandoc",
        "--template", "apa6.tex",
        "--output", "apa6.pdf"
      ]
   ~~~

6. Run Knit from the RStudio interface.

   RStudio log will show

   ~~~
   /usr/lib/rstudio/bin/pandoc/pandoc +RTS -K512m -RTS paper.utf8.md --to latex --from markdown+autolink_bare_uris+ascii_identifiers+tex_math_single_backslash --output paper.pdf --template /home/raniere/R/x86_64-pc-linux-gnu-library/3.4/rmarkdown/rmd/latex/default-1.17.0.2.tex --highlight-style tango --latex-engine pdflatex --variable graphics=yes --data-dir _pandoc --template apa6.tex --output apa6.pdf --variable 'geometry:margin=1in' 
   output file: paper.knit.md


   Output created: paper.pdf
   Error in tools::file_path_as_absolute(output_file) : 
     file 'paper.pdf' does not exist
   Calls: <Anonymous> -> <Anonymous>
   Execution halted
   ~~~

   Looks like that something went wrong
   but this is only because we change the output expected by RStudio with `--output apa6.pdf`.
7. Open `apa6.pdf` with your favourite PDF reader.
8. Repeat steps 3-7 with a different LaTeX templates.

   You can use [Overleaf's IEEE Photonics Journal Paper template](https://www.overleaf.com/latex/examples/ieee-photonics-journal-paper-template-example-submission/bsfjjfkdsjds#.WbUNBDt0N4k). **You will need to download the `IEEEphot.cls`.**

**Looks like that Alice and Bob can use Pandoc's custom template to save time!**

## `--data-dir` versus `$HOME/.pandoc`

Alice and Bob successfully verified that they could use `$HOME/.pandoc` instead of `--data-dir`
but they agreed that using `--data-dir` would make easy for reproduce their work
at the cost of duplicated files.

## HTML

## Appendix

### File does not exist

R Markdown/RStudio doesn't offer a great support to `output_file` argument, `--output` as Pandoc argument,
as explained by [Yihui Xie](https://yihui.name/) in [Stack Overflow](https://stackoverflow.com/a/28785774/1802726).
You can follow his suggestion and use "the undocumented `knit` hook".

### Mathematical expressions

As mentioned on [Pandoc's documentation](http://pandoc.org/MANUAL.html#using-variables-in-templates),

> To write a literal `$` in a template, use `$$`.