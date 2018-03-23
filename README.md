# asciidoc-chunker
Splits a single HTML file generated by Asciidoctor into chunks by chapters.


*Note: This is a temporary implementation and still needs to be improved with respect to the speed.*


## What it Dose

Asciidoc-Chunker generates chunked HTML from a single HTML generated by Asciidoctor.

1. Splits part preambles and chapters into separate files.
1. Places footnotes into the file where they are defined.
1. Re-writes the relative links to point the chunked files.
1. Copies the local images whose `src` is the relative path, to the directory relative to the chuned html output.  The images have to be reachable from the asciidoc-chunker invocation directory.


## How to Install

Asciidoc-Chunker is written in Common Lisp.  The easiest way to install Lisp environment is to use [Roswell](https://github.com/roswell/roswell).

1. Follow [the instruction here](https://github.com/roswell/roswell/wiki/Installation) and install Roswell.
1. Invoke `ros setup` after installation.
1. Put asciidoc-chunker folder under `~/common-lisp` directory.
1. Run the script `~/common-lisp/asciidoc-chunker/roswell/asciidoc-chunker.ros` as follows.

```
$ asciidoc-chunker.ros <single-html-file> -o <output-directory>
```

If output directory is not specified, the default is `output` under the current directory.

## Example

You can generate the chunked html for the Asciidoctor User Manual
under `test/output/html_chunk/` directory.

```
$ cd test
$ make
```

Simply open `test/output/html_chunk/index.html` after generation with your browser.


