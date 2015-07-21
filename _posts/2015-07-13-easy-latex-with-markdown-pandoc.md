---
layout: post
title: Easy LaTeX with Markdown and Pandoc
categories:
  - tutorial
tags:
  - latex
  - markdown
  - pandoc
---

Best of both worlds; power and beauty of LaTeX with the simplicity of Markdown.

<!--more-->

I remember writing my very first LaTeX report in high school, learning the most
useful commands, what goes in the preamble, including other documents, getting
the figures to show up etc.

It took some time to figure out but... wow, how it looked good!

I really like to write in [Markdown][mark] though. It's very clear to me without
being verbose and it's used a lot: reddit, Stack Overflow, GitHub etc.

So I went searching for how I could combine the best of both worlds. The power
and beauty of LaTeX with the simplicity of Markdown. That's how I found
[Pandoc][pandoc] and learned it's possible to mix the two markups.

## Setup
To get this up and running you'll need LaTeX ([Windows][win], [OS X][osx],
[Linux][linux]) and Pandoc.

Pandoc is available for Homebrew: `brew install pandoc`. Your favorite package
manager probably has Pandoc as well.

## A simple document
Let's try out Pandoc with a simple single-file setup. We'll write a Markdown
file mixed in with some LaTeX goodies and convert it to PDF.

Create a Markdown file and name it something. `input.md` will do. Now let's put
something in it so we have something to play with.

```
# Section
This is our first section.

Another paragraph.

We can use Markdown for figures.

![A Markdown logo](markdown.png)

Markdown for lists

* One
* Two
    * Nested one
* Three
    1. Numerated list
    1. No need to specify number

We can even inline math: $y = ax + b$.  
How about displayed equations:

$$
y = -2.2x + 0.5
$$

## Subsection
Just use Markdown to define sections and structure of the document.

Let's finish with a footnote.[^1]

[^1]: I'm a footnote!
```

Now we can typeset this with Pandoc. Assuming you're in the same directory just
type:

`pandoc -o simple.pdf input.md`

Pandoc will use whatever parameter that is not flagged as input, so we could
also have multiple input files:

`pandoc -o simple.pdf input1.md input2.md`

What's great about Pandoc is that it supports templating. That means we can pass
variables and change the output.  
So instead of using the US letter paper size, we want to use A4 here in Europe,
so let's change that. To get the desired A4 paper size we just type:

`pandoc -V papersize:a4paper -o simple.pdf input.md`

Some of the template variables are described [here][temp]. You can also see all
template variables for LaTeX by typing `pandoc -D latex`. The default LaTeX
template file is also available on [GitHub][ptemp].

You can see the generated PDF document [here][doc].

**Pro tip:** say you need to just load a single or few LaTeX packages, you can
just include it in the document using YAML front matter.

```
---
header-includes:
  - \usepackage{cleveref}
---
# Section
![A Markdown logo\label{markdown}](markdown.png)

See \cref{markdown}.
```

Here we include the `cleveref` package to set a reference to the Markdown
figure.

## Writing reports
What's great about LaTex is the modularity. You can keep your sections in
individual files, making it easier to locate and change a section.

I've written a template that I hope makes it easier to setup new reports using
Pandoc. I'll keep it updated as I use it myself and refine it. It is available
on [GitHub][template].

I have included three sample directories with some chapters and sections. You
can use them to typeset the project and see if everything is setup correctly.  
My plan is to update the sample files with examples of how to use LaTeX in
Markdown. References, bibliographies, appendix etc. I will maybe also introduce
a proper build tool such as `make` instead of the simple Bash script.

For now just remove and replace with your own content. Be sure to update
`typeset` with your own input files in the `FILES` variable. Update `cover.tex`
with your own name(s).

The template contains most of the used packages but is made with XeTeX in mind!
You'll have to edit for pdfLaTeX or LuaTeX.

## Other use cases
What's _really_ great about this setup compared to writing in LaTeX is Pandoc.
Without too many changes the same project can be exported to websites, or even
ebooks, using your own stylesheets.

There is a whole list of different [examples][examples] with Pandoc. I really
recommend taking some time getting to learn this tool. It has so many use cases
and is unbelievable for text conversion.


[pandoc]:   http://pandoc.org/
[mark]:     http://commonmark.org/
[win]:      http://miktex.org/
[osx]:      https://tug.org/mactex/
[linux]:    http://latex-project.org/
[temp]:     http://pandoc.org/demo/example9/templates.html
[ptemp]:    https://github.com/jgm/pandoc-templates/blob/master/default.latex
[doc]:      /examples/simple.pdf
[template]: https://github.com/lauritzsh/pandoc-markdown-template
[examples]: http://pandoc.org/demos.html
